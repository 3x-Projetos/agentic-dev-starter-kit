# Agentes de referência

Templates de agentes de domínio. **Não fazem parte da base** — nenhum está ativo
até você mover para `.claude/agents/`.

A base cobre o ciclo de qualquer projeto: issue, git, planejamento, revisão, teste,
segurança. Estes cobrem frentes que só alguns projetos têm — e que muitos nunca terão.

## Como promover

1. Mova o arquivo para `.claude/agents/`
2. Preencha a seção `<!-- Preencha -->` no fim, quando houver. Agente que não
   conhece as convenções do projeto reporta preferência como se fosse defeito
3. Ajuste a terminologia ao stack real
4. Registre em `.claude/decisions.md` o gatilho que justificou a promoção

## Quando promover cada um

A regra é a do resto do kit: **na terceira vez que a dor aparecer**, não na primeira.

| Agente | Promova quando |
|---|---|
| `api-debugger` | Integração com API externa começar a consumir tempo em divergência de contrato — payload, caixa de parâmetro, cabeçalho, fuso horário |
| `database-specialist` | O schema passar de ~15 entidades, ou aparecer a primeira consulta lenta ou migration arriscada em base com dados |
| `backend-validator` | O backend crescer a ponto do `code-reviewer` genérico perder profundidade |
| `frontend-validator` | Idem para o frontend — acessibilidade e performance de render pedem olhar próprio |
| `ui-specialist` | Houver usuário real reclamando de layout, densidade ou hierarquia visual |
| `ux-specialist` | Aparecer atrito de fluxo: passos demais, erro recorrente do usuário, tarefa abandonada |
| `security-blueteam` | O `security-reviewer` (viés ofensivo) começar a só apontar problema sem propor defesa |
| `orchestrator` | Houver mais de uma frente simultânea e o custo passar a ser coordenação, não implementação |
| `release-manager` | O sistema ganhar usuários reais — promova **antes** do primeiro deploy, não depois do primeiro susto |

## Por que não estão na base

Agente criado antes da necessidade não é invocado — e todo agente ativo é
superfície que alguém precisa manter atualizada. Quatro deles (`ui`, `ux`,
`backend-validator`, `frontend-validator`) sobrepõem parcialmente o
`code-reviewer` da base: promovê-los cedo faz duas revisões dizerem a mesma coisa
com palavras diferentes, e a pessoa passa a ignorar as duas.

## Já cobertos pela base

Não procure aqui por escolha de stack, planejamento, revisão de código, teste,
segurança ofensiva, git, gestão de issue ou documentação. Estão em
`.claude/agents/`: `stack-advisor`, `tech-lead`, `code-reviewer`,
`test-specialist`, `security-reviewer`, `git-specialist`, `issue-manager`,
`docs-writer`.
