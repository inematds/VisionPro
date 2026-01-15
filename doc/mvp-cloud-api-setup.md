# MVP com APIs na Nuvem - Setup Completo

Guia para rodar o sistema de producao audiovisual usando **APIs de terceiros** (mais simples, sem GPU local).

## Vantagens vs Local

| Aspecto | Local (Docker) | Cloud (APIs) |
|---------|----------------|--------------|
| GPU necessaria | RTX 3060+ (12GB+) | Nenhuma |
| Setup inicial | ~2 horas | ~30 min |
| Custo fixo | Hardware ~$500-2000 | $0 |
| Custo por video | ~$0.50 (eletricidade) | ~$5-15 |
| Qualidade | Boa | Excelente |
| Velocidade | Media | Rapida |
| Manutencao | Alta | Baixa |

**Recomendado para:** Prototipagem rapida, baixo volume, quem nao tem GPU.

---

## APIs Utilizadas

| Servico | Funcao | Custo Estimado |
|---------|--------|----------------|
| **Kling AI** | Video (I2V, T2V) | ~$0.10/5s video |
| **fal.ai** | Imagens (Flux) | ~$0.01/imagem |
| **Suno** | Musica | ~$0.05/musica |
| **ElevenLabs** | Voz/Narracao | ~$0.01/100 chars |
| **Claude/OpenAI** | Roteiro (LLM) | ~$0.02/roteiro |

### Custo Total por Video de 30s: **~$3-5**

---

## Criando Contas e API Keys

### 1. Kling AI (Video)

```
URL: https://klingai.com
Plano: Creator ($9.90/mes - 660 creditos)

1. Criar conta em https://klingai.com
2. Ir em Settings > API
3. Gerar API Key
4. Guardar: KLING_API_KEY=sk-xxxx
```

**Alternativas:**
- **Runway ML**: https://runwayml.com ($15/mes)
- **Minimax/Hailuo**: https://hailuoai.video (mais barato)
- **Luma Dream Machine**: https://lumalabs.ai

### 2. fal.ai (Imagens)

```
URL: https://fal.ai
Plano: Pay-as-you-go ($0.01/imagem)

1. Criar conta em https://fal.ai
2. Dashboard > API Keys
3. Create new key
4. Guardar: FAL_API_KEY=xxxx
```

**Alternativas:**
- **Replicate**: https://replicate.com
- **Together AI**: https://together.ai

### 3. Suno (Musica)

```
URL: https://suno.ai
Plano: Pro ($10/mes - 500 creditos)

1. Criar conta em https://suno.ai
2. Settings > API (se disponivel) ou usar via Replicate
3. Guardar: SUNO_API_KEY=xxxx

Alternativa via Replicate:
- Modelo: suno-ai/bark ou facebook/musicgen
```

### 4. ElevenLabs (Voz)

```
URL: https://elevenlabs.io
Plano: Starter ($5/mes - 30k chars)

1. Criar conta em https://elevenlabs.io
2. Profile > API Key
3. Guardar: ELEVENLABS_API_KEY=xxxx
```

**Alternativas:**
- **PlayHT**: https://play.ht
- **Murf AI**: https://murf.ai

### 5. Claude ou OpenAI (LLM)

```
Claude:
URL: https://console.anthropic.com
1. Criar conta
2. API Keys > Create Key
3. Guardar: ANTHROPIC_API_KEY=sk-ant-xxxx

OpenAI:
URL: https://platform.openai.com
1. Criar conta
2. API Keys > Create new secret key
3. Guardar: OPENAI_API_KEY=sk-xxxx
```

---

## Estrutura do Projeto

```
studio-ai-cloud/
├── .env                    # API Keys
├── docker-compose.yml      # Redis + Postgres
├── requirements.txt
├── main.py                 # Orquestrador
├── agents/
│   ├── __init__.py
│   ├── director.py         # LLM
│   ├── visual.py           # fal.ai
│   ├── video.py            # Kling
│   ├── voice.py            # ElevenLabs
│   ├── music.py            # Suno
│   └── composer.py         # FFmpeg
├── outputs/
└── projects/
```

---

## Arquivo de Configuracao

