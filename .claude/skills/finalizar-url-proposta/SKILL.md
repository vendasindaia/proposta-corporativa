---
name: finalizar-url-proposta
description: Continua e finaliza a URL da proposta corporativa interativa da Indaiá — a página que veste um orçamento real do CRM (via token) e que o cliente recebe para revisar cardápios, convidados e layout. Use quando pedirem para "finalizar a URL", "continuar a proposta corporativa", "espelhar os valores do orçamento", "colocar a logo do cliente" ou qualquer melhoria em index.html ligada à proposta.
---

# Finalizar a URL da proposta corporativa

Este repo é a **página da proposta corporativa** da Indaiá Eventos: um único
`index.html` (vanilla, sem framework, ~500 KB) publicado no GitHub Pages.
Ela tem dois modos:

- **Demo** (sem parâmetros): a proposta-modelo da DIMY — showroom completo.
- **Proposta real** (`?proposta=TOKEN&espaco=slug&api=BASE`): a página busca o
  orçamento na rota pública do CRM (`GET {api}/api/public/proposta/{token}`)
  e "se veste" com ele — função `vestirPropostaCRM()` no fim do script.

O fluxo de negócio inteiro: a atendente clica **"Sugerir orçamento (IA)"** na
conversa do indachat → a IA lê a conversa e sugere → ela revisa e cria o
rascunho no CRM → a resposta já traz a `url_proposta` (token com validade de
7 dias) → esse link vai pro cliente. Tudo do lado do CRM/chat está pronto;
**o trabalho desta skill é a PÁGINA**.

## O que JÁ funciona (não refazer)

- `vestirPropostaCRM()`: cliente/número/data/convidados/validade aplicados;
  menu, coquetel e open bar selecionados casando os itens do orçamento POR
  NOME com os pacotes da página; dia da locação derivado da data; estado
  salvo por token (`CHAVE` com sufixo `_t…`); falhou o fetch → segue demo,
  nunca quebra na frente do cliente.
- Identidade: topo, título da aba, telão de LED do 3D (canvas — desenha o
  nome do cliente com fonte que encolhe até caber; globais `TELAO_TITULO` /
  `TELAO_SUB`), CTA final (troca o render da DIMY pela 1ª foto real da
  galeria), nome do arquivo do tour gravado.
- 5 ambientes calibrados (`?espaco=`): salao_eventos, solar, mezanino,
  mirante, canto_lagoa — abas por salão, máscaras de piso, maquete 3D, tour.
- Alça de teste: `window.__PROPOSTA_CRM__` ({numero, cliente, telao, dia}).

## Decisões CONGELADAS do dono (não rediscutir, não desfazer)

1. **Preço nunca nasce na página** — todo valor vem do CRM. A página exibe.
2. **Desconto NUNCA aparece** nem viaja pelo link (nem "de X por Y").
3. **Data real e disponibilidade nunca são expostas** — mudar data/dia é com
   o consultor. O card de simular dia da semana está `hidden` de propósito.
4. **Material de um cliente jamais aparece na proposta de outro** — sem a
   identidade do cliente, o fallback é a marca Indaiá. A demo DIMY só existe
   sem token.
5. Vanilla + scroll nativo (Lenis reprovado), paleta neutra, Cormorant
   Garamond + Montserrat self-hosted, nada lento (lazy no 3D/360).
6. Validade da proposta: **7 dias** (o CRM gera o token assim).

## A FILA — o que falta para "finalizar a URL" (em ordem)

