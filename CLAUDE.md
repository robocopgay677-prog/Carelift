# Carelift

## O que é o projeto

Plataforma de doação humanitária estilo GoFundMe, com foco em
**warm minimalism** + emotional warmth. Inspirado visualmente em
Stripe, Airbnb, Headspace e GoFundMe — soft, premium, human-centered.
A linguagem visual evoluiu para um tema **candlelight** (luz de vela):
coral continua sendo a marca, com âmbar/dourado entrando apenas como
LUZ (glow, brilho, chama do progresso).

Páginas estáticas:

- `index.html` — home (hero, discover 1+4, editorial CTA "Your story",
  urgent campaigns, start a campaign, how it works, impact,
  testimonials, footer CTA, footer). **No mobile o hero é a ilustração
  de uma vela (`hero-mobile.jpg`) com animação** (glow pulsante +
  brasas subindo + ondas de calor).
- `campaign.html` — detalhe da campanha (carrossel, anel de progresso,
  donate + share, descrição colapsável, doações, words of support,
  organizer, share circles, promo fan, sidebar sticky desktop, sticky
  donate bar mobile, showcase rail). **Populada via `?c=<índice>`.**
- `donate.html` — fluxo de doação. Hero image-led: imagem full-bleed
  (mobile) com badge "YOU'RE SUPPORTING" + título sobre a foto, e um
  **card de doação coral sobreposto** (sai da base da imagem). Grid de
  valores, custom amount, ticker, submit, protected card, showcase.
  **Populada via `?c=<índice>`.**
- `campaigns.html` — listagem de todas as campanhas (grid `.dc-small`),
  com filtro por categoria. Renderiza cards do array `CAMPAIGNS`.
- `start-campaign.html` — criar campanha. Form de 4 passos (About You,
  The Campaign, The Story, Review) + editor rico (paste de imagem),
  upload tiles, success state inline. **Sem backend: salva em
  localStorage** (`carelift:campaign:<slug>`).
- `terms.html` — Terms of Use (página legal editorial: hero
  candlelight, TOC fixo com scrollspy no desktop, seções em prosa).
- `thanks.html` — confirmação (card central, halo coral animado +
  sparkles, chip do valor doado do localStorage). Página terminal,
  SEM nav.

## Stack e estrutura

- **HTML + CSS + JS vanilla** — sem framework, sem build, sem npm
- **Multi-arquivo**: cada `.html` é standalone com `<style>` e
  `<script>` inline. Tokens, nav, drawer e footer são DUPLICADOS em
  cada página por design (single-file pattern por arquivo)
- **Backend**: nenhum. Toda "data" é hardcoded. O array `CAMPAIGNS`
  (12 campanhas) vive em `campaigns.html`, `campaign.html` e
  `donate.html` (duplicado). Sem Supabase/API/banco
- **Imagens**: logos PNG na raiz (`Logo-Icon.png`, `Logo-Icontext.png`);
  `hero-mobile.jpg` (vela). Fotos via Unsplash `?auto=format&fit=crop&w=...&q=80`

## Preview local (IMPORTANTE — workaround de sandbox)

O sandbox do helper do Claude não acessa `~/Documents/` (TCC do macOS).
Por isso o preview é servido de **`~/.claude/carelift-preview/`**, que
contém **HARDLINKS** (não symlinks — TCC é por path) dos arquivos reais.

- Server: `python3 ~/.claude/serve-carelift.py` na porta **4322**.
  Entry em `~/.claude/launch.json` com o nome `carelift` (é o default).
- **Depois de editar QUALQUER arquivo, re-linkar antes de o usuário ver:**
  ```bash
  cd /Users/rafaelromualdo/Documents/CareliftProject
  for f in index.html campaign.html campaigns.html donate.html thanks.html start-campaign.html terms.html hero-mobile.jpg; do
    rm -f ~/.claude/carelift-preview/"$f"; ln "$f" ~/.claude/carelift-preview/"$f"
  done
  ```
