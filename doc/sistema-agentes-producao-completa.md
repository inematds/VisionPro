# Sistema de Agentes para Producao Audiovisual Completa

Sistema que gera o **produto final completo** - nao so os prompts, mas as imagens, videos, audio, tudo renderizado e pronto.

## Arquitetura Completa

```
+------------------------------------------------------------------+
|                      ENTRADA DO USUARIO                          |
|         "Serie animada: menina 8 anos + raposa magica"           |
+------------------------------------------------------------------+
                              |
+------------------------------------------------------------------+
|                    AGENTE ORQUESTRADOR                           |
|  - Interpreta briefing                                           |
|  - Define pipeline de producao                                   |
|  - Coordena todos os outros agentes                              |
|  - Gerencia dependencias entre tarefas                           |
+------------------------------------------------------------------+
                              |
        +---------------------+---------------------+
        |                     |                     |
+---------------+    +---------------+    +---------------+
|    AGENTE     |    |    AGENTE     |    |    AGENTE     |
|   ROTEIRISTA  |    |   DIRETOR     |    |  COMPOSITOR   |
|               |    |    DE ARTE    |    |   MUSICAL     |
|               |    |               |    |               |
| - Arco        |    | - Style guide |    | - Tema        |
| - Episodios   |    | - Paleta      |    | - Trilhas     |
| - Dialogos    |    | - Referencias |    | - Efeitos     |
+---------------+    +---------------+    +---------------+
        |                     |                     |
        +---------------------+---------------------+
                              |
+------------------------------------------------------------------+
|                    AGENTES DE PRODUCAO                           |
+------------------+------------------+----------------------------+
|  AGENTE VISUAL   |  AGENTE VIDEO    |     AGENTE AUDIO           |
|                  |                  |                            |
| Midjourney API   |  Runway ML API   |  Suno API (musica)         |
| DALL-E API       |  Kling API       |  ElevenLabs (voz)          |
| Flux API         |  Pika API        |  AudioGen (SFX)            |
|                  |                  |                            |
| -> Personagens   | -> Animacoes     |  -> Trilha sonora          |
| -> Cenarios      | -> Transicoes    |  -> Narracao               |
| -> Props         | -> Movimentos    |  -> Efeitos sonoros        |
+------------------+------------------+----------------------------+
                              |
+------------------------------------------------------------------+
|                    AGENTE EDITOR/COMPOSITOR                      |
|                                                                  |
|  - Recebe todos os assets gerados                                |
|  - Monta timeline via FFmpeg/Remotion                            |
|  - Sincroniza audio + video                                      |
|  - Aplica correcao de cor                                        |
|  - Renderiza versao final                                        |
+------------------------------------------------------------------+
                              |
+------------------------------------------------------------------+
|                       PRODUTO FINAL                              |
|                                                                  |
|  projeto-luna-pip/                                               |
|  +-- episodio-01.mp4                                             |
|  +-- episodio-02.mp4                                             |
|  +-- assets/                                                     |
|  |   +-- personagens/                                            |
|  |   +-- cenarios/                                               |
|  |   +-- audio/                                                  |
|  +-- projeto.json (metadados)                                    |
+------------------------------------------------------------------+
```

## Stack Tecnica Recomendada

### Camada de Agentes
- CrewAI / LangGraph / AutoGen
- Orquestracao e comunicacao entre agentes

### Camada de LLM
- Claude API (roteiro, decisoes criativas)
- GPT-4 (backup, tarefas especificas)

### Camada de Geracao
- Replicate (Flux, SDXL, video models)
- fal.ai (geracao rapida de imagens)
- Runway ML API (video)
- Suno API (musica)
- ElevenLabs API (voz)

### Camada de Pos-Producao
- FFmpeg (composicao, encoding)
- Remotion (timeline programatica)
- Sharp (processamento de imagem)

### Camada de Storage
- S3/Cloudflare R2 (assets)
- PostgreSQL (metadados, estado)
- Redis (filas, cache)

## Fluxo de Dados Detalhado

### FASE 1: CONCEITUACAO (Agente Orquestrador + Roteirista)

Input: "serie animada infantil, menina + raposa magica"

