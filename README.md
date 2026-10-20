# Agentic Dev Starter Kit

> 🌐 **Language note:** this README and [`SETUP.md`](SETUP.md) are in English, but
> the kit itself — `PROCESS.md`, `CLAUDE.md`, the rules, and every agent prompt —
> is written in **Brazilian Portuguese (pt-BR)**.
> Translate the files to your language if you adopt it.

A seed for starting a project with agent-assisted development
Copy it at the start of a project, fill it in, and get to work.

## What the method delivers

1. **One non-negotiable loop** — no process decision is ever made twice
2. **Specialists as versioned files** — peer review when there are no human peers
3. **A structured trail** — issues with a taxonomy that answer, on their own,
   where the effort is going

The base is **3 rule files and 8 agents**. That is deliberately small:
a rule nobody read and an agent nobody invokes cost context in every session.
The kit grows by need, not by anticipation — and `.claude/agents/_referencia/` holds
9 ready-made domain agents for when the need shows up.
More specialists can be created if and when your project needs.

## The work cycle

Every change goes through the same loop:

```
Issue → branch → implement → agent review → PR → CI → merge → version → tag
```

| Moment | What to do | Who to invoke |
|---|---|---|
| Before anything | Create the issue with title, body and labels | `issue-manager` |
| If there is more than one path | Decide the approach before writing code | `tech-lead` |
| Start | Branch off `dev` | `git-specialist` |
| Implement | — | you + Claude |
| Before the PR | Review the diff | `code-reviewer` — **always** |
| If behavior changed | Test coverage | `test-specialist` |
| If it touched auth/permissions/input/execution | Offensive review | `security-reviewer` |
| If behavior, setup or deploy changed | Update the docs **in the same PR** | `docs-writer` |
| Open the PR | Body filled in, `Closes #N`, version bump on the branch | `git-specialist` |
| After the remote merge | `git pull` in the working tree **before** build or deploy | `git-specialist` |
| After the deploy is validated | Annotated tag on `main` | `git-specialist` |

Two rules keep the loop honest. **Decision defaults** remove repeated deliberation:
create an issue → *create*, big commit → *split*, needs a test → *add*, docs out of
date → *update*, bump the version → *bump*, record a decision → *record*. And **the
version bump goes in the change's own PR**, as the last commit on the branch — so
every merge into `main` maps to exactly one version.

The full model is in [`PROCESS.md`](PROCESS.md) (pt-BR).

## Quickstart

Requires `git`, an authenticated [`gh`](https://cli.github.com/) CLI and
[Claude Code](https://claude.com/claude-code).

```bash
cp -r agentic-dev-starter-kit/. <new-project>/
cd <new-project>
# then, in Claude Code: invoke the `stack-advisor` agent
```

The full 7-step walkthrough (stack decision, git setup, labels, CI, first issue)
is in [`SETUP.md`](SETUP.md).

## Where to read what

| If you want to… | Read |
|---|---|
| Deploy the kit into a project | [`SETUP.md`](SETUP.md) |
| Understand the model: roles, cycle, where automation stops | [`PROCESS.md`](PROCESS.md) (pt-BR) |
| See the loop and the agent list the way Claude sees them | [`CLAUDE.md`](CLAUDE.md) (pt-BR) |
| Read the workflow, quality and self-management rules | [`.claude/rules/`](.claude/rules) (pt-BR) |
| Read or adapt an agent | [`.claude/agents/`](.claude/agents) and [`_referencia/`](.claude/agents/_referencia) (pt-BR) |

## Structure

```
README.md                          this file
SETUP.md                           deployment guide: 7 steps, labels, metrics, pitfalls
PROCESS.md                         the operating model: roles, cycle, automation
CLAUDE.md                          entry point — the loop and the defaults
.claude/rules/workflow.md          issue → branch → PR → review → merge → version
.claude/rules/quality.md           security, tests, code, documentation
.claude/rules/self-management.md   how the rules grow without becoming dead weight
.claude/decisions.md               decision 0001 (stack, required) + template
.claude/agents/                    the 8 base agents
.claude/agents/_referencia/        the 9 domain agents + promotion trigger
.github/ISSUE_TEMPLATE/            bug and feature
.github/PULL_REQUEST_TEMPLATE.md   with the agents' checklist
.github/workflows/ci.yml           adjust to the stack
```

## About this repo

This kit is **opinionated and unsupported**. It encodes one way of working that
came out of building a real project with agents. It is published as-is: take what
is useful, change what isn't. Issues and pull requests are not actively monitored.

## License

[MIT](LICENSE) © 2026 Luis Romano
