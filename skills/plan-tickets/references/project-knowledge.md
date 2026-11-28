# Project Knowledge

The planning skills (`design-data-model`, `create-prd`, `plan-phases`,
`plan-tickets`) share project context in `.agents/knowledge/` at the project
root:

- `project.md` — planning conventions and product context, used by all four.
- `data-model.md` — database conventions, used by `design-data-model` (its
  template is in that skill's `references/modeling-guide.md`).

These files belong to the project, not to a skill. Never write them inside a
skills folder: installs and updates delete and re-copy skill folders. Suggest
committing them so the whole team shares them.

## Load it (every run)

1. Read `.agents/knowledge/project.md` if it exists, and
   `.agents/knowledge/data-model.md` when this skill needs data model
   conventions.
2. Read `AGENTS.md` and `CLAUDE.md` at the project root, any nested ones in
   the folders the feature touches, and the files they link to for the topics
   below.
3. For each topic this skill needs, take the answer from the knowledge file
   first, then from `AGENTS.md` / `CLAUDE.md`. If the code contradicts either,
   the code wins; name the stale line in the report and offer to fix it.

## Topics

| Topic | What it answers | Needed by |
| --- | --- | --- |
| Save location | Where planning docs go, e.g. `docs/<feature-slug>/` | all four |
| Product and roles | What the product does; the real user roles and what each can do | `create-prd`, `plan-tickets` |
| Tenancy and scope | The ownership hierarchy, e.g. organization → workspace → user | `design-data-model`, `create-prd` |
| Compliance | Regulatory or customer requirements, e.g. GDPR, HIPAA, SOC 2 | `design-data-model`, `create-prd` |
| Codebase map | Apps and packages, where each layer lives, key integrations | all four |
| Issue tracker | Tool, team or project, labels, how parent/child and blocking are recorded | `plan-tickets` |
| Data model conventions | The areas in the Codebase Conventions table of `modeling-guide.md` | `design-data-model` |

## Create or extend it (only when needed)

- Every topic this skill needs is answered by the knowledge files,
  `AGENTS.md`, or `CLAUDE.md` → create nothing.
- Some topics are unanswered → fill only those:
  1. Answer from the code first: schema files, package layout, permission or
     role definitions, existing docs folders, tracker config, CI.
  2. Ask the user only what the code can't answer. One round, at most 4
     questions, recommended option first (use `AskUserQuestion` when
     available). Ask these before the skill's own questions; they don't count
     against its rounds.
  3. Still unknown → write `TBD` and use the skill's default.
  4. Write only the unanswered topics to `.agents/knowledge/project.md` (or
     `data-model.md`) using the template below. If the file exists, add the
     missing sections; don't rewrite sections someone else wrote.
- Once the answers are settled, show the user the sections you'll add and
  write them only after they confirm, before the skill's own output. If they
  decline, write nothing and use the answers for this run only.
- In the report, name the file you created or extended and suggest committing
  it.

## project.md template

Include only the sections you are filling.

````markdown
# Project Knowledge

> Shared context for the planning skills. Covers only what AGENTS.md and
> CLAUDE.md don't. Edit freely; the code wins when they disagree.

## Save location

`[path]/<feature-slug>/` — data-model.md, prd.md, plan.md, tickets.md

## Product and roles

[One sentence on what the product does.]

- [Role] — [what they do; what they can and can't see]

## Tenancy and scope

[Ownership hierarchy, e.g. organization → workspace → project. Which key
tenant-owned rows carry.]

## Compliance

- [Requirement] — [what it means for features: audit, retention, access, PII]

## Codebase map

| Area | Location | Notes |
| --- | --- | --- |
| [API] | `[path]` | [framework, pattern to follow] |
| [UI] | `[path]` | [component library] |
| [Database] | `[path]` | [ORM, migration command] |

Integrations: [background jobs, email, search, realtime, storage, payments]

## Issue tracker

[Tool; team or project; labels; how parent/child tickets and "blocked by"
are recorded.]
````