```json
{
  "titulo": "Luna e Pip",
  "episodios": 5,
  "duracao_ep": "2min",
  "personagens": [
    {"nome": "Luna", "idade": 8, "personalidade": "curiosa"},
    {"nome": "Pip", "tipo": "raposa", "poder": "muda cor por emocao"}
  ],
  "estilo": "watercolor 2D, Studio Ghibli",
  "tom": "aventura gentil, educativo"
}
```

### FASE 2: PRE-PRODUCAO (Agente Diretor de Arte)

```json
{
  "style_guide": {
    "paleta": ["#FFE4B5", "#87CEEB", "#98FB98"],
    "referencias": ["totoro", "ponyo"],
    "proporcoes": "cabeca grande, olhos expressivos"
  },
  "prompts_base": {
    "luna": "8 year old girl, brown hair in pigtails...",
    "pip": "small magical fox, fluffy tail..."
  }
}
```

### FASE 3: GERACAO DE ASSETS (Agentes de Producao)

[Paralelo]
- Midjourney/Flux -> 50 imagens de personagens
- Midjourney/Flux -> 30 cenarios
- Suno -> 5 trilhas musicais
- ElevenLabs -> vozes dos personagens

### FASE 4: ANIMACAO (Agente de Video)

[Sequencial por cena]
- Runway/Kling -> animar personagem em cenario
- Sincronizar com audio
- Gerar transicoes

### FASE 5: COMPOSICAO FINAL (Agente Editor)

FFmpeg pipeline:
- Juntar cenas na ordem do roteiro
- Adicionar trilha sonora
- Mixar efeitos sonoros
- Aplicar color grading
- Exportar MP4 final

## Estimativa de Custos por Episodio (2 min)

| Recurso | Quantidade | Custo Est. |
|---------|------------|------------|
| Claude API | ~50k tokens | $1.50 |
| Imagens (Flux/MJ) | ~30 | $3.00 |
| Video (Runway) | ~20 clips | $10.00 |
| Musica (Suno) | 2-3 tracks | $0.50 |
| Voz (ElevenLabs) | ~500 chars | $0.30 |
| **Total/episodio** | | **~$15-20** |
| **Serie 5 eps** | | **~$75-100** |

## Implementacao Pratica

### Opcao 1: Python + CrewAI

```python
# Estrutura basica
from crewai import Agent, Task, Crew, Process

class StudioAI:
    def __init__(self):
        self.orquestrador = Agent(
            role="Diretor de Producao",
            goal="Coordenar criacao de serie animada",
            backstory="Experiente em producao audiovisual com IA"
        )
        self.roteirista = Agent(...)
        self.diretor_arte = Agent(...)
        self.compositor = Agent(...)

    def produzir(self, briefing: str) -> dict:
        # Pipeline de producao
        ...
```

### Opcao 2: n8n + Microservicos

```
n8n workflow principal
    +-- Webhook: recebe briefing
    +-- Claude: gera estrutura
    +-- Sub-workflow: gerar imagens
    +-- Sub-workflow: gerar videos
    +-- Sub-workflow: gerar audio
    +-- Webhook: servico FFmpeg
    +-- Output: video final + assets
```

## Desafios Reais

### Consistencia Visual
- Problema: Manter personagem igual em todas as cenas
- Solucao: usar mesma seed, reference images, LoRA

### Tempo de Geracao
- Problema: Video demora (Runway ~2-5min por clip)
- Solucao: paralelizar, usar filas

### Sincronizacao
- Problema: Labios com fala, acao com musica
- Solucao: gerar audio primeiro, usar como guia

### Qualidade Variavel
- Problema: APIs nem sempre geram o esperado
- Solucao: sistema de retry, selecao automatica

## Resultado Final

```
INPUT:  "Serie infantil sobre uma menina e sua raposa magica, 5 episodios"

OUTPUT: luna-e-pip/
        +-- ep01-o-encontro.mp4
        +-- ep02-a-floresta-encantada.mp4
        +-- ep03-o-medo-do-escuro.mp4
        +-- ep04-novos-amigos.mp4
        +-- ep05-o-arco-iris.mp4
```

## Status de Viabilidade

| Aspecto | Status |
|---------|--------|
| APIs disponiveis | OK - Todas existem |
| Qualidade | OK - Boa para conteudo digital |
| Custo | OK - ~$15-20 por minuto de video |
| Automacao total | 80-90% (precisa revisao humana) |