```bash
# .env
# LLM
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxx
# ou
OPENAI_API_KEY=sk-xxxxxxxxxxxx

# Imagem
FAL_API_KEY=xxxxxxxxxxxx

# Video
KLING_API_KEY=sk-xxxxxxxxxxxx
# ou alternativas:
RUNWAY_API_KEY=xxxxxxxxxxxx
MINIMAX_API_KEY=xxxxxxxxxxxx

# Audio
ELEVENLABS_API_KEY=xxxxxxxxxxxx
SUNO_API_KEY=xxxxxxxxxxxx

# Infra
REDIS_URL=redis://localhost:6379
DATABASE_URL=postgresql://studio:studio@localhost:5432/studio
```

---

## Docker Compose (Minimo)

```yaml
# docker-compose.yml
version: '3.8'

services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data

  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=studio
      - POSTGRES_PASSWORD=studio
      - POSTGRES_DB=studio
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  redis-data:
  postgres-data:
```

---

## Codigo do Orquestrador

### requirements.txt

```txt
fastapi==0.109.0
uvicorn==0.27.0
httpx==0.26.0
anthropic==0.18.0
openai==1.12.0
fal-client==0.3.0
elevenlabs==0.2.0
redis==5.0.1
sqlalchemy==2.0.25
psycopg2-binary==2.9.9
pydantic==2.5.3
python-dotenv==1.0.0
aiofiles==23.2.1
```

### main.py

```python
# main.py
from fastapi import FastAPI, BackgroundTasks
from pydantic import BaseModel
from typing import Optional
from dotenv import load_dotenv
import os
import asyncio
import uuid

from agents.director import DirectorAgent
from agents.visual import VisualAgent
from agents.video import VideoAgent
from agents.voice import VoiceAgent
from agents.music import MusicAgent
from agents.composer import ComposerAgent

load_dotenv()

app = FastAPI(title="Studio AI Cloud Orchestrator")

# Inicializar agentes
director = DirectorAgent()
visual = VisualAgent()
video = VideoAgent()
voice = VoiceAgent()
music = MusicAgent()
composer = ComposerAgent()


class ProjectRequest(BaseModel):
    briefing: str
    style: Optional[str] = "watercolor 2D animation, Studio Ghibli style"
    duration_seconds: Optional[int] = 30


async def run_pipeline(project_id: str, request: ProjectRequest):
    """Pipeline de producao com APIs na nuvem"""

    output_dir = f"outputs/{project_id}"
    os.makedirs(output_dir, exist_ok=True)

    try:
        # 1. Gerar roteiro (Claude/OpenAI)
        print(f"[{project_id}] Gerando roteiro...")
        script = await director.generate_script(
            request.briefing,
            request.style,
            request.duration_seconds
        )

        # 2. Gerar Element Library (fal.ai)
        print(f"[{project_id}] Gerando imagens dos personagens...")
        character_images = {}
        for char in script["personagens"]:
            img_path = await visual.generate_image(
                char["prompt_visual"],
                f"{output_dir}/char_{char['nome']}.png"
            )
            character_images[char["nome"]] = img_path

        # 3. Gerar musica em paralelo (Suno)
        print(f"[{project_id}] Gerando trilha sonora...")
        music_task = asyncio.create_task(
            music.generate(
                script["trilha_sonora"],
                request.duration_seconds,
                f"{output_dir}/music.mp3"
            )
        )

        # 4. Gerar cenas
        scene_videos = []
        for i, cena in enumerate(script["cenas"]):
            print(f"[{project_id}] Gerando cena {i+1}/{len(script['cenas'])}...")

            # Gerar frame base da cena (fal.ai)
            frame_path = await visual.generate_image(
                cena["prompt_visual"],
                f"{output_dir}/scene_{i+1}_frame.png"
            )

            # Animar frame (Kling)
            video_path = await video.animate(
                frame_path,
                cena["prompt_motion"],
                cena["duracao_seg"],
                f"{output_dir}/scene_{i+1}.mp4"
            )
            scene_videos.append(video_path)

            # Gerar narracao se houver (ElevenLabs)
            if cena.get("narracao"):
                await voice.generate(
                    cena["narracao"],
                    f"{output_dir}/scene_{i+1}_voice.mp3"
                )

        # 5. Aguardar musica
        music_path = await music_task

        # 6. Compor video final (FFmpeg)
        print(f"[{project_id}] Compondo video final...")
        final_path = await composer.compose(
            scene_videos,
            music_path,
            f"{output_dir}/voice",  # pasta com audios
            f"{output_dir}/final.mp4"
        )

        print(f"[{project_id}] CONCLUIDO: {final_path}")

        return {
            "status": "completed",
            "output": final_path,
            "script": script
        }

    except Exception as e:
        print(f"[{project_id}] ERRO: {e}")
        return {
            "status": "error",
            "error": str(e)
        }


@app.post("/projects")
async def create_project(request: ProjectRequest, background_tasks: BackgroundTasks):
    project_id = str(uuid.uuid4())[:8]
    background_tasks.add_task(run_pipeline, project_id, request)

    return {
        "project_id": project_id,
        "status": "started"
    }


@app.get("/")
async def root():
    return {"status": "Studio AI Cloud running"}
```

