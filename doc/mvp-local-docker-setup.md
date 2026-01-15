# MVP Local com Docker - Setup Completo

Guia para rodar o sistema de producao audiovisual com IA **100% local** usando Docker.

## Requisitos de Hardware

### Minimo (Funcional)

```
CPU: 8 cores
RAM: 32 GB
GPU: NVIDIA RTX 3060 12GB
Storage: 200GB SSD
OS: Ubuntu 22.04 / Windows 11 WSL2
```

### Recomendado (Producao)

```
CPU: 16 cores
RAM: 64 GB
GPU: NVIDIA RTX 4090 24GB (ou A6000 48GB)
Storage: 500GB NVMe SSD
OS: Ubuntu 22.04
```

### Mapeamento GPU vs Capacidade

| GPU | VRAM | Video | Imagem | Audio | Observacao |
|-----|------|-------|--------|-------|------------|
| RTX 3060 | 12GB | Wan 1.3B | Flux Schnell | OK | Minimo viavel |
| RTX 3080 | 10GB | Wan 1.3B | Flux Schnell | OK | VRAM limitada |
| RTX 3090 | 24GB | Wan 14B fp8 | Flux Dev | OK | Bom custo-beneficio |
| RTX 4090 | 24GB | Wan 14B fp8 | Flux Dev | OK | Melhor performance |
| A6000 | 48GB | Wan 14B fp16 | Qualquer | OK | Producao profissional |

---

## Arquitetura do Sistema

```
+------------------------------------------------------------------+
|                        DOCKER COMPOSE                            |
+------------------------------------------------------------------+
|                                                                  |
|  +------------------+  +------------------+  +------------------+ |
|  |   ORQUESTRADOR   |  |   COMFYUI        |  |    OLLAMA        | |
|  |   (Python)       |  |   + Wan 2.1      |  |    (LLM local)   | |
|  |                  |  |   + Flux         |  |                  | |
|  |   Port: 8000     |  |   Port: 8188     |  |   Port: 11434    | |
|  +------------------+  +------------------+  +------------------+ |
|                                                                  |
|  +------------------+  +------------------+  +------------------+ |
|  |   COQUI TTS      |  |   MUSICGEN       |  |    REDIS         | |
|  |   (XTTS-v2)      |  |   (AudioCraft)   |  |    (Queue)       | |
|  |                  |  |                  |  |                  | |
|  |   Port: 5002     |  |   Port: 7860     |  |   Port: 6379     | |
|  +------------------+  +------------------+  +------------------+ |
|                                                                  |
|  +------------------+  +------------------+                      |
|  |   POSTGRES       |  |   MINIO          |                      |
|  |   (Metadados)    |  |   (Storage S3)   |                      |
|  |                  |  |                  |                      |
|  |   Port: 5432     |  |   Port: 9000     |                      |
|  +------------------+  +------------------+                      |
|                                                                  |
+------------------------------------------------------------------+
|                     VOLUMES PERSISTENTES                         |
|                                                                  |
|  ./models/     - Modelos de IA (Wan, Flux, XTTS, MusicGen)       |
|  ./outputs/    - Videos e assets gerados                         |
|  ./projects/   - Projetos e metadados                            |
|  ./cache/      - Cache HuggingFace                               |
+------------------------------------------------------------------+
```

---

## Estrutura de Pastas

```bash
mkdir -p ~/studio-ai/{models,outputs,projects,cache,config,scripts}
cd ~/studio-ai

# Estrutura final
studio-ai/
├── docker-compose.yml
├── .env
├── config/
│   ├── comfyui/
│   │   └── workflows/
│   └── orchestrator/
├── models/
│   ├── wan/           # ~30GB
│   ├── flux/          # ~23GB
│   ├── xtts/          # ~2GB
│   └── musicgen/      # ~5GB
├── outputs/
├── projects/
├── cache/
└── scripts/
    ├── download-models.sh
    ├── start.sh
    └── stop.sh
```

---

## Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  # ===========================================
  # COMFYUI - Video (Wan 2.1) + Imagem (Flux)
  # ===========================================
  comfyui:
    image: ghcr.io/loan-mgt/comfyui-wan:latest
    container_name: studio-comfyui
    restart: unless-stopped
    ports:
      - "8188:8188"
    volumes:
      - ./models:/app/models
      - ./outputs:/app/output
      - ./config/comfyui:/app/custom_nodes/workflows
      - ./cache:/root/.cache/huggingface
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    networks:
      - studio-network

  # ===========================================
  # OLLAMA - LLM Local (Roteiro, Orquestracao)
  # ===========================================
  ollama:
    image: ollama/ollama:latest
    container_name: studio-ollama
    restart: unless-stopped
    ports:
      - "11434:11434"
    volumes:
      - ./models/ollama:/root/.ollama
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    networks:
      - studio-network

  # ===========================================
  # COQUI TTS - Voz (XTTS-v2)
  # ===========================================
  tts:
    image: ghcr.io/idiap/coqui-tts-gpu
    container_name: studio-tts
    restart: unless-stopped
    ports:
      - "5002:5002"
    volumes:
      - ./models/xtts:/root/.local/share/tts
      - ./outputs/audio:/app/output
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    command: >
      tts-server
      --model_name tts_models/multilingual/multi-dataset/xtts_v2
      --use_cuda true
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    networks:
      - studio-network

  # ===========================================
  # MUSICGEN - Musica (AudioCraft)
  # ===========================================
  musicgen:
    image: gabotechs/musicgpt:latest
    container_name: studio-musicgen
    restart: unless-stopped
    ports:
      - "7860:8642"
    volumes:
      - ./models/musicgen:/root/.local/share/musicgpt
      - ./outputs/music:/app/output
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    command: --gpu --ui-expose
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    networks:
      - studio-network

  # ===========================================
  # ORQUESTRADOR - Python (LangGraph)
  # ===========================================
  orchestrator:
    build:
      context: ./orchestrator
      dockerfile: Dockerfile
    container_name: studio-orchestrator
    restart: unless-stopped
    ports:
      - "8000:8000"
    volumes:
      - ./config/orchestrator:/app/config
      - ./outputs:/app/outputs
      - ./projects:/app/projects
    environment:
      - COMFYUI_URL=http://comfyui:8188
      - OLLAMA_URL=http://ollama:11434
      - TTS_URL=http://tts:5002
      - MUSICGEN_URL=http://musicgen:8642
      - REDIS_URL=redis://redis:6379
      - DATABASE_URL=postgresql://studio:studio@postgres:5432/studio
    depends_on:
      - comfyui
      - ollama
      - tts
      - musicgen
      - redis
      - postgres
    networks:
      - studio-network

  # ===========================================
  # REDIS - Queue de Jobs
  # ===========================================
  redis:
    image: redis:7-alpine
    container_name: studio-redis
    restart: unless-stopped
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - studio-network

  # ===========================================
  # POSTGRES - Metadados
  # ===========================================
  postgres:
    image: postgres:15-alpine
    container_name: studio-postgres
    restart: unless-stopped
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=studio
      - POSTGRES_PASSWORD=studio
      - POSTGRES_DB=studio
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - studio-network

  # ===========================================
  # MINIO - Storage S3 Local
  # ===========================================
  minio:
    image: minio/minio:latest
    container_name: studio-minio
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      - MINIO_ROOT_USER=minioadmin
      - MINIO_ROOT_PASSWORD=minioadmin
    volumes:
      - minio-data:/data
    command: server /data --console-address ":9001"
    networks:
      - studio-network

networks:
  studio-network:
    driver: bridge

volumes:
  redis-data:
  postgres-data:
  minio-data:
```

---

## Script de Download de Modelos

```bash
#!/bin/bash
# scripts/download-models.sh

set -e

MODELS_DIR="./models"
CACHE_DIR="./cache"

echo "=== Baixando modelos para producao local ==="

# Criar diretorios
mkdir -p $MODELS_DIR/{wan,flux,xtts,musicgen,ollama}
mkdir -p $CACHE_DIR

# 1. WAN 2.1 (Video) - ~30GB
echo "[1/5] Baixando Wan 2.1..."
if [ ! -f "$MODELS_DIR/wan/wan2.1_i2v_14b_fp8.safetensors" ]; then
    # Usando huggingface-cli
    huggingface-cli download Comfy-Org/Wan_2.1_ComfyUI_repackaged \
        --local-dir $MODELS_DIR/wan \
        --include "split_files/wan2.1_i2v_14b_fp8/*"
