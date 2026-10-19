# Agentic Dev Starter Kit

A seed for starting a project with agent-assisted development: a single work
loop, specialists as versioned files, and a structured trail that turns into
metrics without a spreadsheet.

Copy it at the start of a project, fill it in, and get to work.

This README is the **deployment guide** — step by step, with commands. To
understand the model before adopting it (roles, the end-to-end cycle, and where
automation stops), read [`PROCESS.md`](PROCESS.md).

---

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

---

## Prerequisites

| Tool | What for |
|---|---|
| `git` | Obvious |
| `gh` CLI, **authenticated** | Issues and PRs are the spine of the method. Without it, nothing works |
| Claude Code | The agents |
| Stack runtime | Defined in step 2 |

Check with `gh auth status`. If it fails, run `gh auth login` before anything else.

**You don't need to know git.** The `git-specialist` agent exists for that:
describe your intent in plain language — "I want to start this task", "I want to
ship this to production", "I think I deleted something" — and it translates,
explains what is about to happen **before** running it, and executes.

---

## Deployment — 7 steps

Steps 3 to 7 take ~40 minutes. Step 2 takes as long as it needs to.

### Step 1 · Copy the kit

```bash
cp -r agentic-dev-starter-kit/. <new-project>/
cd <new-project>
```

### Step 2 · Decide the stack

> Invoke `stack-advisor` to lead the stack choice.

**This is the step that costs the most if skipped**, and the only one that
produces decisions that are expensive to undo. The stack comes from need — not
from what the agent suggests first, nor from what you saw in an article.

The agent runs six phases: it gathers **constraints** before mentioning any
technology, separates the decisions that are **expensive to undo** from the cheap
ones, presents 2 or 3 options per expensive decision with the "why not" of each
discarded one, checks on the web that each candidate is still alive, proposes a
**walking skeleton** to prove the choice before committing to it, and records
everything in decision `0001`.

The detail that makes this work when you don't know the technologies: phase 1
only asks questions **you can answer** — how many users, where it runs, who
maintains it in two years, what the company already uses. The recommendation must
point to which of your answers produced it. A recommendation that doesn't trace
back to one of your constraints is the agent's preference, and you can reject it
on that basis.

At the end, the agent hands you five questions to ask of its own recommendation —
they exist to give leverage to someone who isn't an expert. Ask all of them.

**Only move on when decision `0001` is filled in.** A constraint you couldn't
answer is left blank on purpose: an empty field there is a risk warning.

### Step 3 · Fill in `CLAUDE.md`

Replace the `<placeholders>`: name, what it is, stack, environments.

**Leave the "Critical domain rules" section empty.** It gets filled with what
actually breaks. A rule written in anticipation isn't followed and costs context
in every session — the ones that matter are born from incidents, with the cost
already paid.

### Step 4 · Set up git and GitHub

> Invoke `git-specialist` to do the initial repository setup.

It covers: `git init`, first commit, a stack-appropriate `.gitignore`, creating
the remote repo, a `dev` branch, **`dev` as the default branch** — so that a PR
opened by mistake doesn't point at production — and protection for `main`.

If your GitHub plan doesn't allow branch protection on a private repo, it warns
you and records in `.claude/decisions.md` that the protection is procedural, not
technical. It's worth knowing when the gate depends on discipline instead of
tooling.

### Step 5 · Create the labels

This is the foundation of the metrics. Run:

```bash
# type: — exactly one per issue, required
gh label create "type:bug"         -c "#d73a4a" -d "Something isn't working"
gh label create "type:feature"     -c "#e36cc9" -d "New functionality"
gh label create "type:improvement" -c "#e36cc9" -d "Iteration on an existing feature"
gh label create "type:docs"        -c "#d4c5f9" -d "Documentation"
gh label create "type:chore"       -c "#d4c5f9" -d "Maintenance, refactor, tooling"

# priority: — exactly one per issue, required
gh label create "priority:high"    -c "#B60205" -d "Urgent"
gh label create "priority:medium"  -c "#FBCA04" -d "Default"
gh label create "priority:low"     -c "#0E8A16" -d "When there's spare time"

# state: — zero or one. No state = active work
gh label create "state:next"       -c "#0E8A16" -d "Next work session"
gh label create "state:later"      -c "#C2E0C6" -d "Backlog, no rush"
gh label create "state:idea"       -c "#FEF2C0" -d "Just a note, may never be done"
gh label create "state:blocked"    -c "#E99695" -d "Blocked by a dependency"

# area: — design it for YOUR project, one per screen/module
gh label create "area:<module>"    -c "#1D76DB" -d "<description>"
```

The first three namespaces are universal — copy them as they are. **`area:` is
the only one you design**: start with one label per main screen or module and add
more as the product grows. It is where the answer to "which part of the system
consumes the most time" comes from.

### Step 6 · Adjust the CI

Edit `.github/workflows/ci.yml` for the stack decided in step 2. **Do it now, not
later** — "later" never arrives, and the project goes to production with no
automatic gate.

Agent review covers **judgment**; CI covers **regression**. They are different
things and the method needs both.

### Step 7 · Open issue #1

> Invoke `issue-manager` to create the project's initial setup issue.

