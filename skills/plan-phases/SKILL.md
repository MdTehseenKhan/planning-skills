---
name: plan-phases
description: >
  Break a finished PRD into an implementation plan: phases of plain-English,
  actionable items that each become one ticket. Reads the PRD, skims the
  relevant code, asks only the questions needed to settle phase order and
  scope, asks where to save, and writes plan.md. Reads shared project knowledge from
  .agents/knowledge/ and fills gaps there when AGENTS.md / CLAUDE.md don't
  cover them. Use when the user asks to
  create an implementation plan, break a PRD into phases or tickets, plan a
  feature or refactor from a PRD, or runs /plan-phases.
metadata:
  version: "1.2.0"
---

# Plan Phases

Turn a PRD into a short plan that gives the whole implementation at a glance
and can be turned straight into tickets. The plan comes **after** the PRD: it
does not restate the problem, goals, or requirements. It orders the work.

## When to Apply

- User asks to create an implementation plan from a PRD or spec
- User asks to break a feature or refactor into phases or tickets
- User runs `/plan-phases`

## References

- `references/project-knowledge.md` — how to load, and when to create, the
  shared project knowledge in `.agents/knowledge/`. Read first.
- `references/plan-template.md` — the output template and an example phase.
  Read before writing.

## What a Good Plan Looks Like

- **Phases** deliver something usable or reviewable on their own, ordered so
  earlier phases unblock later ones.
- **Each top-level bullet is one ticket**, or a small group of bullets
  together make one ticket.
- **Every bullet is a vertical slice**: one user-visible behavior, delivered
  end to end (database, API, UI, permissions, tests). Split work by behavior,
  never by layer: no "Build the API" or "Build the UI" bullets. The only
  usual exception is one database-foundation bullet at the start of Phase 1
  that owns all schema and migration work, or one at the start of each phase
  when the data model is split by phase.
- **Phases are delivery, not testing.** Load tests, acceptance runs, and
  pilots go in a **Test plan** section, not a phase. Non-test release work
  (docs, rollout) goes in the phase that ships the behavior.
- **Sub-bullets carry what the ticket needs**: fields, button states, edge
  cases, permission rules, which existing pattern to follow.
- **Plain-English behavior**, not code tasks. Write "Allow archiving a
  workflow", not "Add `archived_at` column". Mention a technical detail only
  when the ticket can't be understood without it, e.g. "required in the API,
  optional in the database".
- **No tables or diagrams.** Short, scannable, readable by product and
  engineering alike.
- **Nothing duplicated from the PRD.** Problem, metrics, personas, and risks
  stay in the PRD; the plan links to it.

## Related Skills (optional)

This skill is one step of a planning flow: `design-data-model` →
`create-prd` → `plan-phases` → `plan-tickets`. The other skills help but are
never required.

When you suggest another step, check whether that skill is available (in
your skills list, or in the project's or user's skills folder, e.g.
`.agents/skills/` or `.claude/skills/`). If it is, offer to run it. If not,
say it's optional and give the install command:
`npx skills add MdTehseenKhan/planning-skills --skill <name>`. Never install a skill
without the user's OK, and never stop because one is missing.

## Workflow

Copy and track:

```
- [ ] Load project knowledge
- [ ] Get the PRD
- [ ] Read the PRD and skim the relevant code
- [ ] Draft the phases
- [ ] Ask the questions that block tickets
- [ ] Save project knowledge, if missing
- [ ] Ask where to save the plan
- [ ] Write plan.md and self-check
- [ ] Report
```

### 1. Load project knowledge

Follow `references/project-knowledge.md`. This skill needs the topics
Save location and Codebase map. Note which ones the knowledge files, `AGENTS.md`, and `CLAUDE.md`
already answer; you'll fill only the rest.

### 2. Get the PRD

Accept a file path, a pasted PRD, or a link (an issue tracker, a wiki, a
shared doc — use the matching connector tool to read it). If the user gave none, ask for
it. If there is no PRD at all, offer `create-prd` (see Related Skills), or work
from whatever spec or notes the user has instead.

### 3. Read the PRD and skim the code

Read the whole PRD, plus `data-model.md` if one sits next to it. Then briefly skim the code areas it touches (start
from the Codebase map in project knowledge, then `CLAUDE.md`, `AGENTS.md`,
or the README) so you know what already exists. The goal is to avoid asking the user things the code answers,
and to notice where an item is bigger or smaller than the PRD implies.

Keep this brief — minutes, not a full audit. Do not write a "current state"
section into the plan.

