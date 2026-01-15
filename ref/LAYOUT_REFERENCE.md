# Layout Reference - Dashboard Mastery

Documentacao do sistema de layout e design utilizado nas paginas do curso.

---

## 1. Estrutura Base HTML

```html
<!DOCTYPE html>
<html lang="pt-BR" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Titulo] | Dashboard Mastery</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
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
  </script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Inter', sans-serif; }
    .dark body { background-color: #111827; }
    /* Light mode overrides... */
  </style>
</head>
<body class="bg-dark-900 text-neutral-100 min-h-screen">
  <!-- Conteudo -->
</body>
</html>
```

---

## 2. Paleta de Cores

### Dark Mode (Padrao)

| Nome | Codigo | Uso |
|------|--------|-----|
| `dark-900` | `#111827` | Fundo principal |
| `dark-800` | `#1f2937` | Cards, containers |
| `dark-700` | `#374151` | Elementos internos |
| `dark-600` | `#4b5563` | Bordas, divisores |

### Cores das Trilhas

| Trilha | Nome | Cor | Uso |
|--------|------|-----|-----|
| Trilha 1 | Fundamentos | Emerald | `text-emerald-400`, `bg-emerald-500/20` |
| Trilha 2 | Tecnicas | Blue | `text-blue-400`, `bg-blue-500/20` |
| Trilha 3 | Avancado | Purple | `text-purple-400`, `bg-purple-500/20` |

### Cores Especiais

| Cor | Codigo | Uso |
|-----|--------|-----|
| Primary (Yellow) | `#FACC15` | Logo, CTAs, destaques |
| Sky | `text-sky-400` | Link INEMA.CLUB |

---

## 3. Navigation Global (OBRIGATORIO)

O menu global DEVE aparecer em TODAS as paginas do curso.

### Elementos Obrigatorios

1. **Logo** (📊 Dashboard Mastery) - Link para index.html
2. **INEMA.CLUB** - Link externo em sky-400
3. **Trilhas** com descricao (Fundamentos, Tecnicas, Avancado)
4. **Theme Toggle** - Botao dark/light

```html
<nav class="sticky top-0 z-50 bg-dark-900/95 backdrop-blur-sm border-b border-dark-600">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex justify-between items-center h-14">
      <!-- Logo + INEMA.CLUB -->
      <div class="flex items-center space-x-4">
        <a href="../../index.html" class="flex items-center space-x-2 text-yellow-400 hover:text-yellow-300">
          <span class="text-2xl">📊</span>
          <span class="font-bold text-lg hidden sm:inline">Dashboard Mastery</span>
        </a>
        <span class="text-neutral-600">|</span>
        <a href="https://inema.club" target="_blank" class="text-sky-400 hover:text-sky-300 text-sm font-medium">INEMA.CLUB</a>
      </div>

      <!-- Trilhas com descricao -->
      <div class="flex items-center space-x-1 sm:space-x-2">
        <a href="index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-emerald-400 bg-emerald-500/10">
          <span class="sm:hidden">T1</span>
          <span class="hidden sm:inline">Fundamentos</span>
        </a>
        <a href="../trilha2/index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-neutral-400 hover:text-blue-400 hover:bg-blue-500/10">
          <span class="sm:hidden">T2</span>
          <span class="hidden sm:inline">Tecnicas</span>
        </a>
        <a href="../trilha3/index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-neutral-400 hover:text-purple-400 hover:bg-purple-500/10">
          <span class="sm:hidden">T3</span>
          <span class="hidden sm:inline">Avancado</span>
        </a>
        <!-- Theme Toggle -->
      </div>
    </div>
  </div>
</nav>
```

---

## 4. Estrutura de Pagina de Trilha (Index)

