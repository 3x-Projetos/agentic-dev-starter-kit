# Workflow

Issue → branch → implementar → PR → revisão por agente → CI → merge → versão.

## 1. Issue

Toda tarefa começa por uma issue. Sem exceção, inclusive para bug urgente.
Invoque `issue-manager` — o padrão completo de título, corpo e labels está lá.

**Título**: `<tipo>(<área>): <sintoma concreto>`
```
fix(export): planilha sai com 3h a menos — conversão trata UTC como hora local
```

**Corpo**: contexto · comportamento esperado · comportamento atual (bugs) ·
critério de aceite verificável.

**Labels — quatro namespaces, todo label com prefixo:**

| Namespace | Valores | Por issue |
|---|---|---|
| `type:` | `bug` `feature` `improvement` `docs` `chore` | 1, obrigatório |
| `area:` | uma por tela/módulo — desenhado por projeto | 1+, obrigatório |
| `priority:` | `high` `medium` `low` | 1, obrigatório |
| `state:` | `next` `later` `idea` `blocked` | 0 ou 1 |

O prefixo é o que torna o backlog filtrável e as métricas possíveis sem planilha:
`gh issue list --label "area:export" --label "type:bug"` já responde onde estão os bugs.
Issue aberta sem `state:` é trabalho ativo.

## 2. Branch

Sempre a partir de `dev`, nunca de `main`.

```
<tipo>/<número-da-issue>-<slug-curto>
feat/42-rate-limit · fix/57-timezone-eventos
```

Se você não opera git com confiança, peça ao `git-specialist` — descreva a
intenção em português e ele traduz, explica e executa.

## 3. Commits

```
<tipo>: descrição no imperativo

Corpo explicando o porquê, se não for óbvio.
```

Tipos: `feat` `fix` `refactor` `docs` `test` `chore` `perf`

Um commit = uma mudança coerente. Se o título precisa de "e", divida.

## 4. Pull request

- Referencia a issue: `Closes #42`
- Descreve **o que mudou e por quê**, não o diff
- Inclui o bump de versão **dentro do PR** (ver seção 6)
- Não abre PR sem ter rodado os agentes de revisão

## 5. Revisão

Antes de abrir o PR, invoque:

- `code-reviewer` — sempre
- `test-specialist` — se mudou comportamento
- `security-reviewer` — se tocou auth, permissão, entrada de usuário ou execução externa

Aplique o retorno **antes** de abrir o PR. Se discordar de um apontamento, registre
o porquê na descrição do PR — isso é o que substitui a conversa com um colega.

## 6. Versão (SemVer)

| Bump | Quando |
|------|--------|
| MAJOR | Quebra compatibilidade |
| MINOR | Funcionalidade nova, compatível |
| PATCH | Correção ou ajuste pequeno |

**O bump entra no PR da mudança**, como último commit da branch — nunca em commit
separado depois do merge. Assim cada merge em `main` corresponde a exatamente uma versão.

Atualize em todos os lugares onde a versão aparece (liste-os aqui quando souber quais são)
e adicione a entrada no `CHANGELOG.md`.

## 7. Merge e deploy

```
feature → PR para dev → testar local → PR de dev para main → deploy
```

Proibido:
- Merge direto em `main`
- Deploy sem ter rodado localmente
- Hotfix direto em `main`, mesmo urgente

Após deploy confirmado funcionando, crie a tag anotada:

```bash
git tag -a vX.Y.Z -m "vX.Y.Z — <resumo>"
git push origin vX.Y.Z
```

Tags só em `main`, só depois do deploy validado.

## 8. Depois do merge remoto

`gh pr merge` avança só o ref remoto. Rode `git pull` no working tree antes de
qualquer script de build ou deploy — senão você empacota a versão anterior.
