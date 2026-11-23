# Tickets Template

Fill this exactly when writing `tickets.md`. Delete optional fields that would
be empty (**Depends on**, Open notes, Explicit Non-Tickets, the existing-issue
section).

Each child ticket has four parts:

- **Description:** a short problem paragraph: what happens today, and why
  the change matters. Readable by product and engineering alike.
- **What to build:** the action items, in plain language, with UI terms in
  bold. Nested bullets for lists of rules or options.
- **Acceptance criteria:** checkboxes a reviewer can test: the main behavior,
  edge cases, permissions, and anything that must not change.
- **Agent Guidance:** technical detail only (paths, patterns to reuse, the
  layer that owns a rule, tests). In trackers that support it, it goes in a
  collapsible "Agent notes" section.

## Template

```markdown
# [Feature Name] Tickets

Source PRD: `[path]/prd.md`
Source data model: `[path]/data-model.md`
Source phases: `[path]/plan.md`

---

# [Feature Name] Phase 1: [Phase name from plan.md]

**Label:** feat
**Size:** [Rolled up from the child tickets: S, M, or L]
**Risk:** [Low / Medium / High]
**Sizing rationale:** Rolled up from its tickets ([child sizes]): about [N] days of review and verification.
**Description:** [1–2 sentences on what's wrong today that this phase fixes.]

Done when: [The phase's "Done when" from plan.md.]

**Acceptance criteria**

- [ ] [Phase-level outcome a reviewer can check; 3–4 items.]

## Child Tickets

## Ticket 1: [Action title]

**Label:** feat
**Size:** XS
**Risk:** Medium
**Sizing rationale:** [One line on the human time it needs.]

**Description:**

[2–4 sentences: what happens today, why it's a problem, and what this ticket changes.]

Open: [Only when a question is still unresolved.]

**What to build**

- [Action item in plain language.]
- [Action item with nested rules:]
  - [Rule or option.]

**Acceptance criteria**

- [ ] [Testable outcome.]
- [ ] [Edge case or permission rule.]
- [ ] [What must not change.]

**Agent Guidance:**

- Data: [Tables, columns, constraints, or "no schema change".]
- UI: [Screen, component, or existing pattern to reuse.]
- Tests: [Behavior, edge cases, and permission rules to cover.]
- PRD refs: [e.g. US-1, FR-2]

## Ticket 2: [Action title]

**Label:** feat
**Size:** S
**Risk:** Low
**Sizing rationale:** [One line.]
**Depends on:** Ticket 1

**Description:**

[2–4 sentences.]

**What to build**

- [Action item.]

**Acceptance criteria**

- [ ] [Testable outcome.]

**Agent Guidance:**

- [Layer]: [Guidance.]
- PRD refs: [refs]

---

# [Feature Name] Separate Ticket (outside phases)

## [EXISTING-ID]: [Title of an issue that already exists in the tracker]

**Label:** feat
**Size:** S
**Risk:** Medium
**Sizing rationale:** [One line.]

**Description:** Already in [tracker] ([status], under [parent]). Don't create it again; only add the agent notes below if useful.

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

- [Only things someone might expect to be ticketed: "Not in this plan" items, release checklists such as test plans and pilots, and related existing issues kept separate.]
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

**Description:**

Workflows can only be unpublished today, so old workflows stay in every picker. Archiving needs two fields and a permission before any archiving behavior ships.

**What to build**

- Add when a workflow was archived and who archived it.
- Add an archive permission and give it to admins.

**Acceptance criteria**

- [ ] The migration was generated, read, and applied, and generated types were updated.
- [ ] Existing workflows are not archived.
- [ ] Admins have the new permission; other roles don't.

**Agent Guidance:**

- Data: Add `archivedAt` and `archivedBy` to `workflows`; generate the migration with the project's migration tool.
- Permissions: Add and seed `WORKFLOWS_ARCHIVE` for admins.
- Data: No repository or service methods here; each slice adds what it first uses.
- PRD refs: FR-4

## Ticket 2: Allow archiving a workflow

**Label:** feat
**Size:** S
**Risk:** Low
**Sizing rationale:** Cross-layer slice that copies the existing form archive pattern.
**Depends on:** Ticket 1

**Description:**

Staff can't retire a workflow they no longer use, so it keeps appearing when they create cases. Archiving removes it from new cases without affecting cases already using it.

**What to build**

- Add **Archive** to published workflow cards.
- Move archived workflows into an **Archived** group.
- Hide archived workflows from the case-creation picker.
- Leave cases that already use the workflow unchanged.

**Acceptance criteria**

- [ ] **Archive** on a published workflow moves it to the **Archived** group.
- [ ] Archived workflows don't appear when creating a case.
- [ ] Cases already using the workflow keep working.
- [ ] Drafts can't be archived, and staff without `WORKFLOWS_ARCHIVE` are rejected on the server.

**Agent Guidance:**

- Service: Archive in a service method; reject drafts with a clear error.
- Permissions: Require `WORKFLOWS_ARCHIVE` on the server, not only in the UI.
- PRD refs: US-3, FR-4

## Ticket 3: Show the cases assigned to a workflow

**Label:** feat
**Size:** XS
**Risk:** Low
**Sizing rationale:** Read-only side sheet copying the existing sheet and list patterns.
**Depends on:** Ticket 1

**Description:**

Staff can't see which cases use a workflow before changing it. A side sheet lists them and lets staff remove a case from the workflow.

**What to build**

- Open a side sheet from the workflow card and the workflow page, listing the cases that use it.
- Let staff remove a case from the workflow.
- Show an empty state with one clear action.

**Acceptance criteria**

- [ ] The sheet lists every case using the workflow, and only cases in the user's organization.
- [ ] Removing a case takes it off the list and off the workflow.
- [ ] A workflow with no cases shows the empty state.

**Agent Guidance:**

- API: Add a paginated read for cases by workflow.
- UI: Reuse the existing side-sheet component.
- PRD refs: US-5

# Suggested Child Ticket Order

| Phase | Suggested order |
| --- | --- |
| Phase 1 | Ticket 1, then Tickets 2 and 3 |
```