```
TRILHA INDEX
├── Navigation Global (com INEMA.CLUB)
├── Header
│   └── Badge da trilha
│   └── Titulo + emoji
│   └── Descricao
│   └── Stats (Modulos, Topicos, Duracao, Nivel)
├── Modulos (8 por trilha)
│   └── Modulo Card
│       ├── Header (numero, titulo, descricao)
│       ├── Topicos Expansiveis (6 por modulo)
│       │   └── Numero em circulo (NAO seta)
│       │   └── Emoji + titulo + subtitulo
│       │   └── Conteudo com 3 secoes
│       └── Botoes (ESQUERDA - justify-start)
│           └── Ver em Modal
│           └── Ver Completo
└── Footer
```

---

## 5. Estrutura de Pagina Completa de Modulo

```
MODULO COMPLETO
├── Navigation Global (com INEMA.CLUB)
├── Breadcrumb (Inicio / Trilha X / Modulo X.X)
├── Header
│   └── Badge do modulo
│   └── Titulo + emoji
│   └── Descricao
│   └── Stats (Topicos, Duracao, Nivel, Tipo)
├── Topicos (6 secoes completas)
│   └── Section por topico
│       ├── Numero em circulo + titulo
│       ├── Paragrafo introdutorio
│       ├── Boxes de detalhamento
│       ├── Grids de comparacao
│       └── Dicas praticas
├── Resumo Final
│   └── O que aprendemos (checklist)
│   └── Proximo modulo
│   └── CTAs de navegacao
└── Footer
```

---

## 6. Componentes de Layout

### 6.1 Header do Modulo

```html
<header class="bg-gradient-to-br from-emerald-900/30 via-dark-800 to-dark-800 py-12 border-b border-dark-600">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
    <span class="inline-block px-3 py-1 bg-emerald-500/20 text-emerald-400 text-xs font-semibold rounded-full mb-4">
      MODULO 1.1
    </span>
    <h1 class="text-3xl sm:text-4xl font-bold mb-4">📊 Titulo do Modulo</h1>
    <p class="text-lg text-neutral-400 max-w-3xl">Descricao do modulo...</p>

    <!-- Stats Banner -->
    <div class="grid grid-cols-4 gap-4 mt-8 max-w-2xl">
      <div class="bg-dark-800/50 rounded-lg p-3 border border-dark-600">
        <div class="text-xl font-bold text-emerald-400">6</div>
        <div class="text-xs text-neutral-400">Topicos</div>
      </div>
      <!-- mais stats... -->
    </div>
  </div>
</header>
```

### 6.2 Section de Topico

```html
<section id="topico-1" class="mb-16">
  <div class="flex items-center space-x-3 mb-6">
    <span class="flex items-center justify-center w-10 h-10 rounded-full bg-emerald-500/20 text-emerald-400 font-bold">1</span>
    <h2 class="text-2xl font-bold">🎯 Titulo do Topico</h2>
  </div>

  <p class="text-neutral-300 mb-6 leading-relaxed">
    Paragrafo introdutorio com <strong class="text-emerald-400">destaque</strong>...
  </p>

  <!-- Boxes de conteudo... -->
</section>
```

### 6.3 Box de Detalhamento

```html
<div class="bg-dark-700/50 rounded-xl p-6 border border-dark-600 mb-6">
  <h3 class="text-lg font-semibold text-emerald-400 mb-4">Titulo</h3>
  <!-- Conteudo -->
</div>
```

### 6.4 Box de Dados/Pesquisa

```html
<div class="bg-blue-900/20 rounded-xl p-6 border border-blue-500/30 mb-6">
  <h3 class="text-lg font-semibold text-blue-400 mb-3">📊 Dados de Pesquisa</h3>
  <ul class="space-y-2 text-neutral-300">...</ul>
</div>
```

### 6.5 Dica Pratica

```html
<div class="bg-primary/10 rounded-xl p-6 border border-primary/30">
  <h3 class="text-lg font-semibold text-primary mb-3">💡 Dica Pratica</h3>
  <p class="text-neutral-300">...</p>
</div>
```

### 6.6 Grid de Comparacao

