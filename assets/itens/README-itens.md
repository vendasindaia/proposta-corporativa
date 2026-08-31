# Catálogo de itens decorativos 3D (`assets/itens/`)

Cada item da casa (mesa de espelho, telão de LED, pérgola, palco, lounge…) vive numa
pasta própria, **na proporção real**, pronto para ser posicionado em qualquer ambiente.
Este catálogo é a FONTE: o editor lê daqui e o CRM **importa** daqui por script — a
arquiteta nunca precisa (nem deve) acessar o CRM.

```
assets/itens/<slug>/
  item.json     ← manifesto (obrigatório, schema abaixo)
  item.glb      ← o modelo (metros, Y-up, origem na BASE/centro do item, ≤ 2,5 MB)
  thumb.jpg     ← foto/render do item (≥ 640 px de largura)
```

## `item.json` — schema

```json
{
  "slug": "telao-led-4x3",
  "nome": "Telão de LED 4×3 m",
  "categoria": "audiovisual",            // audiovisual · mobiliario · decoracao · estrutura · cenografia
  "medidas_m": { "largura": 4.0, "profundidade": 0.6, "altura": 3.0 },
  "footprint_m": 4.0,                     // diâmetro/maior lado ocupado no piso (para máscara e 2D)
  "glb": "item.glb",
  "thumb": "thumb.jpg",
  "escala_conferida_por": "largura do painel medida no modelo = 4,00 m",
  "espacos": {                            // ONDE o item pode/costuma ficar, POR espaço (opcional)
    "salao_eventos": [ { "aba": 0, "x": 1737, "y": 550, "rotY": 0, "alt": 0 } ],
    "mirante":       [ { "aba": 0, "x": 1200, "y": 300, "rotY": 1.57, "alt": 0 } ]
  },
  "observacoes": "encosta em parede; não obstrui piso além do footprint"
}
```

- `x`/`y` em **px da planta calibrada** do espaço (a mesma do editor); `aba` é o índice
  da aba/piso; `rotY` em radianos; `alt` em metros sobre o piso da área.
- `escala_conferida_por` é OBRIGATÓRIO: toda conversão confere o modelo contra uma
  medida real conhecida (regra da casa desde a mesa de 1,60 m).

## Regras do GLB

- Pipeline igual ao dos ambientes (ver skill `calibrar-ambiente` §4): análise →
  cortes → `gltfpack -cc`. **≤ 2,5 MB**, texturas embutidas, origem na base.
- Item que ocupa piso: quem registrar o item num ambiente também regenera a
  `mascara*.b64` daquele ambiente excluindo o footprint (senão o cliente arrasta
  mesa para dentro do telão).
- Para o item APARECER na maquete de um espaço, ele também entra no registro do
  ambiente em `index.html` → `props:[{glb:'assets/itens/<slug>/item.glb', …}]`
  (o `espacos` do manifesto documenta e alimenta o CRM; o `props[]` desenha).

## Exemplo vivo

`assets/itens/mesa-redonda-160/` — a mesa oficial da casa (tampo 1,60 m, 8 lugares,
footprint 2,60 m). O GLB dela é o que o editor instancia em todas as mesas.

## Como isso chega ao CRM (passo interno da equipe — NÃO é da arquiteta)

O crm-backend tem um importador (`sync-itens-3d.ts`) que lê estas pastas e faz
upsert nas tabelas `itens_3d` (catálogo) e `espaco_itens_3d` (posições por espaço,
resolvendo o slug do ambiente para o espaço real). A arquiteta só publica no repo.