fi

# 2. FLUX Schnell (Imagem) - ~23GB
echo "[2/5] Baixando Flux Schnell..."
if [ ! -f "$MODELS_DIR/flux/flux1-schnell.safetensors" ]; then
    huggingface-cli download black-forest-labs/FLUX.1-schnell \
        --local-dir $MODELS_DIR/flux
fi

# 3. XTTS-v2 (Voz) - ~2GB
echo "[3/5] XTTS sera baixado automaticamente no primeiro uso..."

# 4. MusicGen (Musica) - ~5GB
echo "[4/5] MusicGen sera baixado automaticamente no primeiro uso..."

# 5. Ollama - Qwen2.5 (LLM para roteiro)
echo "[5/5] Baixando modelo LLM..."
docker run --rm -v ./models/ollama:/root/.ollama ollama/ollama pull qwen2.5:14b

echo "=== Download completo! ==="
echo "Espaco usado:"
du -sh $MODELS_DIR/*
```

---

## Dockerfile do Orquestrador

```dockerfile
# orchestrator/Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Instalar dependencias do sistema
RUN apt-get update && apt-get install -y \
    ffmpeg \
    git \
    && rm -rf /var/lib/apt/lists/*

# Copiar requirements
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copiar codigo
COPY . .

# Expor porta
EXPOSE 8000

# Comando
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```txt
# orchestrator/requirements.txt
fastapi==0.109.0
uvicorn==0.27.0
langgraph==0.0.26
langchain==0.1.0
httpx==0.26.0
redis==5.0.1
sqlalchemy==2.0.25
psycopg2-binary==2.9.9
pydantic==2.5.3
python-multipart==0.0.6
aiofiles==23.2.1
pillow==10.2.0
```

---

## Codigo do Orquestrador (MVP)

```python
# orchestrator/main.py
from fastapi import FastAPI, BackgroundTasks
from pydantic import BaseModel
from typing import Optional
import httpx
import asyncio
import json
import os

app = FastAPI(title="Studio AI Orchestrator")

# URLs dos servicos
COMFYUI_URL = os.getenv("COMFYUI_URL", "http://localhost:8188")
OLLAMA_URL = os.getenv("OLLAMA_URL", "http://localhost:11434")
TTS_URL = os.getenv("TTS_URL", "http://localhost:5002")
MUSICGEN_URL = os.getenv("MUSICGEN_URL", "http://localhost:7860")


class ProjectRequest(BaseModel):
    briefing: str
    style: Optional[str] = "watercolor 2D animation"
    duration_seconds: Optional[int] = 30


class ProjectStatus(BaseModel):
    project_id: str
    status: str
    progress: int
    message: str


# ========================================
# AGENTE 1: DIRETOR (LLM)
# ========================================
async def generate_script(briefing: str, style: str) -> dict:
    """Gera roteiro e biblia do projeto usando LLM local"""

    prompt = f"""Voce e um diretor de animacao. Crie um roteiro curto baseado neste briefing:

BRIEFING: {briefing}
ESTILO: {style}

Responda em JSON com esta estrutura:
{{
    "titulo": "nome do projeto",
    "sinopse": "uma frase",
    "personagens": [
        {{"nome": "...", "descricao": "...", "prompt_visual": "..."}}
    ],
    "cenas": [
        {{"numero": 1, "descricao": "...", "duracao_seg": 5, "prompt_video": "...", "audio": "..."}}
    ],
    "trilha_sonora": "descricao do estilo musical"
}}"""

    async with httpx.AsyncClient(timeout=120.0) as client:
        response = await client.post(
            f"{OLLAMA_URL}/api/generate",
            json={
                "model": "qwen2.5:14b",
                "prompt": prompt,
                "stream": False,
                "format": "json"
            }
        )

        result = response.json()
        return json.loads(result["response"])


# ========================================
# AGENTE 2: VISUAL (ComfyUI)
# ========================================
async def generate_image(prompt: str, output_name: str) -> str:
    """Gera imagem usando Flux via ComfyUI"""

    workflow = {
        "prompt": {
            "3": {
                "class_type": "KSampler",
                "inputs": {
                    "seed": 42,
                    "steps": 20,
                    "cfg": 7.5,
                    "sampler_name": "euler",
                    "scheduler": "normal",
                    "denoise": 1,
                    "model": ["4", 0],
                    "positive": ["6", 0],
                    "negative": ["7", 0],
                    "latent_image": ["5", 0]
                }
            },
            # ... workflow completo do Flux
        }
    }

    async with httpx.AsyncClient(timeout=300.0) as client:
        # Envia workflow
        response = await client.post(
            f"{COMFYUI_URL}/prompt",
            json={"prompt": workflow}
        )

        prompt_id = response.json()["prompt_id"]

        # Aguarda conclusao
        while True:
            status = await client.get(f"{COMFYUI_URL}/history/{prompt_id}")
            if prompt_id in status.json():
                break
            await asyncio.sleep(1)

        return f"/outputs/{output_name}.png"


# ========================================
# AGENTE 3: VIDEO (ComfyUI + Wan)
# ========================================
async def generate_video(image_path: str, prompt: str, output_name: str) -> str:
    """Gera video usando Wan 2.1 via ComfyUI (image-to-video)"""

    # Workflow do Wan 2.1 I2V
    workflow = {
        # ... workflow do Wan 2.1
    }

    async with httpx.AsyncClient(timeout=600.0) as client:
        response = await client.post(
            f"{COMFYUI_URL}/prompt",
            json={"prompt": workflow}
        )

        prompt_id = response.json()["prompt_id"]

        # Aguarda (video demora mais)
        while True:
            status = await client.get(f"{COMFYUI_URL}/history/{prompt_id}")
            if prompt_id in status.json():
                break
            await asyncio.sleep(5)

        return f"/outputs/{output_name}.mp4"


# ========================================
# AGENTE 4: AUDIO - VOZ (Coqui TTS)
# ========================================
async def generate_voice(text: str, output_name: str) -> str:
    """Gera narração usando XTTS-v2"""

    async with httpx.AsyncClient(timeout=120.0) as client:
        response = await client.get(
            f"{TTS_URL}/api/tts",
            params={
                "text": text,
                "speaker_id": "pt-br",
                "language_id": "pt"
            }
        )

        # Salva audio
        output_path = f"/outputs/audio/{output_name}.wav"
        with open(output_path, "wb") as f:
            f.write(response.content)

        return output_path


# ========================================
# AGENTE 5: AUDIO - MUSICA (MusicGen)
# ========================================
async def generate_music(description: str, duration: int, output_name: str) -> str:
    """Gera trilha sonora usando MusicGen"""

    async with httpx.AsyncClient(timeout=300.0) as client:
        response = await client.post(
            f"{MUSICGEN_URL}/generate",
            json={
                "prompt": description,
                "duration": duration
            }
        )

        return f"/outputs/music/{output_name}.wav"


# ========================================
# AGENTE 6: COMPOSITOR (FFmpeg)
# ========================================
async def compose_final(video_path: str, voice_path: str, music_path: str, output_name: str) -> str:
    """Compoe video final com FFmpeg"""

    output_path = f"/outputs/final/{output_name}.mp4"

    # Comando FFmpeg para mixar
    cmd = f"""ffmpeg -y \
        -i {video_path} \
        -i {voice_path} \
        -i {music_path} \
        -filter_complex "[1:a]volume=1.0[voice];[2:a]volume=0.3[music];[voice][music]amix=inputs=2:duration=longest[a]" \
        -map 0:v -map "[a]" \
        -c:v copy -c:a aac \
        {output_path}"""

    process = await asyncio.create_subprocess_shell(
        cmd,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE
    )
    await process.communicate()

    return output_path


# ========================================
# PIPELINE PRINCIPAL
# ========================================
async def run_production_pipeline(project_id: str, request: ProjectRequest):
    """Pipeline completo de producao"""

    try:
        # 1. Gerar roteiro
        print(f"[{project_id}] Gerando roteiro...")
        script = await generate_script(request.briefing, request.style)

        # 2. Gerar imagens dos personagens
        print(f"[{project_id}] Gerando imagens...")
        for char in script["personagens"]:
            await generate_image(
                char["prompt_visual"],
                f"{project_id}_char_{char['nome']}"
            )

        # 3. Gerar musica (paralelo)
        print(f"[{project_id}] Gerando trilha sonora...")
        music_task = asyncio.create_task(
            generate_music(
                script["trilha_sonora"],
                request.duration_seconds,
                f"{project_id}_music"
            )
        )

        # 4. Gerar cenas
        videos = []
        for cena in script["cenas"]:
            print(f"[{project_id}] Gerando cena {cena['numero']}...")

            # Gera imagem base da cena
            img_path = await generate_image(
                cena["prompt_video"],
                f"{project_id}_cena_{cena['numero']}_img"
            )

            # Anima a imagem
            video_path = await generate_video(
                img_path,
                cena["prompt_video"],
                f"{project_id}_cena_{cena['numero']}"
            )
            videos.append(video_path)

            # Gera narracao se houver
            if cena.get("audio"):
                await generate_voice(
                    cena["audio"],
                    f"{project_id}_cena_{cena['numero']}_voice"
                )

        # 5. Aguarda musica
        music_path = await music_task

        # 6. Compoe video final
        print(f"[{project_id}] Compondo video final...")
        final_path = await compose_final(
            videos[0],  # simplificado para MVP
            f"/outputs/audio/{project_id}_cena_1_voice.wav",
            music_path,
            f"{project_id}_final"
        )

        print(f"[{project_id}] Producao concluida: {final_path}")
        return final_path

    except Exception as e:
        print(f"[{project_id}] Erro: {e}")
        raise


# ========================================
# ENDPOINTS DA API
# ========================================
@app.get("/")
async def root():
    return {"status": "Studio AI Orchestrator running"}


@app.get("/health")
async def health():
    """Verifica saude dos servicos"""
    services = {}

    async with httpx.AsyncClient(timeout=5.0) as client:
        try:
            await client.get(f"{COMFYUI_URL}/")
            services["comfyui"] = "ok"
        except:
            services["comfyui"] = "error"

        try:
            await client.get(f"{OLLAMA_URL}/")
            services["ollama"] = "ok"
        except:
            services["ollama"] = "error"

        try:
            await client.get(f"{TTS_URL}/")
            services["tts"] = "ok"
        except:
            services["tts"] = "error"

    return services


@app.post("/projects")
async def create_project(request: ProjectRequest, background_tasks: BackgroundTasks):
    """Cria novo projeto de producao"""

    import uuid
    project_id = str(uuid.uuid4())[:8]

    # Executa em background
    background_tasks.add_task(run_production_pipeline, project_id, request)

    return {
        "project_id": project_id,
        "status": "started",
        "message": "Producao iniciada em background"
    }


@app.get("/projects/{project_id}")
async def get_project_status(project_id: str):
    """Retorna status do projeto"""

    # TODO: Implementar tracking real com Redis
    return {
        "project_id": project_id,
        "status": "processing"
    }
```

---

## Scripts de Controle

```bash
#!/bin/bash
# scripts/start.sh

set -e

echo "=== Iniciando Studio AI ==="

# Verificar NVIDIA
if ! nvidia-smi > /dev/null 2>&1; then
    echo "ERRO: NVIDIA driver nao encontrado!"
    exit 1
fi

# Verificar Docker
if ! docker info > /dev/null 2>&1; then
    echo "ERRO: Docker nao esta rodando!"
    exit 1
fi

# Iniciar servicos
docker compose up -d

echo ""
echo "=== Servicos iniciados ==="
echo ""
echo "ComfyUI (Video/Imagem): http://localhost:8188"
echo "Orquestrador API:       http://localhost:8000"
echo "TTS (Voz):              http://localhost:5002"
echo "MusicGen:               http://localhost:7860"
echo "MinIO Console:          http://localhost:9001"
echo ""
echo "Para ver logs: docker compose logs -f"
```

```bash
#!/bin/bash
# scripts/stop.sh

echo "=== Parando Studio AI ==="
docker compose down
echo "Servicos parados."
```

---

## Uso do Sistema

### 1. Primeiro Setup

```bash
# Clonar/criar estrutura
cd ~/studio-ai

# Baixar modelos (demora ~1 hora, ~60GB)
chmod +x scripts/download-models.sh
./scripts/download-models.sh

# Iniciar
chmod +x scripts/start.sh
./scripts/start.sh
```

### 2. Criar um Projeto

```bash
# Via curl
curl -X POST http://localhost:8000/projects \
  -H "Content-Type: application/json" \
  -d '{
    "briefing": "Uma menina de 8 anos chamada Luna encontra uma raposinha magica chamada Pip na floresta",
    "style": "watercolor 2D animation, Studio Ghibli style",
    "duration_seconds": 30
  }'

# Resposta
{
  "project_id": "a1b2c3d4",
  "status": "started",
  "message": "Producao iniciada em background"
}
```

### 3. Verificar Status

```bash
curl http://localhost:8000/projects/a1b2c3d4
```

### 4. Resultado

```
outputs/
└── final/
    └── a1b2c3d4_final.mp4  # Video pronto!
```

---

## Estimativa de Tempo e Recursos

### Para 30 segundos de video:

| Etapa | Tempo (RTX 4090) | Tempo (RTX 3060) |
|-------|------------------|------------------|
| Roteiro (LLM) | ~10s | ~30s |
| Imagens (3x) | ~30s | ~90s |
| Video (Wan) | ~2min | ~8min |
| Voz (TTS) | ~10s | ~30s |
| Musica | ~30s | ~2min |
| Composicao | ~5s | ~10s |
| **TOTAL** | **~3-4 min** | **~12-15 min** |

### Uso de VRAM (pico):

```
ComfyUI (Wan 14B fp8):  ~20GB
ComfyUI (Flux):         ~12GB
Ollama (Qwen 14B):      ~10GB
TTS (XTTS):             ~4GB
MusicGen:               ~6GB

NOTA: Nao rodam simultaneamente, exceto em setup multi-GPU
```

---

## Troubleshooting

### Erro: CUDA out of memory

```bash
# Reduzir modelo do Wan para 1.3B
# Editar docker-compose.yml e usar imagem menor

# Ou limpar cache
docker exec studio-comfyui rm -rf /root/.cache/*
```

### Erro: ComfyUI nao inicia

```bash
# Verificar logs
docker logs studio-comfyui

# Reiniciar com mais tempo
docker compose restart comfyui
sleep 60  # Aguardar carregar modelos
```

### Erro: Modelos nao encontrados

```bash
# Verificar se modelos foram baixados
ls -la models/wan/
ls -la models/flux/

# Re-executar download
./scripts/download-models.sh
```

### Performance lenta

```bash
# Verificar uso de GPU
nvidia-smi -l 1

# Se GPU nao esta sendo usada, verificar Docker
docker info | grep -i runtime
# Deve mostrar: Runtimes: nvidia runc
```

---

## Proximos Passos (Apos MVP)

1. **UI Web** - Interface Streamlit/Gradio
2. **Queue System** - Celery + Redis para multiplos jobs
3. **Element Library** - Persistencia de personagens
4. **Multi-GPU** - Distribuir carga entre GPUs
5. **API Publica** - Rate limiting, auth, billing

---

## Fontes e Referencias

- [ComfyUI Wan Docker](https://github.com/loan-mgt/comfyui-wan)
- [Wan 2.1 GitHub](https://github.com/Wan-Video/Wan2.1)
- [Wan 2.1 ComfyUI Guide](https://comfyui-wiki.com/en/tutorial/advanced/video/wan2.1/wan2-1-video-model)
- [Coqui TTS Docker](https://docs.coqui.ai/en/latest/docker_images.html)
- [openedai-speech](https://github.com/matatonic/openedai-speech)
- [MusicGPT Docker](https://github.com/gabotechs/MusicGPT)
- [AudioCraft](https://github.com/facebookresearch/audiocraft)
- [Flux Official](https://github.com/black-forest-labs/flux)
- [Containerized Flux](https://codingwithcody.com/2025/03/09/containerized-flux/)
