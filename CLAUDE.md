# <NOME DO PROJETO>

**O que é**: <uma frase — o que o software faz e para quem>
**Stack**: <preencher após a decisão 0001 em `.claude/decisions.md`>
**Versão**: v0.1.0

> Enquanto a decisão 0001 estiver em branco, o projeto não tem stack decidida —
> tem preferência não registrada. Invoque `stack-advisor`.

---

## Regras

@.claude/rules/workflow.md
@.claude/rules/quality.md
@.claude/rules/self-management.md

---

## O loop

Toda tarefa segue: **Issue → branch → implementar → PR → revisão por agente → CI → merge → versão**

Sem issue não há branch. Sem branch não há trabalho.

---

## Defaults de decisão

Existem para eliminar deliberação repetida. Na dúvida, a resposta já está dada:

- Criar issue? → **CRIE**
- Commit está grande? → **DIVIDA**
- Precisa de teste? → **ADICIONE**
- Atualizar doc? → **ATUALIZE**
- Bumpar versão? → **BUMPE**
- Registrar a decisão? → **REGISTRE** em `.claude/decisions.md`

---

## Ambientes

| Branch | Ambiente | Onde |
|--------|----------|------|
| `main` | Produção | <host/URL> |
| `dev` | Desenvolvimento | Local |

**Nunca** testar direto em produção. **Nunca** merge em `main` sem passar por `dev`.

---

## Regras críticas do domínio

> Esta seção começa vazia. Preencha com o que **quebrou de verdade** — não com
> o que você imagina que pode quebrar. Cada entrada deve responder: o quê, por
> quê, como. Exemplo de formato:

### <Ex: API espera timestamp em BRT, não UTC>
- **O QUÊ**: <o comportamento contraintuitivo>
- **POR QUÊ**: <a consequência de errar — o custo real já observado>
- **COMO**: <a forma correta, com exemplo>

---

## Agentes

Antes de abrir PR, invoque os agentes relevantes. Ver `.claude/agents/`.

| Agente | Quando |
|--------|--------|
| `stack-advisor` | Antes da primeira linha de código, e ao rever decisão de stack |
| `issue-manager` | Antes de começar qualquer trabalho — a issue vem primeiro |
| `git-specialist` | Setup do repo, e qualquer operação de git fora do trivial |
| `tech-lead` | Antes de implementar algo com mais de um caminho possível |
| `code-reviewer` | Sempre, antes de abrir PR |
| `test-specialist` | Feature nova ou bug corrigido |
| `security-reviewer` | Autenticação, permissão, entrada de usuário, comando externo |
| `docs-writer` | Mudou comportamento observável, setup, deploy ou contrato |

Agentes de domínio (banco, UI, UX, integração, deploy) estão em
`.claude/agents/_referencia/`. Promova um só quando a dor aparecer pela terceira
vez — exceto o `release-manager`, que vai **antes** do primeiro deploy.
