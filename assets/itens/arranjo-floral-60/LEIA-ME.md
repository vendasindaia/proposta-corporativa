# Arranjo floral para montagem dos salões

Peça recebida em 08/09/2026. O pacote contém o modelo do arranjo, a miniatura, o desenho recebido e o cadastro preparado para a nova API de layouts.

- `item.glb`: modelo com texturas incorporadas, aproximadamente 2,4 MiB (2.522.408 bytes).
- `thumb.jpg`: imagem renderizada a partir do próprio modelo.
- `medidas-referencia.jpeg`: desenho enviado, preservado.
- `item.json`: ficha completa, incluindo referência Enscape e origem.
- `cadastro-api.json`: cadastro preparado; os endereços passam a funcionar depois de publicar os arquivos no bucket corporativo.

O DAE original tem 106.104.533 bytes e 1.382.892 triângulos. A versão para montagem tem aproximadamente 150 mil triângulos e todas as 125 texturas recebidas. A simplificação reduz detalhes de pétalas quando vista muito de perto; é destinada à montagem dos salões. Os originais continuam em Downloads e a cópia de trabalho está em `layouts-implementacao/importacao-floral`.

O DAE veio com aproximadamente 74 × 87 × 66 cm. Foi reduzido uniformemente pela referência de **45 cm de altura**, sem esticar flores. O modelo final mede **50,56 × 57,39 × 45 cm**, centralizado na base. A prancha ilustrativa indica uma envoltória de **60 × 60 cm**: reservar esse espaço na montagem. Folhagens naturais têm contorno irregular.

Na prévia do sistema: abra **Decorações**, escolha o arranjo e use **Apoiar sobre** para selecionar a mesa. Se a mesa estiver selecionada ao adicionar o arranjo, o vínculo é automático. A peça acompanha a mesa quando ela é movida ou duplicada. A altura de apoio é calculada pelo sistema.

Estoque físico não foi informado; este cadastro não cria quantidade disponível nem reserva para evento.

Conversão utilizada: texturas até 512 px; `converter.mjs` com altura de referência 0,45 m, centro na base e conferência das texturas; `gltfpack -cc -vtf -si 0.12 -sp -se 0.10`; reconferência da base e da altura após simplificação. Provas visuais e testes de montagem estão na pasta de validação.
