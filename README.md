# 🪟 html-fake-window

> Janelas e diálogos estilo **Windows 11** feitos com HTML, CSS e JavaScript puro — sem frameworks, sem dependências.

---

## 📦 Arquivos do projeto

```
html-fake-window/
├── index.html              ← Desktop principal (WebOS)
├── about.html              ← Janela "Sobre"
├── props.html              ← Janela "Propriedades" (3 abas)
├── demo.html              ← Janela de demonstração
├── dialog-virus.html       ← Diálogo: Aviso de vírus
├── dialog-confirm.html     ← Diálogo: Confirmação de exclusão
├── dialog-success.html     ← Diálogo: Instalação concluída
├── dialog-error.html       ← Diálogo: Erro crítico
└── README.md               ← Este arquivo
```

---

## 🚀 Como usar

### Desktop completo

Abra o `index.html` diretamente no navegador — nenhuma instalação necessária.

```bash
# Ou com um servidor local simples:
npx serve .
python -m http.server 8080
```

### Diálogos individuais

Cada arquivo de diálogo é **completamente independente**. Basta abrir no navegador:

```bash
open dialog-virus.html
open dialog-confirm.html
open dialog-success.html
open dialog-error.html
```

### Incorporar em seu projeto

Copie o HTML de qualquer diálogo e cole no seu projeto. A estrutura básica é:

```html
<div class="dialog" id="dialog">

  <!-- Barra de título -->
  <div class="titlebar" id="titlebar">
    <img src="icon.png" class="titlebar-icon"/>
    <span class="titlebar-text">Título da janela</span>
    <div class="win-btns">
      <button class="wc" onclick="dlg.style.display='none'">─</button>
      <button class="wc">□</button>
      <button class="wc close" onclick="dlg.style.display='none'">✕</button>
    </div>
  </div>

  <!-- Corpo -->
  <div class="dialog-body">
    <div class="dialog-msg">Seu conteúdo aqui.</div>
  </div>

  <!-- Rodapé com botões -->
  <div class="dialog-foot">
    <button class="wbtn">Sim</button>
    <button class="wbtn">Não</button>
  </div>

</div>
```

---

## ✨ Funcionalidades

| Recurso | Suporte |
|---|---|
| Arrastar com mouse | ✅ |
| Arrastar com touch (mobile) | ✅ |
| Minimizar / Maximizar / Fechar | ✅ |
| Duplo clique na titlebar para maximizar | ✅ |
| Resize por borda (S, E, SE) | ✅ |
| Z-index automático por foco | ✅ |
| Animações de abrir / minimizar | ✅ |
| Atalhos de teclado (Enter, Escape) | ✅ |
| Taskbar com toggle minimize/restore | ✅ |
| Menu Iniciar com busca | ✅ |
| Notificações tipo Windows | ✅ |
| Menu de contexto no desktop | ✅ |
| Sem dependências externas | ✅ |

---

## 🎨 Diálogos disponíveis

### ⚠️ `dialog-virus.html` — Aviso de vírus
Simula um alerta de segurança do Windows Defender. Ao clicar em **Remover**, exibe uma tela de confirmação de sucesso.

### 🗑️ `dialog-confirm.html` — Confirmação de exclusão
Diálogo de exclusão permanente com lista de arquivos afetados. Ao confirmar, exibe feedback de conclusão.

### ✅ `dialog-success.html` — Instalação concluída
Tela de conclusão de instalação com barra de progresso, checklist de etapas e botão de reiniciar.

### ❌ `dialog-error.html` — Erro crítico
Mensagem de erro do sistema com código STOP. Possui painel de **Detalhes** expansível com stack trace.

### 📄 `about.html` — Sobre
Janela "Sobre o aplicativo" com logo, versão, badges e lista de funcionalidades.

### ⚙️ `props.html` — Propriedades
Janela de propriedades com **3 abas funcionais**: Geral, Segurança e Detalhes.

---

## 🧩 Classes CSS principais

