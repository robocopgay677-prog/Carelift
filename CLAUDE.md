# Carelift

## O que é o projeto

Plataforma de doação humanitária estilo GoFundMe, com foco em
**warm minimalism** + emotional warmth. Inspirado visualmente em
Stripe, Airbnb, Headspace e GoFundMe — mas mais soft, premium e
human-centered.

Quatro páginas estáticas:

- `index.html` — home (hero full-bleed, discover 1+4, editorial CTA,
  urgent campaigns, start a campaign, how it works, impact,
  testimonials, footer CTA, footer)
- `campaign.html` — detalhe da campanha (carrossel de imagens,
  progresso em anel, donate + share, descrição colapsável,
  doações, words of support, organizer, share circles, promo fan,
  sidebar sticky no desktop, trust block, showcase rail)
- `donate.html` — fluxo de doação (hero editorial da campanha, grid
  de valores com "SUGGESTED", custom amount, ticker rotativo, botão
  grande de submit, protected card, showcase rail)
- `thanks.html` — confirmação (card centralizado, halo coral
  animado + sparkles, chip do valor doado vindo do localStorage)

## Stack e estrutura

- **HTML + CSS + JavaScript vanilla** — sem framework, sem build,
  sem npm install
- **Multi-arquivo**: cada página é um `.html` standalone com `<style>`
  e `<script>` inline no próprio arquivo (NÃO é arquivo único como
  FellowHope)
- **Backend**: nenhum. Toda a "data" (campanhas, doações, testemunhos)
  está hardcoded direto no HTML. Não há Supabase, API, ou banco
- **Imagens**: dois logos PNG na raiz (`Logo-Icon.png`,
  `Logo-Icontext.png`), backups originais em `_orig/` (gitignored).
  Fotos: URLs do Unsplash com `?auto=format&fit=crop&w=...&q=80`
- Sem testes automatizados, sem lint. Validação manual no browser

## Como rodar

```
python3 /Users/rafaelromualdo/.claude/serve-carelift.py
```

Abre em `http://localhost:4322`. O entry está em
`/Users/rafaelromualdo/.claude/launch.json` com o nome `carelift`.

**Produção**: deploy automático no Vercel a cada push pra `main`.

- Repo: <https://github.com/robocopgay677-prog/Carelift>
- Live: <https://carelift-project.vercel.app>

## Arquitetura — pontos-chave

- **Design system**: todas as cores, fontes, raios, sombras e
  espaçamentos são variáveis CSS no `:root`, no topo do `<style>` de
  cada arquivo. Os tokens estão DUPLICADOS em cada página por design
  (single-file pattern por arquivo)
- **Navegação**: páginas HTML reais, não overlays, não SPA. Links
  entre páginas são `<a href="campaign.html">` etc. Links pra seções
  da home a partir de outras páginas: `<a href="index.html#campaigns">`
- **Drawer menu (hamburger)**: drawer lateral de 420px (340px no
  mobile) com backdrop semitransparente. Backdrop tem
  `pointer-events:none` quando fechado pra não bloquear cliques.
  Conteúdo: header (logo + close), três seções agrupadas
  (**EXPLORE** / **GET INVOLVED** / **COMPANY**) com label coral
  curtinha + barrinha accent, e bottom CTA sticky "Donate Now"
- **Hero (só no index)**: foto full-bleed com gradient coral wash de
  cima pra baixo, texto em 3 linhas alinhado à esquerda no canto
  superior (última linha italic + highlight coral), 1 CTA único
- **Cards de campanha**: pattern `.dc-featured` (grande, span 2 rows)
  + 4× `.dc-small` num grid 1.1fr 1fr 1fr. Heart button e avatar pile
  (só em featured) são INJETADOS via JS, não vivem no HTML
- **Filter dropdown** (Discover section): JS-driven. Click no pill
  abre `.disc-filter-box`, click numa categoria atualiza o label

## Detalhes que confundem — ler antes de mexer

- ⚠️ A nav é sticky e fica SOBREPOSTA ao hero no `index.html` via
  `margin-top:-104px` na `.hero` + `padding-top:104px` que compensa.
  Não mexer nesses números isoladamente — sempre ajustar os dois juntos
- ⚠️ A mesma estrutura de **nav + drawer menu** está replicada em
  `index.html`, `campaign.html` e `donate.html`. Se mudar em uma,
  replique nas outras duas. `thanks.html` é página terminal, NÃO tem nav
