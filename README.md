# Agentic Dev Starter Kit

Semente para iniciar um projeto com desenvolvimento assistido por agentes: um loop
de trabalho único, especialistas como arquivos versionados, e rastro estruturado
que vira métrica sem planilha.

Copie no início do projeto, preencha, e comece a trabalhar.

Este README é o **guia de implantação** — passo a passo e comandos. Para entender
o modelo antes de adotá-lo (papéis, ciclo de ponta a ponta e onde a automação
para), leia [`PROCESS.md`](PROCESS.md).

---

## O que o método entrega

Não é geração de código. São três coisas que só funcionam juntas:

1. **Um loop único e inegociável** — nenhuma decisão sobre processo se repete
2. **Especialistas como arquivos versionados** — revisão por pares onde não há pares
3. **Rastro estruturado** — issues com taxonomia, que respondem sozinhas onde o
   esforço está indo

A base são **3 arquivos de regra e 8 agentes**. É deliberadamente pouco: regra que
ninguém leu e agente que ninguém invoca custam contexto em toda sessão. O kit
cresce por incidente, não por antecipação — e `.claude/agents/_referencia/` guarda
9 agentes de domínio prontos para quando a necessidade aparecer.

---

## Pré-requisitos

| Ferramenta | Para quê |
|---|---|
| `git` | Óbvio |
| `gh` CLI **autenticado** | Issues e PRs são a espinha do método. Sem isso, nada funciona |
| Claude Code | Os agentes |
| Runtime da stack | Definido no passo 2 |

Confira com `gh auth status`. Se falhar, `gh auth login` antes de qualquer coisa.

**Você não precisa saber git.** O agente `git-specialist` existe para isso:
descreva a intenção em português — "quero começar essa tarefa", "quero mandar isso
pra produção", "acho que apaguei alguma coisa" — e ele traduz, explica o que vai
acontecer **antes** de rodar, e executa.

---

## Implantação — 7 passos

Passos 3 a 7 levam ~40 minutos. O passo 2 leva o tempo que precisar levar.

### Passo 1 · Copiar o kit

```bash
cp -r agentic-dev-starter-kit/. <novo-projeto>/
cd <novo-projeto>
```

### Passo 2 · Decidir a stack

> Invoque o `stack-advisor` para conduzir a escolha da stack.

**Este é o passo que mais custa se for pulado**, e o único que produz decisões
caras de desfazer. A stack nasce da necessidade — não do que o agente sugere
primeiro, nem do que você viu num artigo.

O agente conduz seis fases: levanta **restrições** antes de mencionar qualquer
tecnologia, separa as decisões **caras de desfazer** das baratas, apresenta 2 ou 3
opções por decisão cara com o "por que não" de cada descartada, verifica na web se
cada candidato ainda está vivo, propõe um **esqueleto ambulante** para provar a
escolha antes de assumi-la, e registra tudo na decisão `0001`.

O detalhe que faz isso funcionar quando você não conhece as tecnologias: a fase 1
só faz perguntas que **você consegue responder** — quantos usuários, onde roda,
quem mantém em dois anos, o que a empresa já usa. A recomendação precisa apontar
qual das suas respostas a produziu. Recomendação que não rastreia até uma
restrição sua é preferência do agente, e você pode recusá-la nessa base.

Ao final, o agente entrega cinco perguntas para você fazer à própria recomendação
— elas existem para dar alavanca a quem não é especialista. Faça todas.

**Só avance quando a decisão `0001` estiver preenchida.** Restrição que você não
soube responder fica em branco de propósito: campo vazio ali é aviso de risco.

### Passo 3 · Preencher o `CLAUDE.md`

Substitua os `<placeholders>`: nome, o que é, stack, ambientes.

**Deixe a seção "Regras críticas do domínio" vazia.** Ela se preenche com o que
quebrar de verdade. Regra escrita por antecipação não é seguida e custa contexto
em toda sessão — as que valem nascem de incidente, com o custo já pago.

### Passo 4 · Configurar git e GitHub

> Invoque o `git-specialist` para fazer o setup inicial do repositório.

Ele cobre: `git init`, primeiro commit, `.gitignore` do stack, criação do repo
remoto, branch `dev`, **`dev` como branch padrão** — para que PR aberto por engano
não aponte para produção — e proteção de `main`.

Se o plano do GitHub não permitir branch protection em repo privado, ele avisa e
registra em `.claude/decisions.md` que a proteção é procedural, não técnica. Vale
saber quando o portão depende de disciplina em vez de ferramenta.

### Passo 5 · Criar as labels

Esta é a fundação das métricas. Rode:

```bash
# type: — exatamente uma por issue, obrigatória
gh label create "type:bug"         -c "#d73a4a" -d "Algo não está funcionando"
gh label create "type:feature"     -c "#e36cc9" -d "Funcionalidade nova"
gh label create "type:improvement" -c "#e36cc9" -d "Iteração sobre feature existente"
gh label create "type:docs"        -c "#d4c5f9" -d "Documentação"
gh label create "type:chore"       -c "#d4c5f9" -d "Manutenção, refactor, tooling"

# priority: — exatamente uma por issue, obrigatória
gh label create "priority:high"    -c "#B60205" -d "Urgente"
gh label create "priority:medium"  -c "#FBCA04" -d "Padrão"
gh label create "priority:low"     -c "#0E8A16" -d "Quando sobrar tempo"

# state: — zero ou uma. Sem state = trabalho ativo
gh label create "state:next"       -c "#0E8A16" -d "Próxima sessão de trabalho"
gh label create "state:later"      -c "#C2E0C6" -d "Backlog, sem pressa"
gh label create "state:idea"       -c "#FEF2C0" -d "Registro, talvez nunca faça"
gh label create "state:blocked"    -c "#E99695" -d "Bloqueado por dependência"

# area: — desenhe para o SEU projeto, uma por tela/módulo
gh label create "area:<modulo>"    -c "#1D76DB" -d "<descrição>"
```

Os três primeiros namespaces são universais — copie como estão. **`area:` é o
único que você desenha**: comece com uma label por tela ou módulo principal e
adicione conforme o produto crescer. É de onde sai a resposta para "que parte do
sistema mais consome tempo".

### Passo 6 · Ajustar o CI

Edite `.github/workflows/ci.yml` para a stack decidida no passo 2. **Faça agora,
não depois** — "depois" não chega, e o projeto entra em produção sem portão automático.

Revisão por agente cobre **julgamento**; CI cobre **regressão**. São coisas
diferentes e o método precisa das duas.

### Passo 7 · Abrir a issue #1

> Invoque o `issue-manager` para criar a issue de setup inicial do projeto.

O método vale desde o primeiro commit. Se o setup não passou pelo loop, o loop já
foi quebrado uma vez — e a segunda fica mais fácil.

---

## O ciclo de trabalho

```
Issue → branch → implementar → revisão por agente → PR → CI → merge → versão → tag
```

| Momento | O que fazer | Quem invocar |
|---|---|---|
| Antes de qualquer coisa | Criar a issue com título, corpo e labels | `issue-manager` |
| Se há mais de um caminho | Decidir a abordagem antes de escrever código | `tech-lead` |
| Começar | Branch a partir de `dev` | `git-specialist` |
| Implementar | — | você + Claude |
| Antes do PR | Revisar o diff | `code-reviewer` — **sempre** |
| Se mudou comportamento | Cobertura de teste | `test-specialist` |
| Se tocou auth/permissão/entrada/execução | Revisão ofensiva | `security-reviewer` |
| Se mudou comportamento, setup ou deploy | Atualizar a doc **no mesmo PR** | `docs-writer` |
| Abrir PR | Corpo preenchido, `Closes #N`, bump de versão na branch | `git-specialist` |
| Depois do merge remoto | `git pull` no working tree **antes** de build ou deploy | `git-specialist` |
| Depois do deploy validado | Tag anotada em `main` | `git-specialist` |

### Defaults de decisão

Existem para eliminar deliberação repetida. Na dúvida, a resposta já está dada:
criar issue → **crie** · commit grande → **divida** · precisa de teste →
**adicione** · atualizar doc → **atualize** · bumpar versão → **bumpe** ·
registrar decisão → **registre**.

### A regra de versão que mais importa

**O bump entra no PR da mudança**, como último commit da branch — nunca em commit
separado depois do merge. Assim cada merge em `main` corresponde a exatamente uma
versão, e o histórico responde "o que subiu quando" sem arqueologia.

---

## O método de issue

A issue é a unidade de trabalho **e** a unidade de medida. Malfeita, o trabalho
fica sem rastro e a métrica mente.

**Título:** `<tipo>(<área>): <sintoma concreto>` — descreva o que a pessoa vê, e a
causa depois de um travessão quando souber.

```
fix(export): planilha sai com 3h a menos — conversão trata UTC como hora local
feat(admin): controle de visibilidade dos atalhos por perfil
docs: incidente 02/06 — falha no login (causa raiz + contorno)
```

"Corrigir bug do export" não é título: não permite priorizar sem abrir a issue.

**Corpo:** contexto · comportamento esperado · comportamento atual (bugs, com
passos numerados) · critério de aceite **verificável**.

**Tamanho:** uma issue cabe numa sessão de trabalho. Se não cabe, decomponha —
issue-mãe com o objetivo, filhas entregáveis isoladamente, ligadas por `Parte de #N`.

O padrão completo está em `.claude/agents/issue-manager.md`.

---

## Métricas de graça

Com os namespaces aplicados de forma consistente, isto vira uma consulta — sem
planilha, sem ferramenta extra:

```bash
# Saúde do código: proporção bug vs feature
gh issue list --state all --label "type:bug"     --limit 500 --json number | jq length
gh issue list --state all --label "type:feature" --limit 500 --json number | jq length

# Onde o esforço está concentrado de verdade
gh issue list --state all --limit 500 --json labels \
  --jq '.[].labels[].name' | grep '^area:' | sort | uniq -c | sort -rn

# A urgência é real ou é etiqueta? (high ainda abertos)
gh issue list --state open --label "priority:high" --json number,title,createdAt
```

A segunda consulta costuma ser a que surpreende: uma frente concentrando a maior
parte das issues é informação de planejamento que ninguém tem sem a taxonomia — e
que só aparece se o label for aplicado com disciplina. Por isso a
responsabilidade é do `issue-manager`, não de quem abre a issue.

---

## Os 8 agentes da base

| Agente | Papel | Quando |
|---|---|---|
| `stack-advisor` | Conduz a escolha da stack a partir de restrições | Uma vez, antes do código — e ao rever |
| `issue-manager` | Padrão de issue e taxonomia de labels | Antes de todo trabalho |
| `git-specialist` | Opera o git para quem não quer aprender git | Setup e qualquer operação fora do trivial |
| `tech-lead` | Planeja antes do código existir | Tarefa com mais de um caminho |
| `code-reviewer` | A revisão por pares que você não tem | Sempre, antes do PR |
| `test-specialist` | Rede de proteção | Feature nova ou bug corrigido |
| `security-reviewer` | Mentalidade ofensiva | Auth, permissão, entrada, execução externa |
| `docs-writer` | Documentação por público, mantida junto com o código | Mudou comportamento, setup, deploy ou contrato |

Formato nativo do Claude Code (`.claude/agents/<nome>.md` com frontmatter),
invocáveis pelo nome.

Os outros 9 estão em `.claude/agents/_referencia/`, cada um com o gatilho de
promoção documentado. O primeiro a promover costuma ser o `release-manager` —
no dia em que o sistema ganhar usuários reais.

---

## Como o kit cresce

A regra vale para regra e para agente: **só na terceira vez que a dor aparecer.**

| Adicione | Quando |
|---|---|
| Regra nova | Um padrão se repetiu 3+ vezes sem estar escrito, ou um erro aconteceu e a regra o teria evitado |
| Agente novo | Uma frente passou a consumir tempo recorrente que os 8 da base não cobrem |
| `area:` nova | Um módulo virou frente própria de trabalho |

**Não crie por antecipação.** Regra sem incidente por trás é ruído consumido em
toda sessão e que ninguém segue. Agente criado "para quando precisar" nunca é
invocado e vira superfície para manter.

E remova: regra sistematicamente contornada está errada ou custa mais do que
entrega. `.claude/rules/self-management.md` governa isso — é a regra mais
importante do kit, porque é a que impede o conjunto de virar peso morto.

---

## Erros comuns de implantação

| Erro | Consequência |
|---|---|
| Deixar a stack ser sugerida sem levantar restrições | A escolha vira preferência do agente, e você não tem base para recusá-la |
| Aceitar uma opção única como recomendação | Sem "por que não" das alternativas, não há decisão auditável |
| Preencher restrição que você não sabe responder | Chute vira dívida cara; campo em branco é aviso, não descuido |
| Pular o esqueleto ambulante | A stack errada só aparece na primeira integração séria, quando trocar já custa |
| Trazer o conjunto de regras de um projeto maduro | Contexto estourado no dia 1, regras que ninguém segue |
| Ativar todos os agentes de saída | Nenhum é usado; a base de 8 é o que cabe na cabeça |
| Preencher "regras críticas do domínio" com hipóteses | Ruído permanente; a seção deve nascer vazia |
| Pular a issue "porque é rápido" | O rastro se perde e a métrica mente |
| Aplicar label de qualquer jeito | A taxonomia só serve se for consistente |
| Deixar o CI para depois | "Depois" não chega |
| Bump de versão em commit separado após o merge | Deixa de existir correspondência entre merge e versão |
| Promover agente de referência sem preencher as convenções | Ele reporta preferência como se fosse defeito |

---

## Estrutura

```
README.md                          guia de implantação — este arquivo
PROCESS.md                         o modelo operacional: papéis, ciclo, automação
CLAUDE.md                          ponto de entrada — o loop e os defaults
.claude/rules/workflow.md          issue → branch → PR → revisão → merge → versão
.claude/rules/quality.md           segurança, testes, código, documentação
.claude/rules/self-management.md   como as regras crescem sem virar peso morto
.claude/decisions.md               decisão 0001 (stack, obrigatória) + template
.claude/agents/                    os 8 da base
.claude/agents/_referencia/        os 9 de domínio + gatilho de promoção
.github/ISSUE_TEMPLATE/            bug e feature
.github/PULL_REQUEST_TEMPLATE.md   com checklist dos agentes
.github/workflows/ci.yml           ajustar ao stack
```
