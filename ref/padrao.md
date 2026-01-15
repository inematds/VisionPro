# Dashboard Mastery - Documento Padrao Master

> **Referencia Rapida de Padroes** - Indice para todos os documentos de referencia

---

## Documentos de Referencia

| Documento | Descricao | Link |
|-----------|-----------|------|
| **PADRAO_PAGINAS.md** | Estrutura visual e elementos obrigatorios | [Ver](./PADRAO_PAGINAS.md) |
| **CONTENT_STRUCTURE.md** | Metodologia de conteudo e escrita | [Ver](./CONTENT_STRUCTURE.md) |
| **LAYOUT_REFERENCE.md** | Sistema de layout e componentes | [Ver](./LAYOUT_REFERENCE.md) |
| **FEP_STYLE_REFERENCE.md** | Guia completo de estilos | [Ver](./FEP_STYLE_REFERENCE.md) |
| **ERROS_COMUNS.md** | Inconsistencias identificadas e correcoes | [Ver](./ERROS_COMUNS.md) |

---

## Resumo Executivo - Padroes Obrigatorios

### 1. Estrutura do Curso

```
Dashboard Mastery
├── 3 Trilhas
│   ├── Trilha 1: Fundamentos (Emerald)
│   ├── Trilha 2: Tecnicas (Blue)
│   └── Trilha 3: Avancado (Purple)
├── 8 Modulos por Trilha (24 total)
├── 6 Topicos por Modulo (48 por trilha)
└── 144 Topicos totais
```

### 2. Cores das Trilhas

| Trilha | Nome | Cor | Classes Tailwind |
|--------|------|-----|------------------|
| T1 | Fundamentos | Emerald | `text-emerald-400`, `bg-emerald-500/20`, `border-emerald-500/30` |
| T2 | Tecnicas | Blue | `text-blue-400`, `bg-blue-500/20`, `border-blue-500/30` |
| T3 | Avancado | Purple | `text-purple-400`, `bg-purple-500/20`, `border-purple-500/30` |

### 3. Elementos Obrigatorios em TODAS as Paginas

1. **Navigation Global**
   - Logo: `📊 Dashboard Mastery` (yellow-400)
   - Link: `INEMA.CLUB` (sky-400) - OBRIGATORIO
   - Trilhas: `Fundamentos | Tecnicas | Avancado` (mobile: T1 | T2 | T3)
   - Theme Toggle: SVG icons dark/light

2. **Breadcrumb** (em paginas de modulo)
   - Formato: `Inicio / Trilha X / Modulo X.X`

3. **Header com Stats**
   - Badge da trilha
   - Titulo com emoji
   - Descricao
   - Stats: Topicos, Duracao, Nivel, Tipo

4. **Topicos com Numero em Circulo**
   - NAO usar setas
   - Numero de 1-6 em circulo colorido
   - Emoji + Titulo + Subtitulo

5. **3 Secoes por Topico**
   - O que e
   - Por que aprender
   - Conceitos-chave

6. **Botoes ESQUERDA**
   - `justify-start` (NAO center)
   - Ver em Modal + Ver Completo

---

## Templates Rapidos

### Navigation (Trilha 1 Ativa)

```html
<nav class="sticky top-0 z-50 bg-dark-900/95 backdrop-blur-sm border-b border-dark-600">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex justify-between items-center h-14">
      <div class="flex items-center space-x-4">
        <a href="../../index.html" class="flex items-center space-x-2 text-yellow-400 hover:text-yellow-300">
          <span class="text-2xl">📊</span>
          <span class="font-bold text-lg hidden sm:inline">Dashboard Mastery</span>
        </a>
        <span class="text-neutral-600">|</span>
        <a href="https://inema.club" target="_blank" class="text-sky-400 hover:text-sky-300 text-sm font-medium">INEMA.CLUB</a>
      </div>
      <div class="flex items-center space-x-1 sm:space-x-2">
        <a href="index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-emerald-400 bg-emerald-500/10">
          <span class="sm:hidden">T1</span>
          <span class="hidden sm:inline">Fundamentos</span>
        </a>
        <a href="../trilha2/index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-neutral-400 hover:text-blue-400 hover:bg-blue-500/10 transition-colors">
          <span class="sm:hidden">T2</span>
          <span class="hidden sm:inline">Tecnicas</span>
        </a>
        <a href="../trilha3/index.html" class="px-3 py-1.5 rounded-lg text-sm font-semibold text-neutral-400 hover:text-purple-400 hover:bg-purple-500/10 transition-colors">
          <span class="sm:hidden">T3</span>
          <span class="hidden sm:inline">Avancado</span>
        </a>
        <button id="theme-toggle" class="p-2 rounded-lg bg-dark-700 hover:bg-dark-600 transition-colors ml-2">
          <svg id="theme-toggle-dark-icon" class="hidden w-5 h-5 text-neutral-300" fill="currentColor" viewBox="0 0 20 20">
            <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z"></path>
          </svg>
          <svg id="theme-toggle-light-icon" class="hidden w-5 h-5 text-neutral-300" fill="currentColor" viewBox="0 0 20 20">
            <path d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z" fill-rule="evenodd" clip-rule="evenodd"></path>
          </svg>
        </button>
      </div>
    </div>
  </div>
</nav>
```

### Numero em Circulo

```html
<span class="w-12 h-12 rounded-full bg-emerald-500/20 text-emerald-400 font-bold text-xl flex items-center justify-center mr-4">1</span>
```

