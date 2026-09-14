# Dani — instalação e prompt de partida (URL da proposta corporativa)

## Parte 1 · O que precisa estar na máquina (uma vez só)

1. **VS Code** instalado (já feito).
2. **Claude Code** instalado e logado (extensão do VS Code ou terminal).
3. **Git** instalado e o GitHub da Dani com acesso ao repo
   `vendasindaia/proposta-corporativa` (o Adriano libera).
4. Clonar o repo numa pasta dela:
   `git clone https://github.com/vendasindaia/proposta-corporativa.git`
5. **Python** instalado (qualquer 3.x — serve a página local com
   `python -m http.server 8140`).

## Parte 2 · O prompt de partida

Abrir a pasta do repo no VS Code, abrir o Claude e colar o texto entre as
linhas — só isso; o resto ele guia:

---

Você está no repositório da proposta corporativa da Indaiá Eventos. Leia a
skill `.claude/skills/finalizar-url-proposta/SKILL.md` inteira antes de
qualquer coisa — ela explica o que a página faz, o que já está pronto, as
decisões que não podem ser mudadas e a fila do que falta, em ordem.

Meu objetivo: continuar esse projeto e FINALIZAR a URL da proposta. Comece
me mostrando a página funcionando (demo local) e me explicando em palavras
simples o item 1 da fila (o espelho fino dos valores). Depois vamos item por
item — sempre com teste real antes de publicar, como a skill manda.

Regras: nunca publique sem me mostrar as provas verdes; nunca exponha
desconto, data real ou disponibilidade na página; qualquer dado que faltar
do CRM, a gente anota e pede ao Adriano em vez de inventar.

---

## Dúvidas rápidas

- **"Preciso de acesso ao CRM?"** Não. A página lê uma rota pública por
  token. O Adriano te passa um link de teste quando precisar de dados reais.
- **"Onde vejo a página?"** `python -m http.server 8140` na pasta do repo e
  abrir http://localhost:8140 (demo). Com um link de teste do Adriano, a
  mesma página abre vestida com um orçamento real.
- **"Como entrego?"** O Claude te guia: teste verde → push na main → o
  GitHub publica sozinho (uns 10 min de cache).
