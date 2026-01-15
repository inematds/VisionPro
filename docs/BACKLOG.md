# VisionPro - Backlog de Refinamento

**Ultima atualizacao:** 2026-01-15
**Status:** Pronto para priorizacao

---

## Resumo Executivo

| Prioridade | Epico | Stories | Esforco Estimado |
|------------|-------|---------|------------------|
| P0 | Harmonizacao de Identidade | 3 | Baixo |
| P1 | Estruturacao de Conteudo | 6 | Alto |
| P2 | Enriquecimento Pedagogico | 5 | Medio |
| P2 | Conexoes e Navegacao | 4 | Medio |
| P3 | Documentacao Tecnica | 3 | Baixo |
| **Total** | **5 Epicos** | **21 Stories** | - |

---

## Epico 1: Harmonizacao de Identidade

**Prioridade:** P0 - Critico
**Objetivo:** Unificar identidade do produto em todos os arquivos

### Stories

#### S1.1 - Atualizar arquivos de referencia para VisionPro
**Como** mantenedor do projeto
**Quero** que todos os arquivos em ref/ usem "VisionPro" ao inves de "Dashboard Mastery"
**Para que** a identidade do produto seja consistente

**Criterios de Aceite:**
- [ ] PADRAO_PAGINAS.md atualizado
- [ ] CONTENT_STRUCTURE.md atualizado
- [ ] LAYOUT_REFERENCE.md atualizado
- [ ] FEP_STYLE_REFERENCE.md atualizado
- [ ] Todos os exemplos HTML refletem VisionPro

---

#### S1.2 - Definir cor para trilha Recursos
**Como** designer do sistema
**Quero** que a trilha Recursos tenha cor Amber definida
**Para que** todas as 4 areas tenham identidade visual

**Criterios de Aceite:**
- [ ] Paleta de cores documentada inclui Amber
- [ ] Classes CSS definidas: `text-amber-400`, `bg-amber-500/20`
- [ ] LAYOUT_REFERENCE.md atualizado

---

#### S1.3 - Padronizar numeracao dos capitulos
**Como** aluno do curso
**Quero** numeracao sequencial sem conflitos
**Para que** a navegacao seja clara

**Criterios de Aceite:**
- [ ] Trilha 1: Capitulos 1-8
- [ ] Trilha 2: Capitulos 9-18
- [ ] Trilha 3: Capitulos 19-25
- [ ] Recursos: Blocos A-E (sem numero)
- [ ] Documentos atualizados

---

## Epico 2: Estruturacao de Conteudo

**Prioridade:** P1 - Alto
**Objetivo:** Expandir cada capitulo com estrutura pedagogica completa

### Stories

#### S2.1 - Expandir Trilha 1 com 3 secoes obrigatorias
**Como** aluno iniciante
**Quero** cada capitulo da Trilha 1 com estrutura completa
**Para que** eu entenda claramente cada conceito

**Criterios de Aceite:**
- [ ] Cada capitulo tem secao "O que e"
- [ ] Cada capitulo tem secao "Por que aprender"
- [ ] Cada capitulo tem secao "Conceitos-chave"
- [ ] 8 capitulos atualizados

---

#### S2.2 - Expandir Trilha 2 com 3 secoes obrigatorias
**Como** aluno intermediario
**Quero** cada capitulo da Trilha 2 com estrutura completa
**Para que** eu execute cada etapa com clareza

**Criterios de Aceite:**
- [ ] Cada capitulo tem as 3 secoes
- [ ] Cada capitulo menciona ferramentas de IA especificas
- [ ] 10 capitulos atualizados

---

#### S2.3 - Expandir Trilha 3 com 3 secoes obrigatorias
**Como** aluno avancado
**Quero** cada capitulo da Trilha 3 com estrutura completa e casos praticos
**Para que** eu aplique em contexto real

**Criterios de Aceite:**
- [ ] Cada capitulo tem as 3 secoes
- [ ] Cada capitulo tem pelo menos 1 caso pratico
- [ ] 7 capitulos atualizados

---

#### S2.4 - Expandir Recursos com 3 secoes obrigatorias
**Como** aluno buscando diferencial
**Quero** cada bloco de Recursos com roteiro de laboratorio
**Para que** eu execute os labs de forma autonoma

**Criterios de Aceite:**
- [ ] Cada bloco tem as 3 secoes
- [ ] Cada bloco tem roteiro passo-a-passo
- [ ] 5 blocos atualizados

---

#### S2.5 - Definir ferramentas de IA recomendadas
**Como** aluno da Trilha 2
**Quero** saber quais ferramentas de IA usar em cada etapa
**Para que** eu nao perca tempo pesquisando

**Criterios de Aceite:**
- [ ] Lista de ferramentas por capitulo
- [ ] Categorizacao: gratuita/paga
- [ ] Links oficiais incluidos
- [ ] Alternativas mencionadas

---

#### S2.6 - Criar biblioteca de prompts
**Como** aluno usando IA
**Quero** prompts prontos para cada etapa
**Para que** eu tenha ponto de partida

**Criterios de Aceite:**
- [ ] Minimo 3 prompts por capitulo da Trilha 2
- [ ] Prompts organizados por objetivo
- [ ] Exemplos de output esperado
- [ ] Arquivo separado: `BIBLIOTECA_PROMPTS.md`

---

## Epico 3: Enriquecimento Pedagogico

