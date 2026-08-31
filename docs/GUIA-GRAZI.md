# Guia para a Grazi — Layouts 3D da Indaiá

Oi Grazi! Este repositório é o sistema de layouts dos espaços da Indaiá: os teus modelos
do SketchUp viram **maquetes navegáveis** com editor de mesas em 2D, visão 3D, 360° e
tour gravado — usados na proposta corporativa, no CRM e, em breve, nas apresentações
dos consultores. Este guia resume o que já existe e como você pode trabalhar direto
aqui, no VS Code com o Claude Code.

## O que já está no ar (clique para abrir)

| Espaço | Link do editor |
|---|---|
| Salão de Eventos · Itapema | https://vendasindaia.github.io/proposta-corporativa/?espaco=salao_eventos&modo=layout |
| Solar 915 · Florianópolis | https://vendasindaia.github.io/proposta-corporativa/?espaco=solar&modo=layout |
| Mezanino · Itapema | https://vendasindaia.github.io/proposta-corporativa/?espaco=mezanino&modo=layout |
| Mirante da Lagoa · Florianópolis | https://vendasindaia.github.io/proposta-corporativa/?espaco=mirante&modo=layout |
| Canto da Lagoa · Florianópolis | https://vendasindaia.github.io/proposta-corporativa/?espaco=canto_lagoa&modo=layout |

Em cada um: **abas por ambiente** no 2D (arraste as mesas — elas respeitam paredes,
pilares e vãos), **Ver em 3D** (pills de câmera por ambiente), **360°** e **tour**.

## Como trabalhar direto no VS Code

1. Instale o [VS Code](https://code.visualstudio.com/) + [Claude Code](https://claude.com/claude-code) + Node.js + Google Chrome.
2. Clone este repositório: `git clone https://github.com/vendasindaia/proposta-corporativa`
3. Abra a pasta no VS Code e converse com o Claude. O repositório carrega uma
   **skill chamada `calibrar-ambiente`** com o passo a passo técnico completo —
   basta pedir, por exemplo: *"calibra o ambiente novo que exportei do SketchUp
   (arquivos na pasta X)"* ou *"converte este item decorativo em prop"*.
   O manual profundo do pipeline está em `assets/ambientes/README-pipeline.md`.

## O que enviar em cada export (checklist)

- `.dae` (COLLADA) do ambiente **vazio** (sem mesas de evento) + a **pasta de
  texturas zipada com o mesmo nome** do arquivo — sem ela o modelo fica cinza.
- **Todos os pavimentos e as escadas modeladas.** No Mirante os dois andares
  vieram sem escada e em posições separadas — o encaixe do lounge sobre o hall
  ficou por dedução (documentada); com a escada, o encaixe é exato.
- Planta cotada (PDF) junto, sempre que existir — é a conferência de escala.
- Item decorativo (pérgola, palco, árvore, lounge...): `.dae` isolado + texturas
  + uma medida de referência (ex.: "tampo 1,60 m").

## O que já está na tua fila (pendências conhecidas)

1. **Solar**: export com o **2º pavimento** (hoje só existem os panoramas dele).
2. **Mirante**: export com a **escada** (ou a posição real do lounge) para
   substituir a hipótese de encaixe.
3. Melhorias que você quiser nos 5 ambientes: texturas, elementos que faltam,
   itens decorativos novos — agora é só exportar e rodar a skill.

## Regras que valem para tudo (decisões já tomadas)

- Uma **aba por ambiente/nível**; nenhum texto desenhado sobre a planta.
- Mesa oficial: redonda de **1,60 m, 8 lugares** (footprint 2,60 m com cadeiras).
- Nada é entregue sem passar nos testes de arrasto (`ferramentas/`) — o Claude
  roda isso sozinho se você pedir "valide antes de publicar".
- Publicar = commit na `main` deste repositório (o site atualiza sozinho em ~10 min).

Qualquer dúvida, me chama. Obrigado! 🙌
