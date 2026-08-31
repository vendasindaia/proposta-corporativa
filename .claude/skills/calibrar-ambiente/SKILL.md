---
name: calibrar-ambiente
description: Converte um export do SketchUp (.dae + texturas) num ambiente calibrado do sistema de Layouts 3D da Indaiá — maquete GLB, plantas por aba, máscaras de piso, registro no editor e itens decorativos (props). Use quando chegar modelo novo ou melhoria de um ambiente/item da arquiteta, ou quando pedirem para "calibrar", "cadastrar ambiente", "ajustar maquete" ou "converter item decorativo".
---

# Calibrar ambiente / item decorativo — Layouts 3D Indaiá

Este repo é a **base central de layouts** da Indaiá Eventos: cada ambiente vive em
`assets/ambientes/<slug>/` (maquete GLB + plantas + máscaras + calibração) e é
registrado em `index.html` (objeto `AMBIENTES`). Quem consome: a URL da proposta
corporativa (esta página), a aba **Maquetes 3D** do CRM (`/operacional/layouts`)
e, nas próximas fases, o Builder e o evento. O CRM guarda um espelho do registro
(tabela `espaco_maquetes`) — o passo do CRM é INTERNO da equipe (fim deste guia).

A referência técnica completa do pipeline (scripts, parâmetros, exemplos reais)
é `assets/ambientes/README-pipeline.md` — leia antes da primeira conversão.
Este skill é o roteiro de ponta a ponta por cima dela.

## 0 · O que precisa chegar do SketchUp (checklist do export)

Por AMBIENTE:
1. **`.dae` (COLLADA)** exportado do SketchUp — padrão `unit 0.0254` (polegada) e
   `Z_UP`; o conversor já corrige para metros/Y-up. Exportar **"Layout Vazio"**
   (sem mesas/cadeiras de evento — as mesas são do sistema).
2. **Pasta de texturas** com o MESMO nome do arquivo (ex.: `Mirante Vazio/`),
   zipada. Sem ela o modelo sai em cores chapadas. O SketchUp troca acentos por
   `_` nos nomes (`Aço_01` → `A_o_01`) — o pipeline resolve, não renomeie.
3. **Todos os pavimentos** no mesmo export (ou um .dae por pavimento, MESMA
   origem do SketchUp) e **as escadas modeladas** — sem escada, o encaixe de um
   piso sobre o outro vira hipótese documentada (aconteceu no Mirante).
4. Planta cotada (PDF do CAD) quando houver — é a conferência de escala.
5. Vistas Enscape (links) ajudam como referência e viram panoramas depois.
6. Não deixar "bloco corrompido" de fora sem avisar (a árvore do Solar veio
   à parte depois — funcionou, mas custa uma rodada extra).

Por ITEM DECORATIVO (mesa, árvore, pérgola, palco, lounge...):
- `.dae` do item ISOLADO + pasta de texturas; de preferência com uma medida
  conhecida (ex.: tampo 1,60 m) para conferir a escala.

## 1 · Pipeline do ambiente (resumo operacional)

Pasta de trabalho: qualquer temp; copie `assets/ambientes/_pipeline/` (scripts +
`vendor/` + `node_modules/`) para ela. Precisa de Node 18+, Chrome instalado e
os pacotes `puppeteer`/`sharp` acessíveis (na máquina da Indaiá eles vêm de
outro projeto via `createRequire` — ajuste o caminho no topo dos scripts para o
seu ambiente, ou `npm i puppeteer sharp` na pasta de trabalho).

1. `preprocess-tex.mjs <arq.dae> <pasta-extraída> <pasta-com-nome-do-dae>` —
   comprime texturas (≤1024 px; `G2048=` p/ pisos grandes) mantendo os nomes.
2. `serve.mjs <porta>` — servidor local (o conversor roda no Chrome headless).
3. `run-conv.mjs <arq.dae> raw-analise.glb cfg-analise.json <porta>` — conversão
   de ANÁLISE. Ler `raw-analise-info.json`: histograma `pisos` (níveis de piso),
   `mergeReport` (cenografia pesada escondida — cordões de luz instanciados já
   somaram 4–32 MILHÕES de triângulos), bbox (sobras fora do prédio).
4. Escrever `cfg-<slug>.json`: `removeMaterials`, `removeInstanciadas`,
   `removeRegioes`, `moverBlocos` (bake), `recenterFixed` (origem = centro XZ do
   piso principal; **Y nunca se mexe**), `simplify` (NUNCA piso/parede/escada/
   guarda-corpo/vidro), `corPorMaterial` p/ material sem textura.
5. Conversão FINAL + `gltfpack -cc` (+`-vtf` se o log avisar de UV). Alvos:
   **GLB ≤ 10 MB, ≤ 350 k triângulos** (os 5 ambientes atuais: 0,6–20 MB).
6. Plantas e máscaras POR ABA (ver §2): `planta*.jpg` 1920×1080 = render
   ortográfico de topo DO PRÓPRIO GLB com corte na altura certa (tira o
   telhado daquele ambiente); `mascara*.b64` = piso caminhável em grade de
   4 px (480×270, bitpack base64, erosão 0,4 m) via `gerar-mascara.mjs`.
7. `calibracao.json` no formato dos existentes (copie o de
   `assets/ambientes/mirante/`): pxm, glbcal, pisos[], áreas em px, porta de
   entrada, escada, níveis, remoções, conferência com o CAD, ressalvas.
8. `provas/`: screenshots conferidos (dollhouse, interiores, máscara sobre a
   planta, topo sem corte). **Nada se entrega sem olhar as provas.**

