# Governança das regras

> Esta é a regra que faz as outras crescerem sem virar peso morto.
> É a mais importante do kit.

## Início de sessão

Antes de mudar código, o agente deve ler:

1. `CLAUDE.md` na raiz
2. Todos os arquivos em `.claude/rules/`
3. Todos os agentes em `.claude/agents/`

Se encontrar conflito entre regras, avise o usuário **antes** de prosseguir.

## Hierarquia

Da maior para a menor prioridade:

1. Instrução direta do usuário na sessão atual
2. `CLAUDE.md` do projeto
3. `.claude/rules/`
4. `~/.claude/CLAUDE.md` global

## Quando criar uma regra nova

Só nestes casos:

- Um padrão se repetiu **3 vezes ou mais** sem estar documentado
- Um erro aconteceu e uma regra o teria evitado
- O projeto adotou ferramenta ou convenção nova

**Não** crie regra por antecipação. Regra sem incidente por trás é ruído que
consome contexto em toda sessão e ninguém segue.

## Quando remover uma regra

- Ela é sistematicamente ignorada ou contornada — ou está errada, ou o custo é maior que o benefício
- O motivo que a criou não existe mais
- Outra regra já cobre o caso

Revisar o conjunto a cada marco importante do projeto. Regras acumulam por inércia;
remover exige decisão consciente.

## Como alterar uma regra

1. **Nunca** modificar arquivo de regra silenciosamente
2. Propor ao usuário: o que muda, por quê (com a evidência recente), o que isso afeta
3. Esperar aprovação
4. Alterar via commit, com entrada no changelog do arquivo

## Higiene

- Arquivo de regra é lido em toda sessão. Linha inútil custa contexto para sempre
- Um arquivo por domínio. Se precisar de um 5º arquivo, primeiro pergunte se algum
  dos 4 existentes não deveria absorver o conteúdo
- Comece com 3 arquivos de regra. Chegar a 10 deve doer

## Changelog

- <data>: versão inicial
