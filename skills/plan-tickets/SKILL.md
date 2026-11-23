---
name: plan-tickets
description: >
  Turn a phase plan and its PRD into tracker-ready tickets: one parent ticket
  per phase and vertical-slice child tickets with label, size, risk, sizing
  rationale, short agent guidance, dependencies, and a parallel build order.
  Reads plan.md, prd.md, and data-model.md, skims the relevant code, asks
  only the questions that change the tickets, asks where to save, and writes
  tickets.md. Reads shared project knowledge from .agents/knowledge/ and
  fills gaps there when AGENTS.md / CLAUDE.md don't cover them. Use when the
  user asks to create, plan, or break a plan into tickets or issues (Linear,
  Jira, GitHub Issues, etc.), or runs /plan-tickets.
metadata:
  version: "1.1.0"
---

# Plan Tickets

Turn `plan.md` into tickets that an engineer or agent can pick up without
reading the whole PRD. It is the last step of the planning flow:

```
design-data-model  →  create-prd  →  plan-phases  →  plan-tickets
data-model.md         prd.md         plan.md         tickets.md
```

The plan decides **what** ships in which phase. This skill decides how each
item is sliced, sized, ordered, and which tickets can be built in parallel.

## When to Apply

- User asks to create tickets or issues from a plan or PRD
- User asks to size, slice, or order implementation work
- User runs `/plan-tickets`

## References

- `references/project-knowledge.md` — how to load, and when to create, the
  shared project knowledge in `.agents/knowledge/`. Read first.
- `references/tickets-template.md` — the output template and an example
  ticket. Read before writing.
- `references/sizing-guide.md` — how to pick Label, Size, and Risk.

## Vertical Slices

Every child ticket is a vertical slice: one user-visible behavior, shipped
end to end with the database access, API, service, UI, permissions, and
tests it needs. A reviewer can try it, and it can merge on its own.

- **Split by behavior, never by layer.** No "Add the API for X", "Build the
  UI for X", or "Write tests for X" tickets. If a slice is too big, split it
  by action, state, user type, or section family, each still end to end.
- **One horizontal exception:** a database-foundation ticket first in
  Phase 1 that owns all schema, migrations, enums, access policies, and
  permission seeds from `data-model.md`. It adds no repository, service, or API code; each
  later slice adds the data access it first needs.
- **Refactors and spikes** are the other allowed non-feature tickets. A
  refactor must state the benefit; a spike must produce findings, not code.
- **Backend-only behavior** (a job, a webhook, an email) is still a slice
  when it is observable: a record changes, an email arrives, a log appears.

## Parallel Work

- Add **Depends on** only for a real blocker: the ticket needs a table,
  endpoint, or component another ticket creates. Order alone isn't a
  dependency.
- Dependencies are per ticket, not per phase. A later-phase ticket can start
  as soon as its dependencies ship.
- Tickets built at the same time must not edit the same files. Schema,
  migrations, enums, and shared routers are the usual clashes; keep them in
  the foundation ticket or add a dependency.