### agents/director.py (LLM)

```python
# agents/director.py
import os
from anthropic import Anthropic
import json

class DirectorAgent:
    def __init__(self):
        self.client = Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

    async def generate_script(self, briefing: str, style: str, duration: int) -> dict:
        prompt = f"""Voce e um diretor de animacao. Crie um roteiro para um video de {duration} segundos.

BRIEFING: {briefing}
ESTILO VISUAL: {style}

Responda APENAS com JSON valido nesta estrutura:
{{
    "titulo": "nome do projeto",
    "sinopse": "uma frase descrevendo a historia",
    "personagens": [
        {{
            "nome": "Nome",
            "descricao": "descricao do personagem",
            "prompt_visual": "prompt detalhado para gerar imagem do personagem, {style}, character sheet, multiple angles, white background"
        }}
    ],
    "cenas": [
        {{
            "numero": 1,
            "duracao_seg": 5,
            "descricao": "o que acontece na cena",
            "prompt_visual": "prompt para gerar frame da cena, {style}",
            "prompt_motion": "descricao do movimento para animacao",
            "narracao": "texto para narracao (opcional)"
        }}
    ],
    "trilha_sonora": "descricao do estilo musical desejado"
}}"""

        response = self.client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=2000,
            messages=[{"role": "user", "content": prompt}]
        )

        # Extrair JSON da resposta
        text = response.content[0].text
        # Limpar markdown se houver
        if "```json" in text:
            text = text.split("```json")[1].split("```")[0]
        elif "```" in text:
            text = text.split("```")[1].split("```")[0]

        return json.loads(text)
```

### agents/visual.py (fal.ai)

```python
# agents/visual.py
import os
import fal_client
import httpx

class VisualAgent:
    def __init__(self):
        os.environ["FAL_KEY"] = os.getenv("FAL_API_KEY")

    async def generate_image(self, prompt: str, output_path: str) -> str:
        """Gera imagem usando Flux via fal.ai"""

        result = fal_client.subscribe(
            "fal-ai/flux/schnell",
            arguments={
                "prompt": prompt,
                "image_size": "landscape_16_9",
                "num_images": 1,
                "enable_safety_checker": False
            }
        )

        # Baixar imagem
        image_url = result["images"][0]["url"]
        async with httpx.AsyncClient() as client:
            response = await client.get(image_url)
            with open(output_path, "wb") as f:
                f.write(response.content)

        return output_path
```

### agents/video.py (Kling)

```python
# agents/video.py
import os
import httpx
import asyncio
import base64

