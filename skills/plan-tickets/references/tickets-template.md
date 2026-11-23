# Tickets Template

Fill this exactly when writing `tickets.md`. Delete optional fields that would
be empty (**Depends on**, Explicit Non-Tickets).

## Template

```markdown
# [Feature Name] Tickets

Source PRD: `[path]/prd.md`
Source data model: `[path]/data-model.md`
Source phases: `[path]/plan.md`

---

# [Feature Name] Phase 1: [Phase name from plan.md]

**Label:** feat
**Size:** L
**Risk:** [Low / Medium / High]
**Description:** [The phase's "Done when" from plan.md.]

## Child Tickets

## Ticket 1: [Action title]

**Label:** feat
**Size:** S
**Risk:** Medium
**Sizing rationale:** [One line on delivery friction.]

**Description:** [1–2 sentences.]

**Agent Guidance:**

- Data: [Tables, columns, constraints, or "no schema change".]
- UI: [Screen, component, or existing pattern to reuse.]
- Tests: [Behavior, edge cases, and permission rules to cover.]
- PRD refs: [e.g. US-1, FR-2]

## Ticket 2: [Action title]

**Label:** feat
**Size:** XS
**Risk:** Low
**Sizing rationale:** [One line.]
**Depends on:** Ticket 1

**Description:** [1–2 sentences.]

**Agent Guidance:**

- [Layer]: [Guidance.]
- PRD refs: [refs]

---

# [Feature Name] Phase 2: [Phase name from plan.md]

**Label:** feat
**Size:** L
**Risk:** [Low / Medium / High]
**Description:** [The phase's "Done when" from plan.md.]

## Child Tickets

## Ticket 1: [Action title]

**Label:** refactor
**Size:** M
**Risk:** Medium
**Sizing rationale:** [One line.]
**Depends on:** Phase 1, Ticket 1

**Description:** [1–2 sentences.]

**Agent Guidance:**

- [Layer]: [Guidance.]
- PRD refs: [refs]

---

# Suggested Child Ticket Order

| Phase | Suggested order |
| --- | --- |
| Phase 1 | Ticket 1, then Ticket 2 |
| Phase 2 | Ticket 1 (after Phase 1, Ticket 1), in parallel with Phase 1, Ticket 2 |

# Explicit Non-Tickets

- [Only things someone might expect to be ticketed. Usually from "Not in this plan" in plan.md.]
```

## Example

A database foundation and two vertical slices from the plan bullets "Set up
the workflow archive data model", "Allow archiving a workflow", and "Show
the cases a workflow is assigned to". Tickets 2 and 3 can be built in
parallel because neither touches the schema.

```markdown
## Ticket 1: Establish workflow archiving database foundations

**Label:** feat
**Size:** XS
**Risk:** Medium
**Sizing rationale:** Small schema change kept in one ticket so later slices don't create competing migrations.

**Description:** Add the archive fields and permission needed before any archiving behavior ships.

**Agent Guidance:**

- Data: Add `archivedAt` and `archivedBy` to `workflows`; generate the migration with the project's migration tool.
- Permissions: Add and seed `WORKFLOWS_ARCHIVE` for admins.
- Data: No repository or service methods here; each slice adds what it first uses.
- Tests: Cover defaults and the permission seed.
- PRD refs: FR-4

## Ticket 2: Allow archiving a workflow

**Label:** feat
**Size:** S
**Risk:** Low
**Sizing rationale:** Cross-layer slice that mirrors the existing form archive pattern.
**Depends on:** Ticket 1

**Description:** Staff can archive a published workflow so it no longer appears when creating cases, without affecting cases already using it.

**Agent Guidance:**

- Service: Archive in a service method; reject drafts with a clear error.
- UI: Archive action on published workflow cards; archived workflows move to an Archived group.
- Permissions: Require `WORKFLOWS_ARCHIVE` on the server, not only in the UI.
- Tests: Archived workflows are excluded from the case-creation picker; existing cases keep working.
- PRD refs: US-3, FR-4

## Ticket 3: Show the cases assigned to a workflow

**Label:** feat
**Size:** S
**Risk:** Low
**Sizing rationale:** Read-only side sheet reusing the existing sheet and list patterns.
**Depends on:** Ticket 1

**Description:** Staff can see which cases use a workflow, from the workflow card and page, and remove a case from it.

**Agent Guidance:**

- API: Add a paginated read for cases by workflow.
- UI: Open a side sheet from the card and page; empty state with one clear action.
- Tests: Only cases in the user's organization appear.
- PRD refs: US-5

# Suggested Child Ticket Order

| Phase | Suggested order |
| --- | --- |
| Phase 1 | Ticket 1, then Tickets 2 and 3 |
```
