# Agentic Dev Starter Kit

> 🌐 **Language note:** this README and [`SETUP.md`](SETUP.md) are in English, but
> the kit itself — `PROCESS.md`, `CLAUDE.md`, the rules, and every agent prompt —
> is written in **Brazilian Portuguese (pt-BR)**. The agents are meant to be used
> and read in Portuguese. Translate the files to your language if you adopt it.

A seed for starting a project with agent-assisted development: a single work
loop, specialists as versioned files, and a structured trail that turns into
metrics without a spreadsheet.

Copy it at the start of a project, fill it in, and get to work.

## What the method delivers

It is not code generation. It is three things that only work together:

1. **One non-negotiable loop** — no process decision is ever made twice
2. **Specialists as versioned files** — peer review where there are no peers
3. **A structured trail** — issues with a taxonomy that answer, on their own,
   where the effort is going

The base is **3 rule files and 8 agents**. That is deliberately small: a rule
nobody read and an agent nobody invokes cost context in every session. The kit
grows by incident, not by anticipation — and `.claude/agents/_referencia/` holds
9 ready-made domain agents for when the need shows up.

```
Issue → branch → implement → agent review → PR → CI → merge → version → tag
```

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