class VideoAgent:
    def __init__(self):
        self.api_key = os.getenv("KLING_API_KEY")
        self.base_url = "https://api.klingai.com/v1"

    async def animate(self, image_path: str, motion_prompt: str, duration: int, output_path: str) -> str:
        """Anima imagem usando Kling AI"""

        # Ler imagem e converter para base64
        with open(image_path, "rb") as f:
            image_base64 = base64.b64encode(f.read()).decode()

        async with httpx.AsyncClient(timeout=600.0) as client:
            # Criar tarefa de geracao
            response = await client.post(
                f"{self.base_url}/videos/image2video",
                headers={
                    "Authorization": f"Bearer {self.api_key}",
                    "Content-Type": "application/json"
                },
                json={
                    "image": image_base64,
                    "prompt": motion_prompt,
                    "duration": str(min(duration, 5)),  # Kling max 5s por clip
                    "cfg_scale": 0.5,
                    "mode": "std"  # ou "pro" para maior qualidade
                }
            )

            task_id = response.json()["data"]["task_id"]

            # Aguardar conclusao
            while True:
                status_response = await client.get(
                    f"{self.base_url}/videos/image2video/{task_id}",
                    headers={"Authorization": f"Bearer {self.api_key}"}
                )

                status = status_response.json()
                if status["data"]["task_status"] == "succeed":
                    video_url = status["data"]["task_result"]["videos"][0]["url"]
                    break
                elif status["data"]["task_status"] == "failed":
                    raise Exception(f"Kling error: {status}")

                await asyncio.sleep(5)

            # Baixar video
            video_response = await client.get(video_url)
            with open(output_path, "wb") as f:
                f.write(video_response.content)

        return output_path
```

### agents/voice.py (ElevenLabs)

```python
# agents/voice.py
import os
from elevenlabs import generate, save, set_api_key

class VoiceAgent:
    def __init__(self):
        set_api_key(os.getenv("ELEVENLABS_API_KEY"))

    async def generate(self, text: str, output_path: str, voice: str = "Rachel") -> str:
        """Gera narracao usando ElevenLabs"""

        audio = generate(
            text=text,
            voice=voice,
            model="eleven_multilingual_v2"
        )

        save(audio, output_path)
        return output_path
```

### agents/music.py (Suno via Replicate)

```python
# agents/music.py
import os
import httpx
import asyncio

class MusicAgent:
    def __init__(self):
        self.api_key = os.getenv("REPLICATE_API_KEY")  # Usando Replicate para MusicGen

    async def generate(self, description: str, duration: int, output_path: str) -> str:
        """Gera musica usando MusicGen via Replicate"""

        async with httpx.AsyncClient(timeout=300.0) as client:
            # Criar predicao
            response = await client.post(
                "https://api.replicate.com/v1/predictions",
                headers={
                    "Authorization": f"Token {self.api_key}",
                    "Content-Type": "application/json"
                },
                json={
                    "version": "b05b1dff1d8c6dc63d14b0cdb42135378dcb87f6373b0d3d341ede46e59e2b38",
                    "input": {
                        "prompt": description,
                        "duration": min(duration, 30),
                        "model_version": "stereo-large"
                    }
                }
            )

            prediction_url = response.json()["urls"]["get"]

            # Aguardar conclusao
            while True:
                status_response = await client.get(
                    prediction_url,
                    headers={"Authorization": f"Token {self.api_key}"}
                )

                status = status_response.json()
                if status["status"] == "succeeded":
                    audio_url = status["output"]
                    break
                elif status["status"] == "failed":
                    raise Exception(f"MusicGen error: {status}")

                await asyncio.sleep(3)

            # Baixar audio
            audio_response = await client.get(audio_url)
            with open(output_path, "wb") as f:
                f.write(audio_response.content)

        return output_path
```

### agents/composer.py (FFmpeg)

```python
# agents/composer.py
import asyncio
import os

