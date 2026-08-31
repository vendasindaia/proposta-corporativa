# Ferramentas de validação (obrigatórias antes de publicar)

- `testar-mascara.mjs [url]` — bateria de arrasto real do Salão de Eventos (referência histórica).
- `smoke-pisos.mjs [url] [slugs]` — abas, botões, arrasto por aba, link `#L=` e persistência de CADA ambiente.
- `check-ambs3d.mjs` — atalhos de câmera por ambiente na maquete (precisa de servidor HTTP local).

Requisitos: Node 18+, Google Chrome. Os scripts pegam o `puppeteer` de outro projeto via
`createRequire(...)` no topo — na tua máquina, rode `npm i puppeteer` nesta pasta e troque
essa linha por `import puppeteer from 'puppeteer'` (e remova o `executablePath` se o
Chromium baixado servir). 3D/360 exigem `python -m http.server` na raiz do repo; o 2D roda
direto em `file://`. Critério de aprovação: TODOS os testes verdes e zero erros de JS.
