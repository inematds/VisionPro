# Dashboard Mastery - Guia de Referencia de Estilos e Layout

> **Supercurso Dashboard Mastery**
> Sistema de Design Completo para Dashboards Profissionais

**Versao:** 2.0 | **Data:** 2025-12-26 | **Autor:** Dashboard Mastery Team

---

## Indice Rapido

- [Visao Geral](#visao-geral)
- [Dark Mode](#dark-mode)
- [Identidade Visual](#identidade-visual)
- [Sistema de Cores](#sistema-de-cores)
- [Navegacao Global](#navegacao-global)
- [Tipografia](#tipografia)
- [Espacamento e Grid](#espacamento-e-grid)
- [Componentes](#componentes)
- [Animacoes](#animacoes)
- [Responsividade](#responsividade)

---

## Visao Geral

### Sobre o Projeto

**Supercurso Dashboard Mastery** e um curso completo de criacao de dashboards profissionais com Next.js, React e melhores praticas de UI/UX. Possui 3 trilhas progressivas de aprendizado.

### Principios de Design

1. **Clareza** - Interface limpa e facil de navegar
2. **Progressao Visual** - Cada trilha tem sua identidade de cor
3. **Acessibilidade** - WCAG 2.1 AA compliant
4. **Performance** - Animacoes suaves
5. **Mobile-First** - Design responsivo

### Stack Tecnologico

```
Framework CSS:  Tailwind CSS 3.x (CDN)
JavaScript:     Vanilla JS
Fontes:         Google Fonts - Inter
Build:          HTML puro (sem build process)
Dark Mode:      Tailwind class-based (localStorage)
```

---

## Dark Mode

### Configuracao

```javascript
tailwind.config = {
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: '#FACC15',
        dark: { 900: '#111827', 800: '#1f2937', 700: '#374151', 600: '#4b5563' }
      }
    }
  }
}
```

### Paleta Dark Mode

```css
/* Dark Mode (Padrao) */
--bg-primary:      #111827   /* dark-900 */
--bg-card:         #1f2937   /* dark-800 */
--bg-interactive:  #374151   /* dark-700 */
--border:          #4b5563   /* dark-600 */
--text-primary:    #f5f5f5   /* neutral-100 */
--text-secondary:  #d4d4d4   /* neutral-300 */

/* Light Mode */
--bg-primary:      #f8fafc   /* slate-50 */
--bg-card:         #ffffff   /* white */
--bg-interactive:  #f1f5f9   /* slate-100 */
--border:          #e2e8f0   /* slate-200 */
--text-primary:    #1e293b   /* slate-800 */
--text-secondary:  #475569   /* slate-600 */
```

---

## Identidade Visual

### Logo e Marca

```
Nome:     Dashboard Mastery
Emoji:    📊
Fonte:    Inter Bold (700)
Cor:      Yellow 400 (#FACC15)
Tagline:  "Supercurso de Dashboards Profissionais"
```

### Link Externo

```
INEMA.CLUB:  text-sky-400 hover:text-sky-300
Posicao:     Ao lado do logo, separado por |
Visivel:     Em TODAS as paginas
```

---

## Navegacao Global

### Menu Global (Todas as Paginas)

```html
<nav class="sticky top-0 z-50 bg-dark-900/95 backdrop-blur-sm border-b border-dark-600">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex justify-between items-center h-14">
      <!-- Logo + INEMA.CLUB -->
      <div class="flex items-center space-x-4">
        <a href="index.html" class="flex items-center space-x-2 text-yellow-400 hover:text-yellow-300">
          <span class="text-2xl">📊</span>
          <span class="font-bold text-lg hidden sm:inline">Dashboard Mastery</span>
        </a>
        <span class="text-neutral-600">|</span>
        <a href="https://inema.club" target="_blank" class="text-sky-400 hover:text-sky-300 text-sm font-medium">INEMA.CLUB</a>
      </div>

      <!-- Trilhas + Theme Toggle -->
      <div class="flex items-center space-x-1 sm:space-x-2">
        <a href="curso/trilha1/index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold...">
          <span class="sm:hidden">T1</span>
          <span class="hidden sm:inline">Fundamentos</span>
        </a>
        <!-- Trilha 2, 3... -->
        <button id="theme-toggle">...</button>
      </div>
    </div>
  </div>
</nav>
```

### Cores das Trilhas

| Trilha | Nome | Cor | Classes Ativa | Classes Inativa |
|--------|------|-----|---------------|-----------------|
| **Trilha 1** | Fundamentos | Emerald | `bg-emerald-500/10 text-emerald-400` | `text-neutral-400 hover:text-emerald-400 hover:bg-emerald-500/10` |
| **Trilha 2** | Tecnicas | Blue | `bg-blue-500/10 text-blue-400` | `text-neutral-400 hover:text-blue-400 hover:bg-blue-500/10` |
| **Trilha 3** | Avancado | Purple | `bg-purple-500/10 text-purple-400` | `text-neutral-400 hover:text-purple-400 hover:bg-purple-500/10` |

---

## Sistema de Cores

### Cores por Trilha (PRINCIPAL)

```css
/* 3 Trilhas de Aprendizado */
Trilha 1 (Fundamentos):  #10B981  /* Emerald 500 */
Trilha 2 (Tecnicas):     #3B82F6  /* Blue 500 */
Trilha 3 (Avancado):     #8B5CF6  /* Purple 500 */
```

### Classes por Trilha

```css
/* Trilha 1 - Fundamentos (Emerald) */
Badge:      bg-emerald-500/20 text-emerald-400
Borda:      border-emerald-500/30
Gradiente:  from-emerald-900/30 to-dark-800
Numero:     w-6 h-6 rounded-full bg-emerald-500/20 text-emerald-400

/* Trilha 2 - Tecnicas (Blue) */
Badge:      bg-blue-500/20 text-blue-400
Borda:      border-blue-500/30
Gradiente:  from-blue-900/30 to-dark-800
Numero:     w-6 h-6 rounded-full bg-blue-500/20 text-blue-400

/* Trilha 3 - Avancado (Purple) */
Badge:      bg-purple-500/20 text-purple-400
Borda:      border-purple-500/30
Gradiente:  from-purple-900/30 to-dark-800
Numero:     w-6 h-6 rounded-full bg-purple-500/20 text-purple-400
```

### Cores Semanticas

| Cor | Uso |
|-----|-----|
| `emerald` | Sucesso, positivo, "fazer", Trilha 1 |
| `red` | Erro, alerta, "nao fazer" |
| `blue` | Informacao, dados, Trilha 2 |
| `yellow/primary` | Logo, CTAs, destaques |
| `purple` | Avancado, premium, Trilha 3 |
| `sky` | Links externos (INEMA.CLUB) |

---

## Tipografia

### Hierarquia

```css
H1 (Hero):      text-4xl sm:text-5xl font-bold
H2 (Section):   text-2xl font-bold
H3 (Subsection): text-xl font-bold ou text-lg font-semibold
Body:           text-neutral-300
Secondary:      text-neutral-400
Small:          text-sm text-neutral-400
```

---

## Espacamento e Grid

### Container Principal

```html
<main class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
```

### Espacamento Padrao

| Classe | Uso |
|--------|-----|
| `mb-16` | Entre secoes principais |
| `mb-6` | Entre sub-secoes |
| `mb-4` | Entre elementos |
| `p-6` | Padding de cards |
| `gap-4` ou `gap-6` | Entre itens de grid |
| `space-y-3` | Entre itens de lista |

---

## Componentes

### Topicos Expansiveis (Numeros em Circulo)

```html
<button onclick="toggleTopic(this)" class="w-full px-6 py-4 flex items-center...">
  <span class="w-6 h-6 rounded-full bg-emerald-500/20 text-emerald-400 text-sm font-bold flex items-center justify-center">1</span>
  <span class="text-lg">🎯</span>
  <div>
    <span class="font-medium">Titulo do Topico</span>
    <span class="text-neutral-500 text-sm ml-2">- Descricao breve</span>
  </div>
</button>
```

### Conteudo do Topico (3 Secoes Obrigatorias)

```html
<div class="bg-dark-700/50 rounded-lg p-4 space-y-3">
  <div>
    <span class="text-emerald-400 font-semibold">O que e:</span>
    <p class="text-neutral-300 text-sm">Definicao clara...</p>
  </div>
  <div>
    <span class="text-emerald-400 font-semibold">Por que aprender:</span>
    <p class="text-neutral-300 text-sm">Beneficios praticos...</p>
  </div>
  <div>
    <span class="text-emerald-400 font-semibold">Conceitos-chave:</span>
    <p class="text-neutral-300 text-sm">Lista de termos...</p>
  </div>
</div>
```

### Botoes de Modulo (ESQUERDA)

```html
<div class="p-4 bg-dark-700/30 flex justify-start space-x-3">
  <button onclick="openModal('modal-1-1')" class="px-4 py-2 text-sm bg-dark-600 hover:bg-dark-500 rounded-lg">
    📖 Ver em Modal
  </button>
  <a href="modulo-1-1.html" class="px-4 py-2 text-sm bg-emerald-600 hover:bg-emerald-500 text-white rounded-lg">
    📄 Ver Completo
  </a>
</div>
```

### Grid de Comparacao

```html
<div class="grid md:grid-cols-2 gap-6">
  <div class="bg-emerald-900/20 p-6 rounded-xl border border-emerald-500/30">
    <h4 class="font-bold text-emerald-400 mb-4">✓ O que FAZER</h4>
    <ul class="space-y-3 text-neutral-300">...</ul>
  </div>
  <div class="bg-red-900/20 p-6 rounded-xl border border-red-500/30">
    <h4 class="font-bold text-red-400 mb-4">✗ O que NAO fazer</h4>
    <ul class="space-y-3 text-neutral-300">...</ul>
  </div>
</div>
```

---

## JavaScript

### Toggle de Topicos (Sem Setas)

```javascript
function toggleTopic(button) {
  const topicItem = button.closest('.topic-item');
  const explanation = topicItem.querySelector('.topic-explanation');
  const moduleCard = button.closest('.bg-dark-800');

  moduleCard.querySelectorAll('.topic-explanation.active').forEach(exp => {
    if (exp !== explanation) {
      exp.classList.remove('active');
    }
  });

  explanation.classList.toggle('active');
}
```

### CSS Necessario

```css
.topic-explanation { display: none; }
.topic-explanation.active { display: block; }
```

---

## Checklist de Implementacao

- [ ] Navigation com INEMA.CLUB (sky-400)
- [ ] Trilhas com descricao (Fundamentos, Tecnicas, Avancado)
- [ ] Header completo (badge, titulo, descricao)
- [ ] Stats banner com metricas
- [ ] Topicos com NUMEROS (nao setas)
- [ ] 3 secoes por topico (O que e, Por que, Conceitos)
- [ ] Botoes a ESQUERDA (justify-start)
- [ ] Resumo/checklist no final
- [ ] Dark/light mode funcionando
- [ ] Responsivo em mobile

---

**Ultima atualizacao:** 2025-12-26
**Versao:** 2.0
**Autor:** Dashboard Mastery Team