- Show what runs together in **Suggested Child Ticket Order** using "and"
  ("Ticket 1, then Tickets 2 and 3").

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
- [ ] Get the plan and PRD
- [ ] Read them and skim the relevant code
- [ ] Draft the tickets
- [ ] Ask the questions that change the tickets
- [ ] Save project knowledge, if missing
- [ ] Ask where to save
- [ ] Write tickets.md and self-check
- [ ] Report
```

### 1. Load project knowledge

Follow `references/project-knowledge.md`. This skill needs the topics
Save location, Product and roles, Codebase map, and
Issue tracker. Note which ones the knowledge files, `AGENTS.md`, and `CLAUDE.md`
already answer; you'll fill only the rest.

### 2. Get the plan and PRD

Accept file paths, pasted content, or links (an issue tracker, a wiki, a
shared doc — use the matching connector tool). Look for `prd.md` and `data-model.md` next
to `plan.md`. If there is no plan, offer `plan-phases` (see Related
Skills), or derive 2–5 vertical-slice phases from the PRD or spec yourself
and say so in the report.

If the plan still has unowned **Open questions**, list them and ask whether
to continue. Tickets that depend on an open question get a note in their
Description.

### 3. Read and skim the code

Read the whole plan, the PRD's requirements, and the data model. Then skim
the code each phase touches (start from the Codebase map in project
knowledge, then `CLAUDE.md`, `AGENTS.md`, or the README) to find the existing patterns, components, and
services to reuse. That is what goes into Agent Guidance. Keep this brief.

### 4. Draft the tickets

- Each plan phase becomes an L-sized parent ticket. Its Description is the
  phase's **Done when**.
- Each top-level plan bullet becomes one child ticket. Merge small bullets
  that only make sense together; split a bullet that would be XL.
- If the plan has a layer bullet ("Build the API for X"), fold it into the
  slices that use it and say so in the report.
- Title: a plain-English action ("Allow archiving a workflow").
- Description: 1–2 sentences on what ships and why.
- Agent Guidance: 3–6 bullets prefixed by layer (`Data:`, `API:`,
  `Service:`, `UI:`, `Permissions:`, `Security:`, `Tests:`). Only what isn't
  obvious: the pattern to reuse, the layer that owns a rule, edge cases from
  the plan's sub-bullets. End with `PRD refs:`.
- Size and risk each ticket using `references/sizing-guide.md`.
- Add **Depends on** and build the order table as described in Parallel Work.

### 5. Ask the questions that change the tickets

First, if step 1 left project knowledge topics the code couldn't answer,
ask that one short round as `references/project-knowledge.md` describes.
Then ask only what would produce different tickets. Good questions:

- Whether a large item should be one ticket or split, and how
- Whether something unclear should be a spike first
- Whether two items can safely run in parallel when they may touch the same
  area

Do not ask about anything the plan, PRD, code, or project knowledge already
answers.

Rules:

- Batch questions; at most 4 per round and 2 rounds. Use `AskUserQuestion`
  when available, with your recommended option first.
- Share the draft ticket titles per phase with the first round.
- Anything still unresolved goes into the affected ticket's Description as
  "Open: …". Do not keep asking.

### 6. Save project knowledge, if missing

Skip this step when every topic from step 1 was already answered. Otherwise
write only the unanswered topics to `.agents/knowledge/project.md`, as
`references/project-knowledge.md` describes. Record project-wide context
only, never anything specific to this feature.

### 7. Ask where to save

Always ask where to save the output. Suggest the save location from project
knowledge when there is one, otherwise `tickets.md` next to `plan.md`
and offer `docs/<feature-slug>/tickets.md` as an alternative. Do not
overwrite an existing file without confirming.

### 8. Write tickets.md

Fill `references/tickets-template.md` exactly. Remove its comments and any
optional fields that would be empty.

Self-check before finishing:

- [ ] Every plan bullet maps to a ticket, or to Explicit Non-Tickets
- [ ] Every child ticket is a vertical slice; the only layer ticket is the
      optional database foundation first in Phase 1
- [ ] No ticket is XL; most are XS–M, and the sizes vary
- [ ] Every ticket has Label, Size, Risk, Sizing rationale, Description, and
      Agent Guidance ending in PRD refs
- [ ] Every Depends on points to an earlier ticket, with no loops
- [ ] Tickets shown as parallel don't edit the same schema, migrations,
      enums, or shared routers
- [ ] Each ticket reads in under a minute

### 9. Report

Give the saved path, the ticket count per phase, the size distribution, any
L tickets that could still be split, which tickets can start in parallel,
and any open items. If you created or extended a knowledge file, name it and
suggest committing it. If an issue tracker is connected (Linear, Jira, GitHub
Issues, or similar, through a connector or CLI), offer to create the
tickets there: parent issues per phase, child issues under them, and each
Depends on as a "blocked by" relation. Check for existing issues first, and
create nothing until the user confirms the destination (team, project, or
repo). Suggest the tracker and destination named in project knowledge.
