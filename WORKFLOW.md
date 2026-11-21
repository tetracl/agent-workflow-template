# Agent workflow

A development workflow for coding agents that is nothing but instructions:
one `AGENTS.md` and five skills. No scripts, nothing to install. It assumes
one maintainer who starts every task, and agents that work through the
maintainer's own GitHub login.

- [Start a new project](#start-a-new-project)
- [Add it to an existing project](#add-it-to-an-existing-project)
- [Day to day](#day-to-day)
- [What agents remember](#what-agents-remember)
- [When you do not want to merge](#when-you-do-not-want-to-merge)
- [What is in the repository](#what-is-in-the-repository)
- [What it does not do](#what-it-does-not-do)
- [Keeping projects up to date](#keeping-projects-up-to-date)
- [Publishing this as a template repository](#publishing-this-as-a-template-repository)

## What it gives you

- One task is one small pull request, with tests written first.
- Every pull request is reviewed by fresh, read-only reviewers before you
  read it, and every finished milestone gets a gate review.
- Tasks are GitHub issues: sub-issues of their milestone, marked as blocked
  by what they need. A merged pull request closes its task.
- Design changes come to you as ADRs with options. Your answer is recorded
  as you gave it.
- You merge, or tell the agent to merge a named pull request.

## Start a new project

You need `git` and the GitHub command line `gh`, version 2.94 or newer,
logged in. Replace `<owner>` and `<project>` throughout.

**1. Create the repository from the template**

    gh repo create <owner>/<project> --template <owner>/agent-workflow-template --private --clone
    cd <project>

Or use the "Use this template" button on the template's page on GitHub,
then clone the new repository. Use `--public` for a public project.

**2. Make it this project's**

- `README.md`: replace it with the project's own.
- `AGENTS.md`: fill in the four placeholders.

| Placeholder in `AGENTS.md` | What to write |
| --- | --- |
| The opening paragraph | What the project is and who uses it |
| Rule 11 | The project's own rules, for example "no unsafe code" |
| Checks | The commands for the `quick`, `full` and `milestone` tiers. Leave them empty if there is no build yet: the first task of the first milestone will propose them |
| Protected paths | What must never change, and the test or command that checks it; or delete the example row |

**3. Write the design and the roadmap**

Open an agent in the repository and say:

> Draft the design and roadmap.

It asks you what it cannot look up, then writes `docs/design.md` and
`docs/roadmap.md`. Read both. Check that every limit is a number, that each
acceptance bullet is something one test can decide, and that everything you
have not decided is under Open questions.

**4. Make the first commit**

    git add -A
    git commit -m "Project setup, design and roadmap"
    git push

This is the only commit that goes straight to the main branch.

**5. Optional: require pull requests on the main branch**

In the repository's settings on GitHub, under Branches or Rules, require a
pull request before merging into `main`. No approvals are needed. This is
the one safeguard GitHub enforces for a single account. It is available on
public repositories, and on private ones with a paid plan.

**6. Plan the first milestone**

> Plan milestone M1.

The agent writes the plan as a pull request. Read it, then:

> Merge pull request 1.

> Create the issues for M1.

It creates the milestone issue and one issue per task, and reports the list.
The labels it needs are created on the way.

**7. Start working**

> Do the next task.

From here on, see [Day to day](#day-to-day).

**Checklist for a new project**

- [ ] Repository created from the template and cloned
- [ ] `README.md` replaced
- [ ] `AGENTS.md`: opening paragraph, project rules, checks, protected paths
- [ ] `docs/design.md` and `docs/roadmap.md` written and read
- [ ] First commit pushed
- [ ] Pull requests required on `main` (optional)
- [ ] Milestone M1 planned, plan merged, issues created

## Add it to an existing project

A template only works for new repositories. For an existing one:

1. Copy `AGENTS.md`, `CLAUDE.md`, `.agents/` and `docs/adr/README.md` from
   this repository into the project's root. Leave out `README.md` and
   `WORKFLOW.md`, or keep `WORKFLOW.md` for reference.
2. If the project already has agent instructions or skill folders, such as
   an older `CLAUDE.md` or `.claude/skills`, remove them or merge their
   project-specific rules into `AGENTS.md`, so that the agent does not find
   two sets of instructions.
3. Fill in `AGENTS.md` as in step 2 above.
4. Keep the existing design and roadmap documents. Give the next
   milestone's roadmap entry the five fields and testable acceptance
   bullets; the rules are in
   `.agents/skills/draft-design/references/design-and-roadmap.md`. An agent
   can do this: "Restructure the roadmap entry for the next milestone to fit
   the structure rules."
5. Commit through a pull request, then continue with step 6 above.

## Day to day

Open an agent in the repository and say one of these. Use a fresh session
for each task.

| You say | What happens |
| --- | --- |
| "Plan milestone M1." | A plan as a pull request |
| "Create the issues for M1." | After the plan is merged: the milestone issue and one issue per task |
| "Do the next task." | The agent picks the first unblocked task, labels it `in-progress`, writes the tests first, implements, opens a draft pull request, has it reviewed, and marks it ready |
| "Continue." | The agent picks up the task of the branch it is on, including your comments on its pull request |
| "Merge pull request 12." | The agent checks that it is ready and merges it |
| "Review pull request 12." | A review by fresh read-only reviewers, on its own |
| "Run the gate for M1." | Evidence, two independent review passes, a review record, and a triage with you |
| "Draft an ADR for ..." | An ADR with options as a pull request; affected tasks are marked blocked until you decide |

When the agent stops and asks, answer in the session. A design question is
recorded with your answer word for word.

If your agent runs in a sandbox, let it run `git` and `gh` outside it.
Everything else can stay inside.

## What agents remember

Agents may keep their own memory, but nothing the next session needs lives
only there. `AGENTS.md` tells them where things go instead:

| Kind of information | Where it goes |
| --- | --- |
| Where a task stands: what is done, what is left, what failed | A comment on the task's issue, written whenever a session stops before the task is finished |
| Something true for the whole project | A line under Lessons in `AGENTS.md`, through a small pull request that you merge |
| A decision of yours | The plan's Decisions table or the ADR |
| Your personal preferences | The agent's own memory, or your own user-level instructions |

This is what lets a different agent continue a task: "Continue." makes it
read the issue's comments first. On a public repository these notes are
public; the rules against host names, paths and raw output apply to them.

## When you do not want to merge

A closed pull request alone tells the agent nothing, and it will ask. Say
which of these you mean:

| You want | Do |
| --- | --- |
| Changes to the same work | Comment on the pull request, leave it open, and say "Continue." |
| The task done again from scratch | Say "Start task #<n> over." The agent removes the `in-progress` label and leaves the old branch in place |
| The task dropped | Say "Drop task #<n>." The plan is changed in a pull request and the issues are brought in line with it |

## What is in the repository

| File | Purpose |
| --- | --- |
| `AGENTS.md` | The rules, the list of skills, your check commands and protected paths |
| `CLAUDE.md` | One line that makes Claude Code read `AGENTS.md` |
| `.agents/skills/draft-design/` | Drafting the design and roadmap, with the structure rules and an example |
| `.agents/skills/plan-milestone/` | Planning a milestone and creating its issues, with the plan template and an example |
| `.agents/skills/implement-task/` | One task from start to a reviewed pull request |
| `.agents/skills/review/` | Task review and milestone gate, with the four reviewer briefs |
| `.agents/skills/decision/` | Short decisions and ADRs, with the ADR template |
| `docs/adr/README.md` | The index of decisions |
| `WORKFLOW.md` | This file |

Agents that read `AGENTS.md` need nothing more: it lists the skills and
where they are. Agents that look for skills in `.agents/skills` also find
them there by name.

## What it does not do

- It does not stop two sessions from working on the same task. You start
  the tasks; the `in-progress` label is a marker, not a lock.
- It enforces nothing against an agent that ignores its instructions. The
  safeguards are the rules, your reading of each pull request, and pull
  requests being required on the main branch.
- It does not filter what agents post. `AGENTS.md` forbids raw output and
  secrets; on public repositories GitHub's secret scanning is the backstop.
- It has no status view across projects. Use the issue lists, or a GitHub
  project board with the milestone issues.

## Keeping projects up to date

A repository created from the template does not follow it. When you improve
a rule or a skill, change it in the template repository first, then copy
`.agents/` into each project through a pull request. Changes to `AGENTS.md`
are merged by hand, because each project's copy has its own rules, checks
and protected paths.

A rule you find yourself explaining to an agent more than once belongs in
`AGENTS.md`: in the project's copy if it is about the project, in the
template's if it is about the workflow.

## Publishing this as a template repository

Done once, from the unpacked folder:

    cd agent-workflow-template
    git init -b main
    git add -A
    git commit -m "Agent workflow template"
    gh repo create <owner>/agent-workflow-template --public --source . --push
    gh repo edit <owner>/agent-workflow-template --template

The last command marks the repository as a template, which makes
`gh repo create --template` and the "Use this template" button work.

## Where it comes from

The rules are what test runs with real agents showed to matter: tests
first, a task never changes its own checks, a deviation from the design
needs a decision, reviewers read a pinned commit, an agent never writes as
the maintainer. This version itself has not yet been run by an agent.