## 2 · Decisões de produto (congeladas — não rediscutir por ambiente)

- **Uma ABA por ambiente/nível** (padrão aprovado 27/08): Solar = Pátio/Hall/
  Balada; Mezanino = Térreo/Deck; Mirante = Salão/Hall/Lounge; Canto da Lagoa =
  Salão/Deck/Cerimônia. Sem títulos desenhados dentro da planta — a aba
  identifica. Cada aba tem planta + máscara PRÓPRIAS na MESMA calibração
  (pxm/glbcal únicos por ambiente).
- **Recorte por aba com ~4 m de contexto** ao redor do ambiente (`recorte`).
- **Mesa oficial**: redonda, tampo 1,60 m, 8 lugares, footprint 2,60 m.
- A planta 2D compacta (≈52vh, teto 540 px) e o modo `?modo=layout` (o CRM abre
  só o editor) são comportamento da página — não requerem nada do pacote.
- Calibração px→metros: `x=(px−px0)/pxm+x0 · z=(py−py0)/pxm+z0` (glbcal); a
  planta, a máscara e a maquete SAEM DA MESMA calibração — nunca misturar.

## 3 · Registro no editor (`index.html`)

Adicionar a entrada em `AMBIENTES` copiando o padrão do ambiente mais parecido
(`canto_lagoa` é o mais recente): identidade/textos, `planta`, `pxm`,
`mascara` (constante `MASCARA_*_B64` inline no arquivo), `areas` (para 3D/tour),
`pisos[]` (abas: planta/mascara/areas/recorte/pisoY/`mascaraDaArea` quando a aba
recorta a máscara do piso pela área), `glb{src, cal, pisoY, corteY,
luzCentrada}`, `camera`, `panos`, `tour`/`tourOrdem`, `galeria` (fotos REAIS do
banco de mídias — curadoria, nunca inventar), `cta`.

Layout padrão: gerar com o distribuir automático de cada aba num navegador
headless e FIXAR os px em `mesasPadrao` (há exemplos de script na sessão de
integração; o algoritmo é o do próprio botão "Distribuir automaticamente").

## 4 · Itens decorativos (props)

Um item vira um GLB próprio e entra no registro do ambiente em `props:[]`:
`{glb, nome, px:{x,y}, alt, escala, rotY}` — posição em px da planta, base no
piso da área (`pisoYEm`) + `alt`. Regras:
- Pipeline igual ao do ambiente (análise → cortes → gltfpack), **≤ 2,5 MB**.
- Conferir a ESCALA por uma medida conhecida do item (o DAE às vezes vem com
  int16 quantizado — converter para float32 antes de transformar).
- Itens repetidos (cadeiras da mesa etc.) ficam DENTRO do GLB do item.
- Exemplo vivo: a árvore do Solar (`assets/ambientes/solar/arvore.glb`, podada
  ao poço do telhado) e a mesa oficial (`assets/mesa-real-lite.glb`).
- Item que obstrui piso: a máscara do ambiente precisa excluir a área dele
  (regerar `mascara*.b64`), senão o cliente arrasta mesa para dentro do item.

## 5 · Validação (obrigatória antes de publicar)

Regra da casa: **montar a lógica certa e TESTAR com arrasto real** — nunca
entregar no olho. Ferramentas em `ferramentas/` (ajuste o caminho do puppeteer
no topo se necessário):
- `testar-mascara.mjs [url]` — bateria de arrasto do Salão de Eventos
  (referência histórica; tem que passar SEMPRE).
- `smoke-pisos.mjs [url] [slugs]` — para CADA ambiente: abas, adicionar/
  distribuir/limpar/restaurar, arrasto real por aba, link `#L=`, persistência.
- `check-ambs3d.mjs` — atalhos de câmera por ambiente na maquete (via HTTP).
- 3D/360 exigem HTTP (`python -m http.server`); o 2D roda em `file://`.
- Zero erros de JS no console é critério de aprovação, não desejo.

## 6 · Publicação

1. Bump do carimbo `<span id="versaoBuild">vDD.MM-N</span>`.
2. Copiar `index.html` + os assets NOVOS/alterados (nunca `provas/` nem
   `_pipeline/`) para o clone de publicação do repo GitHub
   (`vendasindaia/proposta-corporativa`), commit e push na `main` — o GitHub
   Actions publica no Pages. Cache de ~10 min: conferir o carimbo com
   `?vN=<timestamp>` antes de anunciar.
3. Revalidar AO VIVO (mesmos harnesses apontando para a URL publicada).

## 7 · Passo interno da equipe (NÃO é da arquiteta)

Espelhar o registro no CRM: regenerar os seeds (`extrair-registro` da sessão de
integração → `crm-backend/src/database/seeds/maquetes/<slug>.json` com o
`espaco_id` real) e rodar `npx tsx src/scripts/apply-migration-701-espaco-layouts-3d.ts`
(idempotente). O ambiente aparece sozinho na aba Maquetes 3D do CRM.

## Estado atual (28/08/2026)

| Ambiente | slug | Abas | Observações |
|---|---|---|---|
| Salão de Eventos (ITP) | `salao_eventos` | 1 | telão/palco; deck da figueira só em pano |
| Solar 915 (Floripa) | `solar` | 3 | falta o 2º pavimento no export; árvore = prop |
| Mezanino (ITP) | `mezanino` | 2 | térreo + deck a +3,1 m |
| Mirante da Lagoa | `mirante` | 3 | SEM escada modelada — lounge posicionado por hipótese documentada |
| Canto da Lagoa | `canto_lagoa` | 3 | remodelação cenográfica ≠ CAD 2019 (documentado) |
