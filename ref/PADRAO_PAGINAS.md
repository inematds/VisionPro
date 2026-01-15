# Padrao de Paginas - VisionPro

Padrao visual e estrutural para o curso de Producao Audiovisual com IA.

**Hospedagem:** GitHub Pages (https://inematds.github.io/VisionPro)

---

## 1. Menu no Topo

```html
[🎬 VisionPro] | [INEMA.CLUB]
[Fundamentos] [Metodo] [Avancado] [Laboratorios] [🌙 Theme]
```

**Elementos:**
- Logo com emoji + nome do curso
- Link externo para INEMA.CLUB (azul claro `text-sky-400`)
- Botoes das 4 trilhas com descricao (ativo com cor, outros neutros)
  - T1: Fundamentos (emerald) - 8 modulos
  - T2: Metodo (blue) - 10 modulos
  - T3: Avancado (purple) - 7 modulos
  - T4: Laboratorios (amber) - 5 modulos
- Toggle de tema (dark/light)

**INEMA.CLUB deve aparecer em TODAS as paginas (index, trilhas, modulos)**

---

## 2. Topicos Expansiveis com Numeros

**Estrutura:**
```
[1] 🎯 Titulo do Topico - Descricao breve
  └─ O que e: [Explicacao]
  └─ Por que aprender: [Justificativa]
  └─ Conceitos-chave: [Lista]
```

**Comportamento:**
- Numero em circulo (nao seta) como indicador
- Fecha outros topicos do mesmo modulo ao abrir um novo
- Conteudo oculto com classe `.topic-explanation`
- Toggle via JavaScript `toggleTopic(button)`

**HTML do numero:**
```html
<span class="w-6 h-6 rounded-full bg-emerald-500/20 text-emerald-400 text-sm font-bold flex items-center justify-center">1</span>
```

---

## 3. Estrutura de Cada Topico

Cada topico DEVE ter 3 secoes internas:

| Secao | Descricao |
|-------|-----------|
| **O que e** | Definicao clara e objetiva do conceito |
| **Por que aprender** | Justificativa e beneficios praticos |
| **Conceitos-chave** | Lista dos pontos principais a memorizar |

**Exemplo:**
```html
<div class="bg-dark-700/50 rounded-lg p-4 space-y-3">
  <div>
    <span class="text-emerald-400 font-semibold">O que e:</span>
    <p class="text-neutral-300 text-sm">Dashboard e uma interface visual...</p>
  </div>
  <div>
    <span class="text-emerald-400 font-semibold">Por que aprender:</span>
    <p class="text-neutral-300 text-sm">Reduz tempo de analise em 80%...</p>
  </div>
  <div>
    <span class="text-emerald-400 font-semibold">Conceitos-chave:</span>
    <p class="text-neutral-300 text-sm">Consolidacao, tempo real, interatividade...</p>
  </div>
</div>
```

---

## 4. Botoes Modal e Completo (ESQUERDA)

Cada modulo tem 2 botoes no rodape, **alinhados a esquerda**:

```html
<div class="p-4 bg-dark-700/30 flex justify-start space-x-3">
  <button onclick="openModal('modal-1-1')">
    📖 Ver em Modal
  </button>
  <a href="modulo-1-1.html">
    📄 Ver Completo
  </a>
</div>
```

**Funcionalidade:**
- **Ver em Modal**: Abre o conteudo completo em overlay (carrega via fetch)
- **Ver Completo**: Navega para a pagina completa do modulo
- Ambos levam para a MESMA pagina de conteudo completo

---

## Estrutura Completa de um Modulo

```html
<!-- Card do Modulo -->
<div class="bg-dark-800 rounded-xl border border-dark-600 mb-6">

  <!-- Header -->
  <div class="p-6 border-b border-dark-600">
    <span class="text-emerald-400 font-bold">1.1</span>
    <span class="text-xs text-neutral-500">~20 min</span>
    <h3>📊 Titulo do Modulo</h3>
    <p>Descricao breve do modulo.</p>
  </div>

  <!-- Topicos Expansiveis -->
  <div class="divide-y divide-dark-600">
    <div class="topic-item">
      <button onclick="toggleTopic(this)">
        <span class="w-6 h-6 rounded-full bg-emerald-500/20 text-emerald-400">1</span>
        🎯 Topico 1 - Descricao
      </button>
      <div class="topic-explanation">
        <!-- O que e, Por que aprender, Conceitos-chave -->
      </div>
    </div>
    <!-- Mais topicos... -->
  </div>

  <!-- Botoes (ESQUERDA) -->
  <div class="p-4 bg-dark-700/30 flex justify-start space-x-3">
    <button>📖 Ver em Modal</button>
    <a href="modulo-x-x.html">📄 Ver Completo</a>
  </div>

</div>
```

---

## JavaScript Necessario

```javascript
// Toggle de topicos (sem mudanca de seta, usa numeros)
function toggleTopic(button) {
  const topicItem = button.closest('.topic-item');
  const explanation = topicItem.querySelector('.topic-explanation');

  // Fecha outros do mesmo modulo
  const moduleCard = button.closest('.bg-dark-800');
  moduleCard.querySelectorAll('.topic-explanation.active').forEach(exp => {
    if (exp !== explanation) {
      exp.classList.remove('active');
    }
  });

  // Toggle atual
  explanation.classList.toggle('active');
}

// Modal
function openModal(modalId) {
  // Carrega conteudo via fetch e exibe em overlay
}

function closeModal() {
  // Fecha o overlay
}
```

---

## CSS Necessario

```css
.topic-explanation { display: none; }
.topic-explanation.active { display: block; }
```

---

## Cores por Trilha

| Trilha | Nome | Cor Principal | Classes |
|--------|------|---------------|---------|
| Trilha 1 | Fundamentos | Emerald | `text-emerald-400`, `bg-emerald-500/20` |
| Trilha 2 | Tecnicas | Blue | `text-blue-400`, `bg-blue-500/20` |
| Trilha 3 | Avancado | Purple | `text-purple-400`, `bg-purple-500/20` |

---

## Link INEMA.CLUB

- Cor: `text-sky-400 hover:text-sky-300`
- Deve aparecer em TODAS as paginas
- Posicao: ao lado do logo, separado por `|`