class ComposerAgent:
    async def compose(
        self,
        video_paths: list,
        music_path: str,
        voice_dir: str,
        output_path: str
    ) -> str:
        """Compoe video final com FFmpeg"""

        # 1. Concatenar videos
        concat_list = "concat_list.txt"
        with open(concat_list, "w") as f:
            for vp in video_paths:
                f.write(f"file '{vp}'\n")

        concat_video = output_path.replace(".mp4", "_concat.mp4")
        await self._run_ffmpeg(
            f"ffmpeg -y -f concat -safe 0 -i {concat_list} -c copy {concat_video}"
        )

        # 2. Mixar audio (musica + vozes)
        voice_files = sorted([
            os.path.join(voice_dir, f)
            for f in os.listdir(voice_dir)
            if f.endswith(".mp3")
        ])

        if voice_files:
            # Concatenar vozes
            voice_concat = output_path.replace(".mp4", "_voices.mp3")
            voice_list = "voice_list.txt"
            with open(voice_list, "w") as f:
                for vf in voice_files:
                    f.write(f"file '{vf}'\n")

            await self._run_ffmpeg(
                f"ffmpeg -y -f concat -safe 0 -i {voice_list} -c copy {voice_concat}"
            )

            # Mixar com musica
            await self._run_ffmpeg(f"""ffmpeg -y \
                -i {concat_video} \
                -i {voice_concat} \
                -i {music_path} \
                -filter_complex "[1:a]volume=1.0[voice];[2:a]volume=0.3[music];[voice][music]amix=inputs=2:duration=longest[a]" \
                -map 0:v -map "[a]" \
                -c:v copy -c:a aac -shortest \
                {output_path}""")
        else:
            # Apenas musica
            await self._run_ffmpeg(f"""ffmpeg -y \
                -i {concat_video} \
                -i {music_path} \
                -filter_complex "[1:a]volume=0.5[music]" \
                -map 0:v -map "[music]" \
                -c:v copy -c:a aac -shortest \
                {output_path}""")

        # Cleanup
        os.remove(concat_list)
        os.remove(concat_video)

        return output_path

    async def _run_ffmpeg(self, cmd: str):
        process = await asyncio.create_subprocess_shell(
            cmd,
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE
        )
        stdout, stderr = await process.communicate()
        if process.returncode != 0:
            raise Exception(f"FFmpeg error: {stderr.decode()}")
```

---

## Executando

### 1. Setup

```bash
# Criar pasta
mkdir studio-ai-cloud && cd studio-ai-cloud

# Criar ambiente virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou: venv\Scripts\activate  # Windows

# Instalar dependencias
pip install -r requirements.txt

# Configurar .env com suas API keys
cp .env.example .env
nano .env
```

### 2. Iniciar Servicos

```bash
# Iniciar Redis e Postgres
docker compose up -d

# Iniciar orquestrador
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 3. Criar Projeto

```bash
curl -X POST http://localhost:8000/projects \
  -H "Content-Type: application/json" \
  -d '{
    "briefing": "Luna, uma menina de 8 anos, encontra Pip, uma raposinha magica que muda de cor conforme suas emocoes",
    "style": "watercolor 2D animation, Studio Ghibli style, soft colors",
    "duration_seconds": 30
  }'
```

---

## Estimativa de Custos por Projeto

### Video de 30 segundos (6 cenas de 5s):

| Servico | Quantidade | Custo |
|---------|------------|-------|
| Claude (roteiro) | 1 call | $0.02 |
| fal.ai (imagens) | 8 imagens | $0.08 |
| Kling (video) | 6 clips | $0.60 |
| ElevenLabs (voz) | ~500 chars | $0.05 |
| Suno (musica) | 1 track | $0.10 |
| **TOTAL** | | **~$0.85** |

### Video de 2 minutos (Serie animada):

| | Custo |
|--|-------|
| Por episodio | ~$3-5 |
| Serie 5 eps | ~$15-25 |

---

## Alternativas de APIs

### Video (alternativas ao Kling)

```python
# Runway ML
RUNWAY_API_KEY=xxxx
# Endpoint: https://api.runwayml.com/v1

# Minimax (Hailuo)
MINIMAX_API_KEY=xxxx
# Endpoint: https://api.minimax.chat/v1

# Luma Dream Machine
LUMA_API_KEY=xxxx
# Endpoint: https://api.lumalabs.ai/v1
```

### Imagem (alternativas ao fal.ai)

```python
# Replicate
REPLICATE_API_KEY=xxxx
# Modelo: black-forest-labs/flux-schnell

# Together AI
TOGETHER_API_KEY=xxxx
# Modelo: black-forest-labs/FLUX.1-schnell
```

---

## Fontes

- [Kling AI API](https://klingai.com)
- [fal.ai Flux](https://fal.ai/models/fal-ai/flux)
- [ElevenLabs API](https://elevenlabs.io/docs/api-reference)
- [Replicate MusicGen](https://replicate.com/facebook/musicgen)
- [Anthropic Claude API](https://docs.anthropic.com)