1. **Espelho fino dos valores** (a maior pendência). Hoje a página soma o
   modelo demo COMPLETO (locação de R$ 20 mil, coquetel padrão, extras,
   taxas próprias) por cima das escolhas reais — um orçamento de R$ 25 mil
   abre mostrando R$ 136 mil. Na proposta real ela deve exibir **exatamente
   e somente o que está no orçamento**: itens de `orcamento.itens[]` (com
   `valor_unitario`/`valor_total` de lá), serviços de `orcamento.servicos[]`
   (`valor_calculado` — é onde vive a Locação real), total = `valor_total`
   do CRM. Bloco que o orçamento não tem não aparece (sem coquetel default).
   Estratégia: um modo "real" nas funções de cálculo/render que leia os
   dados do snapshot, mantendo a demo intacta. Trocas do cliente (outro
   menu/open bar) recalculam localmente com os preços por pessoa da página
   e o aviso "sujeito a confirmação do consultor" — o CRM revalida depois.
2. **Logo do cliente em imagem** no telão/topo: buscar pelo CNPJ público
   (BrasilAPI/minhareceita → site → favicon/logo) com fallback de upload
   pela equipe; hoje é o nome em tipografia (aprovado como v1).
3. **Cardápios**: conferir os níveis/preços hardcoded (MENUS/COQUETEIS/
   OPENBARS) contra o catálogo vigente do CRM — o casamento por nome só
   funciona se os nomes baterem.
4. **"Confirmar os ajustes"** deve registrar a decisão do cliente no CRM
   (rota pública de confirmação — não existe ainda; combinar o contrato com
   o Adriano antes: whitelist de campos, teto por capacidade, rate-limit).
5. **Deploy oficial**: preencher `API_CRM_OFICIAL` (constante no topo do
   script — hoje vazia; o parâmetro `api` só é aceito em localhost, de
   propósito) e o domínio `proposta-corporativa.eventosindaia.com.br`.

## Como testar (regra da casa: nada se entrega no olho)

- Sirva a página local: `python -m http.server 8140` na raiz do repo.
- Peça ao Adriano um **link de teste** (ele gera um orçamento de rascunho e
  te passa a URL completa com `?proposta=…&espaco=…&api=…`). Você não
  precisa de acesso ao CRM — a rota do token é pública.
- Prova automatizada: `node ferramentas/probe-proposta-url.mjs "<url>"` —
  abre em Chrome headless e confere nome, data, convidados, seleções, CTA e
  erros de JS. Ajuste o caminho do puppeteer no topo se necessário
  (`npm i puppeteer` numa pasta qualquer e aponte o createRequire).
- As baterias antigas continuam obrigatórias quando mexer no editor de
  mesas: `ferramentas/testar-mascara.mjs` e `ferramentas/smoke-pisos.mjs`.
- Antes de subir servidor local, confira se a porta já está ocupada e mate
  o processo anterior — servidor velho servindo página velha é a armadilha
  nº 1 desta casa.

## Publicação

Push na `main` do repo (GitHub Pages via Actions, cache ~10 min). SEMPRE:
carimbo de versão novo no rodapé (`#versaoBuild`, padrão `v18.09-N`) e
conferir a URL publicada com `?vN=` de cache-buster + a prova headless.
Nunca publique com bateria vermelha.

## Fronteiras — o que NÃO é trabalho desta skill

- **O 3D é território da GRAZI (arquiteta).** Calibrar ambiente novo,
  converter export do SketchUp, cadastrar item decorativo, gerar máscara e
  planta — nada disso é da Dani. As skills `calibrar-ambiente` e
  `cadastrar-item` que vivem neste mesmo repo são DELA; não as invoque, não
  mexa em `assets/ambientes/`, `assets/itens/` nem nas calibrações. A URL
  apenas **consome** as maquetes prontas (e vincula: o layout do orçamento
  abre na maquete do espaço certo). Precisa de algo no 3D — um ambiente que
  falta, uma textura ruim, um item novo? Anote e avise o Adriano, que
  encaminha pra fila da Grazi.
- O botão do chat, as rotas do CRM (`/external/orcamentos`, token, catálogo)
  e o painel da atendente vivem no crm-backend/indachat — mudanças lá são do
  Adriano. Se a página precisar de um dado que o snapshot não traz, anote o
  campo que falta e peça; não invente contorno do lado de cá.