The method applies from the first commit. If the setup didn't go through the loop,
the loop has already been broken once — and the second time gets easier.

---

## The work cycle

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

### Decision defaults

They exist to eliminate repeated deliberation. When in doubt, the answer is
already given: create an issue → **create** · big commit → **split** · needs a
test → **add** · update docs → **update** · bump the version → **bump** · record
a decision → **record**.

### The most important versioning rule

**The bump goes in the change's PR**, as the last commit on the branch — never in
a separate commit after the merge. That way every merge into `main` corresponds
to exactly one version, and the history answers "what shipped when" without
archaeology.

---

## The issue method

The issue is the unit of work **and** the unit of measurement. If it's poorly
made, the work is left without a trail and the metrics lie.

**Title:** `<type>(<area>): <concrete symptom>` — describe what the person sees,
and the cause after a dash when you know it.

```
fix(export): spreadsheet comes out 3h off — conversion treats UTC as local time
feat(admin): per-role visibility control for shortcuts
docs: Jun 2 incident — login failure (root cause + workaround)
```

"Fix export bug" is not a title: it doesn't let you prioritize without opening
the issue.

**Body:** context · expected behavior · current behavior (bugs, with numbered
steps) · **verifiable** acceptance criteria.

**Size:** an issue fits in one work session. If it doesn't, break it down — a
parent issue with the goal, children that can be delivered independently, linked
by `Part of #N`.

The full standard is in `.claude/agents/issue-manager.md`.

---

## Free metrics

With the namespaces applied consistently, this becomes a query — no spreadsheet,
no extra tooling:

```bash
# Code health: bug vs feature ratio
gh issue list --state all --label "type:bug"     --limit 500 --json number | jq length
gh issue list --state all --label "type:feature" --limit 500 --json number | jq length

# Where the effort is really concentrated
gh issue list --state all --limit 500 --json labels \
  --jq '.[].labels[].name' | grep '^area:' | sort | uniq -c | sort -rn

# Is the urgency real or just a label? (high still open)
gh issue list --state open --label "priority:high" --json number,title,createdAt
```

The second query is usually the one that surprises: one front concentrating most
of the issues is planning information nobody has without the taxonomy — and it
only shows up if the label is applied with discipline. That is why the
responsibility belongs to `issue-manager`, not to whoever opens the issue.

---

## The 8 base agents

| Agent | Role | When |
|---|---|---|
| `stack-advisor` | Leads the stack choice from constraints | Once, before code — and when revisiting |
| `issue-manager` | Issue standard and label taxonomy | Before all work |
| `git-specialist` | Operates git for those who don't want to learn git | Setup and any non-trivial operation |
| `tech-lead` | Plans before the code exists | Task with more than one path |
| `code-reviewer` | The peer review you don't have | Always, before the PR |
| `test-specialist` | Safety net | New feature or fixed bug |
| `security-reviewer` | Offensive mindset | Auth, permissions, input, external execution |
| `docs-writer` | Documentation by audience, kept alongside the code | Behavior, setup, deploy or contract changed |

Native Claude Code format (`.claude/agents/<name>.md` with frontmatter),
invocable by name.

The other 9 are in `.claude/agents/_referencia/`, each with its promotion trigger
documented. The first one to promote is usually `release-manager` — the day the
system gets real users.

---

## How the kit grows

The rule holds for rules and for agents: **only the third time the pain shows up.**

| Add | When |
|---|---|
| New rule | A pattern repeated 3+ times without being written down, or an error happened that the rule would have prevented |
| New agent | A front started consuming recurring time that the 8 base agents don't cover |
| New `area:` | A module became its own front of work |

**Don't create by anticipation.** A rule with no incident behind it is noise
consumed in every session and followed by no one. An agent created "for when it's
needed" is never invoked and becomes surface area to maintain.

And remove: a rule that is systematically bypassed is either wrong or costs more
than it delivers. `.claude/rules/self-management.md` governs this — it is the
most important rule in the kit, because it's what keeps the set from becoming
dead weight.

---

## Common deployment mistakes

| Mistake | Consequence |
|---|---|
| Letting the stack be suggested without gathering constraints | The choice becomes the agent's preference, and you have no basis to reject it |
| Accepting a single option as the recommendation | Without the "why not" of the alternatives, there is no auditable decision |
| Filling in a constraint you don't know the answer to | A guess becomes expensive debt; a blank field is a warning, not an oversight |
| Skipping the walking skeleton | The wrong stack only shows up at the first serious integration, when switching already costs |
| Bringing the rule set of a mature project | Context blown on day 1, rules nobody follows |
| Enabling all the output agents | None gets used; the base of 8 is what fits in your head |
| Filling in "critical domain rules" with hypotheses | Permanent noise; the section should start empty |
| Skipping the issue "because it's quick" | The trail is lost and the metrics lie |
| Applying labels haphazardly | The taxonomy only works if it is consistent |
| Leaving CI for later | "Later" never arrives |
| Version bump in a separate commit after the merge | The correspondence between merge and version no longer exists |
| Promoting a reference agent without filling in the conventions | It reports preference as if it were a defect |

---

## Structure

```
README.md                          deployment guide — this file
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