- A ferramenta de screenshot do preview às vezes captura do topo mesmo
  após scroll — confiar em `preview_eval` (medições do DOM) quando o
  screenshot não refletir o scroll.

## Deploy

- Push para `main` no GitHub → auto-deploy no Vercel
- Repo: <https://github.com/robocopgay677-prog/Carelift>
- Live: <https://carelift-project.vercel.app>
- **SEMPRE pedir confirmação antes de `git push`** (afeta repo público +
  dispara Vercel). Autorização de commit NÃO vale para push
- Commits via HEREDOC, com co-author do Claude no fim

---

# DESIGN SYSTEM

Todas as cores, fontes, raios, sombras e gradientes são variáveis CSS
no `:root` de cada arquivo (duplicadas por arquivo). Camadas:

## 1) Marca coral (base)

```css
--coral:#FF7452; --coral-dark:#F06543; --coral-soft:#FFC2B2; --coral-tint:#FFE9E1;
--paper:#FFF8F4; --white:#FFFFFF; --surface:#F9EEE8; --line:#E7D6CC;
--ink:#2D3135; --slate:#6A7C89; --muted:#7E736D;
--font-head:'Plus Jakarta Sans'; --font-body:'Inter';
```

## 2) Premium layer (aditivo)

```css
--coral-deep:#D9492A; --coral-amber:#FFB48F; --peach-glow:#FFD8C7;
--grad-btn / --grad-btn-hover   /* botões: gradiente vertical layered */
--grad-progress                 /* barra de progresso (peach→coral→deep) */
--grad-warm / --grad-warm-side  /* atmosferas radiais */
--s-card / --s-card-hover       /* sombras coral-tinted em camadas */
--s-btn / --s-btn-hover / --s-glow
```

## 3) Candlelight layer (aditivo — luz/atmosfera)

```css
--amber:#E5A94B; --candle-gold:#F2C66D; --honey:#E9B95C;
--grad-flame   /* candle-gold→coral→deep — usado nas progress bars/anel */
--glow-candle; --s-candle;
```

## Regras de cor

- **Coral é a cor primária da marca** (CTAs, links, títulos de destaque,
  badges). Não trocar o coral por outra cor primária sem o usuário pedir.
- Âmbar / candle-gold / honey entram como **LUZ**: glow de chama, halos
  candlelight, brilho, e o gradiente das progress bars (`--grad-flame`).
  Não usar como preenchimento chapado que compita com o coral.
- O usuário já autorizou adicionar tons novos quando for pra elevar o
  premium (ex.: card de doação coral "luxury", warm-whites). Manter tudo
  na **família quente** (coral/âmbar/cream). Evitar neon, frio, saturado.
- Toda cor/raio/sombra vem de variável. Se precisar de um valor novo,
  criar a variável (ou usar valor inline quando for localizado a 1 componente).

## Componentes-chave

- **Botões primários**: `--grad-btn` + highlight interno (`::before`
  branco) + glow embaixo (`::after`). Hover: `--grad-btn-hover` + lift.
- **Progress bars / anel**: `--grad-flame` (gradiente "chama crescendo").
- **Cards** (`.dc-featured`, `.dc-small`, `.how-card`, `.testi-card`):
  sombra `--s-card`, hover lift + `--s-card-hover` + borda coral-soft.
- **Drawer (hamburger)**: NÃO é mais lista de links gigantes numerados.
  Agora são 3 seções (How to Help / Company / Legal) com eyebrow coral
  (dot gradiente) + links com bullet coral-soft que deslizam no hover +
  divisores tracejados. `.mobile-menu-nav` precisa de `align-items:stretch`
  e `.mm-section`/`.mm-divider` com `width:100%` (senão centralizam e
  desalinham). Replicado em index, campaign, campaigns, donate,
  start-campaign, terms.
- **Atmosfera**: body de várias páginas tem radiais warm fixos; seções
  com glows direcionais; faixa de impacto e painéis coral com ember âmbar.

---

# ARQUITETURA / COMPORTAMENTOS

## Roteamento por campanha (`?c=<índice>`)