```html
<div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
  <div class="bg-emerald-900/20 rounded-xl p-6 border border-emerald-500/30">
    <h3 class="text-lg font-semibold text-emerald-400 mb-4">✓ Fazer</h3>
    <ul class="space-y-3 text-neutral-300">...</ul>
  </div>
  <div class="bg-red-900/20 rounded-xl p-6 border border-red-500/30">
    <h3 class="text-lg font-semibold text-red-400 mb-4">✗ Nao Fazer</h3>
    <ul class="space-y-3 text-neutral-300">...</ul>
  </div>
</div>
```

### 6.7 Timeline de Passos

```html
<div class="space-y-4">
  <div class="flex items-start space-x-4">
    <div class="flex-shrink-0 w-10 h-10 rounded-full bg-emerald-500/20 flex items-center justify-center">
      <span class="text-emerald-400 font-bold">1</span>
    </div>
    <div class="flex-1 bg-dark-700/50 rounded-xl p-4 border border-dark-600">
      <h4 class="font-semibold text-neutral-200 mb-1">Titulo do Passo</h4>
      <p class="text-sm text-neutral-400">Descricao...</p>
    </div>
  </div>
</div>
```

### 6.8 Resumo Final

```html
<section class="bg-dark-800 rounded-2xl p-8 border border-dark-600">
  <h2 class="text-2xl font-bold mb-6 flex items-center">
    <span class="mr-3">📝</span> Resumo do Modulo
  </h2>

  <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
    <div>
      <h3 class="font-semibold text-emerald-400 mb-3">O que aprendemos:</h3>
      <ul class="space-y-2 text-neutral-300">...</ul>
    </div>
    <div>
      <h3 class="font-semibold text-emerald-400 mb-3">Proximo modulo:</h3>
      <p class="text-neutral-300 mb-4">...</p>
      <a href="modulo-1-2.html" class="text-emerald-400 hover:text-emerald-300">
        Ir para Modulo 1.2 →
      </a>
    </div>
  </div>

  <div class="flex flex-col sm:flex-row gap-4">
    <a href="index.html" class="flex-1 text-center px-6 py-3 bg-dark-700 text-neutral-300 rounded-lg font-semibold hover:bg-dark-600">
      ← Voltar para Trilha 1
    </a>
    <a href="modulo-1-2.html" class="flex-1 text-center px-6 py-3 bg-emerald-600 text-white rounded-lg font-semibold hover:bg-emerald-500">
      Proximo Modulo →
    </a>
  </div>
</section>
```

---

## 7. Padroes de Espacamento

| Classe | Uso |
|--------|-----|
| `mb-16` | Entre secoes principais (topicos) |
| `mb-8` | Antes do resumo final |
| `mb-6` | Entre boxes dentro de uma secao |
| `mb-4` | Entre elementos menores |
| `p-8` | Padding de cards principais |
| `p-6` | Padding de boxes internos |
| `gap-4` | Grid de cards |
| `gap-6` | Grid de comparacao |

---

## 8. Responsividade

```html
<!-- Grid responsivo -->
<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">

<!-- Texto responsivo -->
<h1 class="text-3xl sm:text-4xl font-bold">

<!-- Padding responsivo -->
<div class="px-4 sm:px-6 lg:px-8">

<!-- Visibilidade -->
<span class="sm:hidden">T1</span>
<span class="hidden sm:inline">Fundamentos</span>
```

---

## 9. Checklist de Criacao de Pagina

- [ ] Navigation Global com INEMA.CLUB (sky-400)
- [ ] Trilhas com descricao (Fundamentos, Tecnicas, Avancado)
- [ ] Breadcrumb (em paginas de modulo)
- [ ] Header com badge, titulo e descricao
- [ ] Stats banner
- [ ] 6 topicos completos
- [ ] Cada topico com numero em circulo
- [ ] Boxes variados (detalhamento, dados, dica, comparacao)
- [ ] Resumo final com checklist
- [ ] CTAs de navegacao
- [ ] Footer
- [ ] Dark/light mode funcionando
- [ ] Responsivo

---

**Ultima atualizacao:** 2025-12-26
**Projeto:** Dashboard Mastery
