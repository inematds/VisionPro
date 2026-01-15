# Erros Comuns e Inconsistencias - Dashboard Mastery

> **Documento de Referencia** - Lista de problemas identificados para correcao

---

## 1. Inconsistencia de Texto nos Botoes

### Problema
O texto do botao de modal nao corresponde a documentacao.

| Local | Texto Atual | Texto Documentado |
|-------|-------------|-------------------|
| Implementacao | `🔍 Ver Modal` | - |
| PADRAO_PAGINAS.md | - | `📖 Ver em Modal` |

### Arquivos Afetados
- `curso/trilha1/index.html`
- `curso/trilha2/index.html`
- `curso/trilha3/index.html`

### Decisao Necessaria
Escolher qual padrao seguir e atualizar:
- **Opcao A**: Atualizar documentacao para `🔍 Ver Modal` (mantem implementacao)
- **Opcao B**: Atualizar codigo para `📖 Ver em Modal` (segue documentacao)

---

## 2. Light Mode CSS Ausente em Paginas de Modulo

### Problema
Os estilos de light mode (`html:not(.dark)`) estao presentes apenas em algumas paginas.

### Arquivos COM Light Mode CSS
```
✅ curso/trilha1/index.html
✅ curso/trilha2/index.html
✅ curso/trilha3/index.html
✅ curso/trilha1/modulo-1-1.html
✅ curso/trilha1/modulo-1-2.html
✅ curso/trilha1/modulo-1-3.html
✅ curso/trilha1/modulo-1-4.html
```

### Arquivos SEM Light Mode CSS
```
❌ curso/trilha1/modulo-1-5.html
❌ curso/trilha1/modulo-1-6.html
❌ curso/trilha1/modulo-1-7.html
❌ curso/trilha1/modulo-1-8.html
❌ curso/trilha2/modulo-2-1.html
❌ curso/trilha2/modulo-2-2.html
❌ curso/trilha2/modulo-2-3.html
❌ curso/trilha2/modulo-2-4.html
❌ curso/trilha2/modulo-2-5.html
❌ curso/trilha2/modulo-2-6.html
❌ curso/trilha2/modulo-2-7.html
❌ curso/trilha2/modulo-2-8.html
❌ curso/trilha3/modulo-3-1.html
❌ curso/trilha3/modulo-3-2.html
❌ curso/trilha3/modulo-3-3.html
❌ curso/trilha3/modulo-3-4.html
❌ curso/trilha3/modulo-3-5.html
❌ curso/trilha3/modulo-3-6.html
❌ curso/trilha3/modulo-3-7.html
❌ curso/trilha3/modulo-3-8.html
```

### CSS Necessario
Adicionar no `<style>` de cada pagina:

```css
/* Light mode overrides */
html:not(.dark) {
  --bg-primary: #ffffff;
  --bg-secondary: #f3f4f6;
  --text-primary: #111827;
  --text-secondary: #4b5563;
}

html:not(.dark) body {
  background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 50%, #f1f5f9 100%);
}

html:not(.dark) .bg-dark-900 { background-color: #ffffff; }
html:not(.dark) .bg-dark-800 { background-color: #f9fafb; }
html:not(.dark) .bg-dark-700 { background-color: #f3f4f6; }
html:not(.dark) .bg-dark-600 { background-color: #e5e7eb; }
html:not(.dark) .text-white { color: #111827; }
html:not(.dark) .text-neutral-300 { color: #4b5563; }
html:not(.dark) .text-neutral-400 { color: #6b7280; }
html:not(.dark) .border-dark-600 { border-color: #d1d5db; }
html:not(.dark) .border-dark-700 { border-color: #e5e7eb; }
```

---

## 3. Padroes Verificados e CORRETOS

### Alinhamento de Botoes
- Botoes usam `justify-start` conforme documentado ✅

### Link INEMA.CLUB
- Presente em todas as paginas de trilha ✅
- Cor `text-sky-400` ✅

### Numeros em Circulo
- Implementados corretamente com `rounded-full` ✅
- Nao usam setas ✅

### Cores das Trilhas
- Trilha 1: Emerald ✅
- Trilha 2: Blue ✅
- Trilha 3: Purple ✅

---

## 4. Checklist de Correcao

### Prioridade Alta
- [ ] Adicionar light mode CSS em 20 paginas de modulo
- [ ] Decidir padrao do texto do botao modal

### Prioridade Media
- [ ] Verificar consistencia de emojis em titulos
- [ ] Confirmar todas as 3 secoes por topico (O que e, Por que aprender, Conceitos-chave)

### Prioridade Baixa
- [ ] Revisar acessibilidade do toggle de tema
- [ ] Validar HTML em todas as paginas

---

## 5. Processo de Verificacao

Antes de criar nova pagina, verificar:

1. **Navegacao**
   - [ ] Logo 📊 Dashboard Mastery presente
   - [ ] Link INEMA.CLUB em sky-400
   - [ ] Trilhas com nomes corretos
   - [ ] Theme toggle funcional

2. **Estrutura**
   - [ ] Breadcrumb (se aplicavel)
   - [ ] Header com badge, titulo, stats
   - [ ] 6 topicos com numeros em circulo
   - [ ] 3 secoes por topico

3. **Estilos**
   - [ ] Light mode CSS incluido
   - [ ] Cores corretas da trilha
   - [ ] Botoes alinhados a esquerda

4. **Scripts**
   - [ ] Theme toggle script
   - [ ] Modal script (se aplicavel)

---

**Ultima atualizacao:** 2026-01-02
**Versao:** 1.0