- O array `CAMPAIGNS` (12 itens: `title, org, loc, cat, img, raised,
  goal`) está duplicado em `campaigns.html`, `campaign.html`, `donate.html`.
- **TODO card de campanha leva a `campaign.html?c=<índice>`** — cards do
  index (Discover/Urgent), grid do campaigns.html, e as showcase rails
  (`.cs-card`) de campaign.html e donate.html. Nunca deixar
  `href="campaign.html"` sem `?c`.
- `campaign.html` lê `?c`, busca no array e popula título, imagem,
  organizer+iniciais, categoria, raised/goal/%, anel, sticky bar, nº de
  doadores e uma história templated. Default = campanha 0.
- Os botões "Donate" do campaign.html passam o `?c` adiante; `donate.html`
  popula o hero da campanha a partir do `?c` (mesma técnica).
- Doadores são derivados: `Math.max(9, Math.round(raised/68))`.

## Navegação

- Páginas HTML reais (não SPA). Links entre páginas: `campaign.html?c=N`,
  `donate.html?c=N`, `campaigns.html`, `start-campaign.html`, `terms.html`.
  Links pra seções da home: `index.html#how`, `#impact`, `#stories`.
- Nav + drawer + footer replicados em index, campaign, campaigns, donate,
  start-campaign, terms. `thanks.html` é terminal (sem nav).
- Links "Terms" (drawer Legal + footer) → `terms.html`. "Privacy" ainda
  é placeholder `#` (página de privacidade não existe ainda).

## Detalhes que confundem — ler antes de mexer

- ⚠️ No `index.html` a nav é sticky e sobrepõe o hero desktop via
  `margin-top:-104px` + `padding-top:104px`. No **mobile (≤700px)** o hero
  vira a vela: `margin-top:0`, full-bleed, e a camada de motion
  `.hero-flames` (glow/embers/heat) só aparece ≤700px.
- ⚠️ `donate.html`: o hero é full-bleed no mobile via
  `left:50%;width:100vw;margin-left:-50vw` + `body{overflow-x:clip}` (guard
  contra scroll lateral). O card `.dn-hero-progresso` é irmão do `.dn-hero`,
  puxado com `margin-top` negativo + z-index pra sobrepor a imagem.
- ⚠️ `thanks.html` lê `localStorage.getItem('carelift:pending')`. Se mexer
  no fluxo de donate, garantir que o `setItem` continua antes do redirect.
- ⚠️ No footer (fundo escuro), `Logo-Icontext.png` usa
  `filter:brightness(0) invert(1)` pra ficar branco.
- ⚠️ Cards de campanha têm heart button com `preventDefault` +
  `stopPropagation` pra não disparar a navegação do card.
- ⚠️ Breakpoint principal: **700px**. Também 1100, 900, 600, 480.
- ⚠️ Animações respeitam `prefers-reduced-motion` (candle hero, terms hero).

## Idioma

- Texto visível, comentários no código e commits: **inglês**
- Conversa com o usuário: **português**
- Não traduzir conteúdo/comentários existentes sem pedido

---

# REGRAS DE EDIÇÃO

- Arquivos grandes (index ~2000 linhas). **Nunca reescrever um arquivo
  inteiro** — edições pontuais (`Edit`). Para mudanças repetidas em vários
  arquivos, um script Python pontual em `/tmp` é OK (e checar chaves
  `{}` + tags `<script>`/`<style>` balanceadas depois).
- Reutilizar classes/variáveis existentes. Só criar classe nova se não
  houver equivalente.
- Mudou nav, drawer ou footer? Replicar nos arquivos que os têm.
- Depois de editar: re-linkar o preview, conferir balanceamento, testar
  mentalmente mobile E desktop, e verificar no preview (`preview_eval`/
  screenshot).
- Escopo: fazer o que foi pedido. Não refatorar/limpar fora do pedido.
  Não adicionar frameworks/build/deps. Não mover CSS/JS pra arquivos
  separados (cada página é inline por decisão). Não criar arquivos sem
  necessidade clara.
- Mudança grande? Listar o que mudou e por quê, em vez de só "pronto".