**Prioridade:** P2 - Medio
**Objetivo:** Adicionar elementos que facilitam aprendizado

### Stories

#### S3.1 - Adicionar estimativas de duracao
**Como** aluno planejando estudos
**Quero** saber quanto tempo cada capitulo leva
**Para que** eu organize minha agenda

**Criterios de Aceite:**
- [ ] Cada capitulo tem duracao estimada
- [ ] Formato: "~XX min"
- [ ] Total por trilha calculado

---

#### S3.2 - Criar exercicios praticos por capitulo
**Como** aluno praticante
**Quero** exercicios para fixar o conteudo
**Para que** eu aprenda fazendo

**Criterios de Aceite:**
- [ ] Minimo 1 exercicio por capitulo
- [ ] Exercicios tem objetivo claro
- [ ] Criterio de conclusao definido

---

#### S3.3 - Desenvolver templates de entrega
**Como** aluno da Trilha 2
**Quero** templates para as entregas de cada capitulo
**Para que** eu saiba o formato esperado

**Criterios de Aceite:**
- [ ] Template para Brief Narrativo
- [ ] Template para Ficha de Personagem
- [ ] Template para Mapa de Cenas
- [ ] Template para Roteiro
- [ ] Templates em formato editavel

---

#### S3.4 - Adicionar estudos de caso
**Como** aluno buscando inspiracao
**Quero** ver exemplos reais de aplicacao
**Para que** eu entenda o resultado esperado

**Criterios de Aceite:**
- [ ] Minimo 1 caso por trilha
- [ ] Casos documentam processo completo
- [ ] Resultados visiveis (imagens/links)

---

#### S3.5 - Criar checklists de progresso
**Como** aluno acompanhando evolucao
**Quero** checklists para cada modulo
**Para que** eu saiba o que ja completei

**Criterios de Aceite:**
- [ ] Checklist por capitulo
- [ ] Itens verificaveis
- [ ] Formato utilizavel em plataforma

---

## Epico 4: Conexoes e Navegacao

**Prioridade:** P2 - Medio
**Objetivo:** Facilitar navegacao entre trilhas

### Stories

#### S4.1 - Mapear pre-requisitos entre trilhas
**Como** aluno planejando jornada
**Quero** diagrama claro de dependencias
**Para que** eu siga a ordem correta

**Criterios de Aceite:**
- [ ] Diagrama visual de dependencias
- [ ] Pre-requisitos por capitulo
- [ ] Caminhos alternativos identificados

---

#### S4.2 - Definir pontos de entrada nos Recursos
**Como** aluno interessado em labs
**Quero** saber quando posso acessar cada lab
**Para que** eu aproveite no momento certo

**Criterios de Aceite:**
- [ ] Cada bloco tem pre-requisito definido
- [ ] Momento recomendado na jornada
- [ ] Conexao com capitulos especificos

---

#### S4.3 - Criar resumo de transicao entre trilhas
**Como** aluno concluindo uma trilha
**Quero** texto de transicao para proxima
**Para que** a mudanca seja suave

**Criterios de Aceite:**
- [ ] Resumo T1 → T2
- [ ] Resumo T2 → T3
- [ ] Celebracao de conclusao

---

#### S4.4 - Desenvolver guia de navegacao do aluno
**Como** aluno novo
**Quero** mapa visual da jornada completa
**Para que** eu entenda o programa

**Criterios de Aceite:**
- [ ] Mapa visual das 3+1 trilhas
- [ ] Indicadores de progresso
- [ ] Versao para onboarding

---

## Epico 5: Documentacao Tecnica

**Prioridade:** P3 - Baixo
**Objetivo:** Documentar decisoes e arquitetura

### Stories

#### S5.1 - Criar documento de visao do produto
**Como** stakeholder do projeto
**Quero** PRD formal do VisionPro
**Para que** todos entendam o produto

**Criterios de Aceite:**
- [x] Documento VISAO_PRODUTO.md criado
- [ ] Validado por stakeholders
- [ ] Versionado

---

#### S5.2 - Definir personas do aluno
**Como** criador de conteudo
**Quero** personas detalhadas
**Para que** o conteudo seja direcionado

**Criterios de Aceite:**
- [ ] 3 personas documentadas
- [ ] Dores e objetivos claros
- [ ] Jornada recomendada por persona

---

#### S5.3 - Documentar stack tecnica da plataforma
**Como** desenvolvedor
**Quero** decisoes tecnicas documentadas
**Para que** a implementacao seja consistente

**Criterios de Aceite:**
- [ ] Tecnologias definidas
- [ ] Arquitetura documentada
- [ ] Decisoes justificadas

---

## Ordem de Execucao Sugerida

```
Fase 1: Fundacao (P0)
├── S1.1 Atualizar refs
├── S1.2 Definir cor Amber
└── S1.3 Padronizar numeracao

Fase 2: Conteudo Core (P1)
├── S2.1 Expandir T1
├── S2.2 Expandir T2
├── S2.3 Expandir T3
├── S2.4 Expandir Recursos
├── S2.5 Ferramentas IA
└── S2.6 Biblioteca prompts

Fase 3: Enriquecimento (P2)
├── S3.1-S3.5 (paralelo)
└── S4.1-S4.4 (paralelo)

Fase 4: Documentacao (P3)
└── S5.1-S5.3 (paralelo)
```

---

**Gerado por:** Sarah (Product Owner)
**Metodo:** BMad
