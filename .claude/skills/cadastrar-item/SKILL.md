---
name: cadastrar-item
description: Cadastra um item decorativo 3D da Indaiá (mesa de espelho, telão de LED, pérgola, palco, lounge…) no catálogo assets/itens — converte o export do SketchUp em GLB na proporção real, escreve o manifesto item.json e posiciona o item por espaço. Use quando pedirem para "cadastrar item/produto", "converter peça decorativa", "colocar o telão/mesa/pérgola no salão X".
---

# Cadastrar item decorativo 3D

O catálogo vive em `assets/itens/<slug>/` — leia `assets/itens/README-itens.md`
(estrutura, schema do `item.json`, regras). O CRM importa este catálogo por
script interno; o trabalho aqui termina no repositório publicado.

## Passo a passo

1. **Receber**: `.dae` do item ISOLADO + pasta de texturas (mesmo nome) + UMA
   medida real de referência (ex.: "tampo 1,60 m", "painel 4,00 m de largura").
   Sem medida de referência, PARE e peça — escala no olho é proibida.
2. **Converter** com o pipeline de `assets/ambientes/_pipeline/` (mesmos scripts
   dos ambientes): análise → cortes (tirar chão/entorno que vier junto do
   export) → simplificar decorativos → `gltfpack -cc`. Alvos: **≤ 2,5 MB**,
   origem na BASE do item (centro XZ, y=0 no chão), metros/Y-up.
   ⚠ DAE do SketchUp às vezes traz posição int16 quantizada — converter para
   float32 ANTES de aplicar transformações (precedente: mesa oficial).
3. **Conferir a escala**: medir no GLB a dimensão de referência (raycast ou
   bbox) e registrar em `escala_conferida_por`. Divergiu >2%? Corrigir escala
   no cfg, nunca "deixar perto".
4. **Manifesto** `item.json` (schema no README): slug kebab-case com a medida
   no nome quando fizer sentido (`telao-led-4x3`, `mesa-espelho-120`), nome,
   categoria, medidas, footprint, thumb (render do próprio GLB ou foto real).
5. **Posicionar por espaço** (quando pedido): coordenadas em px da planta
   calibrada do ambiente — abrir o editor do espaço, achar o px certo (o
   painel `?equipe=1` e a planta ajudam), gravar em `espacos` no manifesto E
   adicionar em `index.html` → `AMBIENTES.<slug>.props[]` para o item aparecer
   na maquete. Item que ocupa piso ⇒ regenerar a máscara da aba excluindo o
   footprint (`gerar-mascara.mjs` com `excluir_retangulos_m`).
6. **Validar** (obrigatório): `ferramentas/smoke-pisos.mjs` do(s) espaço(s)
   tocado(s) + abrir a maquete e conferir o item de perto e de longe (screenshot).
   Item posicionado não pode: flutuar, afundar no piso, atravessar parede,
   nem deixar mesa entrar dentro dele.
7. **Publicar**: bump do carimbo de versão + commit/push na `main` (Pages
   atualiza em ~10 min). O espelhamento no CRM é passo INTERNO da equipe.

## Categorias válidas

`audiovisual` · `mobiliario` · `decoracao` · `estrutura` · `cenografia`
(não inventar novas sem combinar — o CRM valida contra esta lista).

## Exemplos vivos

- `assets/itens/mesa-redonda-160/` — mesa oficial (8 lugares), manifesto completo.
- `assets/ambientes/solar/arvore.glb` — item embutido no ambiente (árvore podada
  ao poço do telhado): mostra o padrão de `props[]` + máscara excluindo o item.