### Box de Detalhamento

```html
<div class="bg-dark-800 rounded-xl border border-dark-600 p-6">
  <h3 class="text-lg font-semibold text-emerald-400 mb-4 flex items-center">
    <span class="mr-2">📋</span> Titulo
  </h3>
  <!-- conteudo -->
</div>
```

### Dica Pratica

```html
<div class="bg-gradient-to-r from-emerald-900/30 to-dark-800 rounded-xl border border-emerald-500/30 p-6">
  <h3 class="text-lg font-semibold text-emerald-400 mb-3 flex items-center">
    <span class="mr-2">💡</span> Dica Pratica
  </h3>
  <p class="text-neutral-300">Texto da dica...</p>
</div>
```

### Grid Fazer vs Evitar

```html
<div class="bg-dark-800 rounded-xl border border-dark-600 p-6">
  <h3 class="text-lg font-semibold text-emerald-400 mb-4">✅ Fazer vs ❌ Evitar</h3>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
    <div class="bg-emerald-900/20 border border-emerald-500/30 rounded-lg p-4">
      <div class="text-emerald-400 font-semibold mb-3">✅ Fazer</div>
      <ul class="space-y-2 text-sm text-neutral-300">
        <li>• Item 1</li>
        <li>• Item 2</li>
      </ul>
    </div>
    <div class="bg-red-900/20 border border-red-500/30 rounded-lg p-4">
      <div class="text-red-400 font-semibold mb-3">❌ Evitar</div>
      <ul class="space-y-2 text-sm text-neutral-300">
        <li>• Item 1</li>
        <li>• Item 2</li>
      </ul>
    </div>
  </div>
</div>
```

### Codigo de Exemplo

```html
<div class="bg-dark-800 rounded-xl border border-dark-600 p-6">
  <h3 class="text-lg font-semibold text-emerald-400 mb-4 flex items-center">
    <span class="mr-2">💻</span> Codigo: Titulo
  </h3>
  <div class="bg-dark-900 rounded-lg p-4 font-mono text-sm overflow-x-auto">
    <pre class="text-green-400">// codigo aqui</pre>
  </div>
</div>
```

### Resumo do Modulo

```html
<section class="mb-12">
  <div class="bg-gradient-to-br from-emerald-900/40 via-dark-800 to-dark-800 rounded-xl border border-emerald-500/30 p-8">
    <h2 class="text-2xl font-bold mb-6 flex items-center">
      <span class="mr-3">📝</span> Resumo do Modulo
    </h2>
    <div class="space-y-4 mb-8">
      <div class="flex items-start space-x-3">
        <span class="text-emerald-400 mt-1">✓</span>
        <div>
          <strong class="text-white">Conceito</strong>
          <span class="text-neutral-400"> - Descricao breve</span>
        </div>
      </div>
    </div>
    <div class="flex flex-col sm:flex-row gap-4">
      <a href="index.html" class="flex-1 px-6 py-3 bg-dark-700 hover:bg-dark-600 text-neutral-300 rounded-lg text-center transition-colors">
        ← Voltar para Trilha
      </a>
      <a href="modulo-X-Y.html" class="flex-1 px-6 py-3 bg-emerald-600 hover:bg-emerald-500 text-white rounded-lg text-center transition-colors font-semibold">
        Proximo Modulo →
      </a>
    </div>
  </div>
</section>
```

---

## Theme Toggle Script

```javascript
<script>
  const themeToggle = document.getElementById('theme-toggle');
  const themeToggleDarkIcon = document.getElementById('theme-toggle-dark-icon');
  const themeToggleLightIcon = document.getElementById('theme-toggle-light-icon');
  const html = document.documentElement;

  // Set initial icon
  if (localStorage.getItem('theme') === 'light' || (!localStorage.getItem('theme') && !html.classList.contains('dark'))) {
    themeToggleDarkIcon.classList.remove('hidden');
    html.classList.remove('dark');
  } else {
    themeToggleLightIcon.classList.remove('hidden');
  }

  themeToggle.addEventListener('click', () => {
    themeToggleDarkIcon.classList.toggle('hidden');
    themeToggleLightIcon.classList.toggle('hidden');
    html.classList.toggle('dark');
    localStorage.setItem('theme', html.classList.contains('dark') ? 'dark' : 'light');
  });
</script>
```

---

## Checklist de Qualidade

### Antes de Criar Pagina

- [ ] Verificar trilha e cor correspondente
- [ ] Copiar template base correto
- [ ] Atualizar paths de navegacao

### Durante Criacao

- [ ] Navigation com INEMA.CLUB
- [ ] Trilhas com nomes completos (Fundamentos, Tecnicas, Avancado)
- [ ] Breadcrumb correto
- [ ] Header com badge, titulo, descricao
- [ ] Stats grid (4 itens)
- [ ] 6 topicos com numeros em circulo
- [ ] Cada topico com 3 secoes internas
- [ ] Boxes variados (detalhamento, dica, codigo, comparacao)
- [ ] Resumo final
- [ ] Botoes ESQUERDA

### Depois de Criar

- [ ] Testar dark/light mode
- [ ] Testar responsivo
- [ ] Verificar links
- [ ] Commit e push

---

**Ultima atualizacao:** 2026-01-02
**Versao:** 1.0
**Projeto:** Dashboard Mastery