### 4. Draft the phases

Before asking anything, draft the phase outline yourself:

- Group requirements into 2–5 phases. Prefer shipping a narrow, complete
  slice first over building every backend piece before any UI. A phase is a
  set of slices, never a layer ("Backend", "Frontend", "API" are not phases).
- Turn each requirement into one or more ticket-sized vertical slices. If a
  bullet would take more than a few days, split it by behavior (one action,
  one state, one user type, one section family), not by layer.
- If the feature needs new tables, put one "Set up the [feature] data model"
  bullet first in Phase 1. It covers schema, migrations, enums, access
  policies, and permission seeds, so later slices don't fight over
  migrations and can be built in parallel. When early phases ship
  separately and don't need the later tables, ask whether to split it: each
  phase then starts with the schema it needs.
- If a large phase mixes several screens or workflows, split it into
  sequential phases by screen or workflow rather than keeping one big phase.
- Items that don't depend on any phase (for example an existing ticket that
  can start any time) go under **Parallel work (outside phases)**.
- Check whether PRD items are already built or already ticketed (in the code
  or, when one is connected, in the issue tracker named in project
  knowledge). Built items go under "Not in this plan"; ticketed items keep
  their ticket ID.
- Note dependencies that aren't obvious from the order.
- Note every point where the PRD is ambiguous, contradicts itself, or leaves
  a decision open.

### 5. Ask the questions that block tickets

First, if step 1 left project knowledge topics the code couldn't answer,
ask that one short round as `references/project-knowledge.md` describes.
Then ask only what changes the plan. Good questions:

- Phase order or what ships first, when there is a real tradeoff
- What to defer out of this plan
- Ambiguities that would produce two different tickets depending on the answer
- Grouping, when it is unclear whether items are one ticket or several

Do not ask about anything the PRD, code, or project knowledge already
answers, or about implementation details that belong in the ticket itself.

Rules:

- Batch questions; at most 4 per round and 2 rounds. Use `AskUserQuestion`
  when available, with your recommended option first.
- Share the draft phase names with the first round so the user can correct
  the shape early.
- Anything still unresolved after two rounds goes into **Open questions**
  with an owner if known. Do not keep asking.

### 6. Save project knowledge, if missing

Skip this step when every topic from step 1 was already answered. Otherwise
write only the unanswered topics to `.agents/knowledge/project.md`, as
`references/project-knowledge.md` describes. Record project-wide context
only, never anything specific to this feature.

### 7. Ask where to save the plan

Always ask where to save the output. Suggest the save location from project
knowledge when there is one, otherwise `plan.md` in the same folder as the
PRD when the PRD is a local file; otherwise offer
`docs/<feature-slug>/plan.md`. Do not overwrite an existing file without
confirming.

### 8. Write plan.md

Read `references/plan-template.md` and fill it exactly. It also has an example
phase showing the level of detail to aim for. Delete optional parts that would
be empty.

- **PRD:** a relative path or URL to the PRD.
- **Goal:** 1–3 sentences on what will be true when everything ships.
- **Done when:** one sentence per phase, from the user's point of view. It
  doubles as the milestone description.
- **Depends on:** only when the order isn't obvious. Use the phase line for a
  whole-phase dependency, or `(needs: ...)` on a single bullet.
- **Test plan:** load, acceptance, and pilot checks that must pass before
  rollout, with when they run. Delete the section if empty.
- **Parallel work (outside phases):** items that can start any time. Delete
  the section if empty.
- **Open questions:** anything that blocks writing a ticket. Delete the
  section if empty.
- **Not in this plan:** only things someone might reasonably expect to be
  included. Delete the section if empty.

Self-check before finishing:

- [ ] Every PRD requirement maps to a bullet, or appears under "Not in this plan"
- [ ] Every top-level bullet could be pasted into a ticket title and understood
- [ ] No bullet is a code task; technical details appear only where necessary
- [ ] Every bullet is a vertical slice; no phase or bullet is a single layer,
      except the optional data-model bullet first in Phase 1 (or first in
      each phase when the data model is split by phase)
- [ ] No phase is only testing; tests and pilots are in the Test plan
- [ ] No tables, diagrams, or sections copied from the PRD
- [ ] Every phase has a "Done when"

### 9. Report

Give the saved path, the phase names with item counts, and any open
questions. Tickets get created only once Open questions is empty or every
question has an owner; only then suggest `plan-tickets` to turn the plan into
`tickets.md`, following Related Skills. If you created or extended a
knowledge file, name it and suggest committing it.
