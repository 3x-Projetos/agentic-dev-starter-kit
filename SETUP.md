# Setup guide

Step-by-step deployment of the Agentic Dev Starter Kit into a new project.
For the model behind it (roles, end-to-end cycle, where automation stops), read
[`PROCESS.md`](PROCESS.md) (pt-BR).

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

## Free metrics

With the label namespaces applied consistently, these become queries — no
spreadsheet, no extra tooling:

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