### Janela
| Classe | Descrição |
|---|---|
| `.dialog` | Container principal da janela |
| `.titlebar` | Barra de título (arrastável) |
| `.titlebar-icon` | Ícone 16×16 na titlebar |
| `.titlebar-text` | Texto do título |
| `.win-btns` | Grupo dos botões de controle |
| `.wc` | Botão de controle (min/max/close) |
| `.wc.close` | Variante vermelha para fechar |

### Conteúdo
| Classe | Descrição |
|---|---|
| `.dialog-body` | Área de conteúdo branca |
| `.dialog-msg` | Texto da mensagem |
| `.dialog-foot` | Rodapé com botões |
| `.wbtn` | Botão estilo Windows |
| `.wbtn.primary` | Botão azul (ação principal) |
| `.wbtn.danger` | Botão vermelho (ação destrutiva) |

---

## 🖱️ Lógica de drag — mouse + touch

Toda janela usa a mesma lógica de arrastar, compatível com mouse e toque:

```javascript
const dlg = document.getElementById('dialog');
const bar = document.getElementById('titlebar');

let drag = false, ox = 0, oy = 0;

// Mouse
bar.addEventListener('mousedown', e => {
  if (e.target.closest('.wc')) return; // Ignora clique nos botões
  drag = true;
  ox = e.clientX - dlg.getBoundingClientRect().left;
  oy = e.clientY - dlg.getBoundingClientRect().top;
  e.preventDefault();
});
document.addEventListener('mousemove', e => {
  if (!drag) return;
  dlg.style.left = Math.max(0, Math.min(e.clientX - ox, innerWidth  - dlg.offsetWidth))  + 'px';
  dlg.style.top  = Math.max(0, Math.min(e.clientY - oy, innerHeight - dlg.offsetHeight)) + 'px';
});
document.addEventListener('mouseup', () => drag = false);

// Touch
bar.addEventListener('touchstart', e => {
  if (e.target.closest('.wc')) return;
  const t = e.touches[0];
  drag = true;
  ox = t.clientX - dlg.getBoundingClientRect().left;
  oy = t.clientY - dlg.getBoundingClientRect().top;
}, { passive: true });
document.addEventListener('touchmove', e => {
  if (!drag) return;
  const t = e.touches[0];
  dlg.style.left = Math.max(0, Math.min(t.clientX - ox, innerWidth  - dlg.offsetWidth))  + 'px';
  dlg.style.top  = Math.max(0, Math.min(t.clientY - oy, innerHeight - dlg.offsetHeight)) + 'px';
}, { passive: true });
document.addEventListener('touchend', () => drag = false);
```

---

## ⌨️ Atalhos de teclado

| Tecla | Ação |
|---|---|
| `Enter` | Confirma / botão primário |
| `Escape` | Cancela / fecha |
| `Ctrl+1` | Abre o Explorador de Arquivos |
| `Ctrl+2` | Abre o Dialog Demo |
| `Ctrl+3` | Abre o README / Sobre |

---

## 🎨 Personalização

### Trocar cor de destaque

Edite a variável CSS `--accent` no `:root` de qualquer arquivo:

```css
:root {
  --accent: #0078d4;       /* Azul Windows (padrão) */
  /* --accent: #107c10;    Verde */
  /* --accent: #8764b8;    Roxo */
  /* --accent: #c42b1c;    Vermelho */
}
```

### Mudar o fundo do desktop

O wallpaper é feito com gradientes CSS puros no `index.html`. Procure o seletor `#desktop` e substitua pelo fundo desejado:

```css
#desktop {
  background: url('seu-wallpaper.jpg') center / cover no-repeat;
}
```

---

## 🌐 Compatibilidade

| Navegador | Suporte |
|---|---|
| Chrome / Edge 90+ | ✅ Completo |
| Firefox 89+ | ✅ Completo |
| Safari 15+ | ✅ Completo |
| Mobile (iOS/Android) | ✅ Touch suportado |

> `backdrop-filter` requer Chrome 76+, Edge 79+ ou Safari 9+. Em navegadores sem suporte, o fundo aparece sólido (sem blur), mas tudo funciona normalmente.



<div align="center">
  Feito com HTML, CSS e JavaScript puro &nbsp;·&nbsp; Estilo Windows 11
</div>
