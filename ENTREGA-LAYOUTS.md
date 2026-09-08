# Montagens e arranjo floral — 08/09/2026

O visualizador passa a buscar a maquete e o padrão do salão na API central por `?espaco=<slug>`. O modo `&modo=layout` abre diretamente a montagem do salão escolhido. Peças decorativas do padrão aparecem em 2D e 3D; o apoio do floral acompanha a mesa ao mover, guardar no navegador e compartilhar o link.

Publicar esta alteração após a API `GET /api/public/layouts-3d/ambientes/:slug` do crm-backend e sua migration 778. A API padrão é `https://comercial-api.squareweb.app/api/`; uma instalação diferente pode definir a meta `layouts-api-base`. O domínio do visualizador deve estar autorizado no CORS do backend. A página não grava no banco do CRM.

O pacote `assets/itens/arranjo-floral-60/` contém o modelo GLB de 2,52 MB, imagem, medidas e o corpo de cadastro. A altura conferida é de 45 cm; "60" identifica o envelope de referência do arquivo recebido. Os arquivos DAE e texturas originais ficam fora da publicação. Instruções completas no LEIA-ME da pasta.

Antes do cadastro real, publicar os arquivos no Storage e conferir as URLs do `cadastro-api.json`. O catálogo existente usa `estante-velas`: não duplicar a estante. Esta entrega não inclui nem substitui a PR anterior do modelo de estante.

Validação dirigida: seis salões e identificador inválido; floral acompanha a mesa, persiste ao recarregar e em link aberto noutra sessão. Os testes usam API e dados locais, sem escrita em produção. Alterações do cliente no visualizador são locais; a montagem oficial e seu histórico são salvos pelo editor autenticado do CRM.
