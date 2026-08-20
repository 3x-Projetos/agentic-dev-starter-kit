# Qualidade

> Se o projeto já tem linter, formatter ou convenção estabelecida, ela vence.
> Estas regras preenchem lacunas, não substituem o que existe.

## Segurança — inegociável

- Nunca commitar: senha, chave de API, token, chave privada, `.env`
- `.gitignore` cobre no mínimo: `.env`, `*.pem`, `*.key`, `node_modules/`, `dist/`
- Toda entrada de usuário é validada no servidor (schema, não `if`)
- Toda dependência nova passa por pergunta: dá para resolver sem ela?

## Testes

- Bug corrigido exige teste que reproduz o bug — escrito **antes** da correção
- Feature nova exige teste do caminho feliz + o caminho de erro mais provável
- Teste não pode ser intermitente. Teste que falha às vezes é pior que nenhum
- Nome descreve o comportamento: `deve_recusar_comando_quando_usuario_sem_permissao`

## Código

- Um módulo, um propósito
- Arquivo grande é sintoma, não estilo. Acima de ~500 linhas, pergunte o que está junto que não deveria
- Comentário explica **por quê**, nunca **o quê**
- Se a função precisa de um parágrafo para ser explicada, refatore em vez de documentar

## Documentação

- `README.md` desde o commit 1: o que é, como rodar, como contribuir
- Doc desatualizada é pior que ausente — atualize junto com o código, no mesmo PR
- Quando o projeto tiver usuário não-técnico, separe a doc por público:
  desenvolvedor / operação / usuário final

## Registro de decisões

Toda decisão que alguém pode questionar em três meses vai para `.claude/decisions.md`:
contexto, decisão, justificativa, alternativas descartadas, **gatilho de revisão** e
arquivos afetados.

O gatilho de revisão é o campo que quase todo mundo pula — o número ou evento que
obriga a reabrir a decisão. É o que transforma uma escolha datada em dívida
controlada em vez de surpresa. A decisão `0001` (stack) é obrigatória e vem
pré-estruturada no arquivo.

Isso não é burocracia — é o que impede refazer a mesma discussão e o que permite
outra pessoa (ou outra sessão) retomar o trabalho sem você.