- ⚠️ Os logos foram redimensionados de 1536×1024 → 384×256 via `sips`.
  Os originais ficam em `_orig/` (gitignored). Se precisar reupload em
  alta, restaurar de lá
- ⚠️ No footer (fundo escuro `#1A1D20`), o `Logo-Icontext.png` recebe
  `filter: brightness(0) invert(1)` pra ficar branco. Se trocar por
  outro logo já branco, REMOVER o filter
- ⚠️ Os cards de campanha têm click handler em JS que navega pra
  `campaign.html`. O botão de heart usa `e.preventDefault()` +
  `e.stopPropagation()` pra não disparar essa navegação. Se mexer no
  card, preservar esse padrão
- ⚠️ O servidor Python local (`serve-carelift.py`) já teve bugs de
  `translate_path` no passado. A versão atual usa `os.chdir` +
  handler padrão e é estável. Se mexer no script, testar com `curl`
  antes de assumir que funciona
- ⚠️ O breakpoint responsivo principal é **700px** (não 900px como em
  FellowHope). Há também breaks em 1100, 900, 600 e 480 px
- ⚠️ `thanks.html` lê `localStorage.getItem('carelift:pending')` pra
  mostrar o valor doado. Se mexer no fluxo de `donate.html`, garantir
  que o `setItem('carelift:pending', JSON.stringify({v: amount, ...}))`
  continua sendo chamado antes do redirect

## Idioma

- **Texto visível na tela** (botões, labels, copy): **inglês**
- **Comentários no código**: **inglês** (convenção estabelecida no
  codebase desde o início — mantida por consistência)
- **Mensagens de commit**: inglês
- **Conversa com Claude**: português
- Não traduzir conteúdo existente nem comentários sem pedido explícito

---

# REGRAS DE EDIÇÃO

## Como editar estes arquivos

- `index.html` tem ~1700 linhas, `campaign.html` ~870, `donate.html`
  ~580, `thanks.html` ~360. NUNCA reescreva um arquivo inteiro.
  Faça edições pequenas e localizadas (`Edit` pontual)
- Antes de editar, leia o trecho ao redor pra entender contexto. CSS,
  HTML e JS estão todos no mesmo arquivo — uma mudança no `<style>`
  pode afetar qualquer parte
- Depois de qualquer edição, confira: as tags HTML abrem/fecham
  corretamente? As chaves `{}` do CSS e do JS estão balanceadas?
- Mudou nav, footer, ou drawer menu? Replicar nos 3 arquivos
  principais (index, campaign, donate)

## HTML / CSS

- O site é controlado por `id` no JavaScript. Antes de adicionar um
  elemento com `id`, confirme que não existe ainda — id duplicado
  quebra `getElementById` silenciosamente
- Reutilize classes existentes (`.btn-primary`, `.cc`, `.dc-featured`,
  `.dc-small`, `.mm-link`, `.how-card`, `.testi-card`, etc.) em vez de
  criar novas. Só crie classe nova se realmente não houver equivalente
- TODA cor, raio, sombra e espaçamento vem das variáveis do `:root`.
  Nunca use valores hardcoded. Se precisar de um valor que não existe
  como variável, crie a variável primeiro

## Design tokens — paleta Carelift coral (NÃO MUDAR)

A identidade Carelift é definida por essas variáveis no `:root` de
cada arquivo. **NUNCA** alterar esses valores sem permissão explícita:

```css
:root{
  /* Brand */
  --coral:       #FF7452;   /* primary, CTAs, gradients */
  --coral-dark:  #F06543;   /* hover, gradient end */
  --coral-soft:  #FFC2B2;   /* soft glow, hover borders */
  --coral-tint:  #FFE9E1;   /* hover bg, eyebrow chips */

  /* Surfaces */
  --paper:       #FFF8F4;   /* body background */
  --white:       #FFFFFF;   /* card backgrounds */
  --surface:     #F9EEE8;   /* light section bg */
  --line:        #E7D6CC;   /* borders, dividers */

  /* Text */
  --ink:         #2D3135;   /* primary text */
  --slate:       #6A7C89;   /* secondary text */
  --muted:       #7E736D;   /* muted/tertiary text */

  /* Type */
  --font-head: 'Plus Jakarta Sans', ...;
  --font-body: 'Inter', ...;
}
```

