# Sistema de Agentes para Criacao de Cursos

## Conceito

Sistema que recebe um assunto e cria automaticamente todo o conteudo do curso.

```
Input: "Serie animada infantil sobre uma menina e um animal magico"
     |
[Orquestrador] -> Define estrutura, modulos, topicos
     |
[Agente Roteirista] -> Cria narrativa, personagens, arcos
     |
[Agente de Prompts] -> Gera prompts Midjourney/Runway/Suno
     |
[Agente de Assets] -> Organiza HTML, navegacao, estrutura
     |
Output: Trilha completa pronta para usar
```

## Arquiteturas Possiveis

### 1. n8n + Claude API (Mais Simples)

```
Workflow:
- Webhook recebe: assunto + tipo de projeto
- Node Claude: gera estrutura de modulos
- Node Claude: gera conteudo de cada modulo
- Node Code: monta os HTMLs
- Node GitHub: faz commit automatico
```

### 2. CrewAI / LangGraph (Agentes Especializados)

```python
# Exemplo conceitual com CrewAI
crew = Crew(
    agents=[
        Agent("Arquiteto", goal="Definir estrutura do curso"),
        Agent("Roteirista", goal="Criar narrativa e conceitos"),
        Agent("Prompt Engineer", goal="Gerar prompts de IA"),
        Agent("Desenvolvedor", goal="Criar HTMLs finais"),
    ],
    process=Process.sequential
)
```

### 3. Claude Code + Sistema de Templates (Mais Pratico)

```
Criar um comando/skill que:
1. Recebe briefing do usuario
2. Usa templates das trilhas existentes
3. Gera conteudo personalizado
4. Cria arquivos automaticamente
```

## Viabilidade

| Aspecto | Avaliacao |
|---------|-----------|
| Tecnica | Alta - APIs de LLM suportam bem |
| Qualidade | Boa - com revisao humana |
| Custo | Moderado - ~$2-5 por trilha completa |
| Tempo dev | 1-2 semanas para MVP |

## Recomendacao

Comecar com **n8n + Claude API** porque:
- Interface visual para ajustar o fluxo
- Pode testar rapidamente
- Escala facil depois

## Opcoes de Implementacao

1. **Workflow n8n** que gera uma trilha a partir de um briefing
2. **Script Python** que funciona como "gerador de trilhas"
3. **Skill para Claude Code** chamada com `/criar-trilha "assunto"`