**Proibido**: amarelo, dourado, neon, cores saturadas, serifas
decorativas, tom religioso/vintage, beige-heavy palettes.

## Espaçamento vertical

O Carelift respira mas sem inflar — equilíbrio premium:

- Padding vertical de seção: **88-96px** desktop, **56-64px** mobile
- Padding horizontal de seção: sempre **5%**
- Título a ~16-20px do conteúdo que o segue
- Cards: gap de **18-22px** entre eles
- Cantos arredondados: **12-20px** para containers, **100px** (pill)
  para botões
- Sombras: usar os tokens `--s-xs / --s-sm / --s-md / --s-lg` quando
  existirem; caso contrário, sombras coral suaves
  (`rgba(255,116,82,.10-.18)`)
- Se algo "precisa de mais espaço", o problema provavelmente é outro
  (tipografia, line-height, hierarquia). Não resolva com mais espaço
  vazio

## Navegação entre páginas

- Páginas HTML reais — não SPA, não overlays, não hash routing
- Links entre páginas: `<a href="campaign.html">`, etc.
- Links pra seções da home a partir de outras páginas:
  `<a href="index.html#campaigns">`, `<a href="index.html#how">`
- O **drawer menu** é o mesmo nas 3 páginas (index, campaign, donate)
  — replicar nas 3 ao mudar
- `thanks.html` NÃO tem nav nem hamburger menu (é terminal de
  celebração)
- Cards de campanha SEMPRE linkam para `campaign.html`
- CTAs "Start Helping" / "Donate Now" SEMPRE linkam para `donate.html`
- O search button (ícone lupa) na nav linka pra `#campaigns` (ou
  `index.html#campaigns` em páginas internas). Não tem search real
  ainda — é placeholder
- Fluxo a preservar a todo custo:
  `index → campaign → donate → thanks → index`

## Imagens e assets

- `Logo-Icon.png` (384×256, ~18KB): logo só ícone, vai na nav
  centralizada
- `Logo-Icontext.png` (384×256, ~94KB): logo com texto, vai no footer
  (com `filter:invert(1)`) e no header do drawer menu (sem filter)
- Originais 1536×1024 ficam em `_orig/` (gitignored). Restaurar de lá
  se precisar
- Fotos: URLs de `images.unsplash.com/photo-XXX?auto=format&fit=crop&w=600&q=80`
- Hero usa `w=1800&q=80` (alta qualidade pra full-bleed)
- Cards usam `w=600&q=80`
- `loading="lazy"` em fotos abaixo do fold, `loading="eager"` +
  `fetchpriority="high"` no logo da nav e no hero
- NÃO introduzir frameworks de imagem, libs de lazy-loading, ou
  Next/Image. Vanilla `<img>` basta

## Deploy

- Push para `main` no GitHub → auto-deploy no Vercel
- URL de produção: <https://carelift-project.vercel.app>
- **SEMPRE pedir confirmação antes de `git push`** — push é
  destrutivo no remoto público e dispara Vercel
- Commits: usar HEREDOC pra preservar formatação:

  ```bash
  git commit -m "$(cat <<'EOF'
  Short subject line

  Longer body explaining the change.

  Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
  EOF
  )"
  ```

- Co-author Claude sempre presente nas mensagens

## Escopo

- Faça exatamente o que foi pedido. Não refatore, modernize,
  reorganize ou "limpe" código que não faz parte da tarefa
- Não adicione frameworks, build tools, `package.json`, módulos ES
  ou dependências. O projeto é vanilla e multi-file standalone por
  decisão
- Não mova CSS/JS para arquivos separados sem pedido explícito (cada
  página tem seu próprio bloco inline)
- Não crie arquivos novos (componentes, partials, helpers) sem
  necessidade clara
- Se notar um problema fora do escopo, avise — mas não conserte sem
  perguntar

## Antes de finalizar qualquer mudança

- Rode o server local (`python3 ~/.claude/serve-carelift.py` ou
  via launch.json) e descreva o que testar no browser
- Teste mentalmente mobile E desktop. Breakpoints principais:
  **1100, 900, 700, 600, 480** px
- Se mexeu em JS, confira o console por erros (`getElementById`
  null é o sintoma mais comum)
- Se mexeu em uma página, verifique se a mesma mudança precisa ir
  nas outras (nav, drawer menu, footer são replicados em 3 arquivos)
- Mudou algo grande? Liste o que mudou e por quê, em vez de só
  dizer "pronto"
