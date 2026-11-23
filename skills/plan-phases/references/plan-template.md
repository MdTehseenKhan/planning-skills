# Plan Template

Fill this exactly when writing `plan.md`. Delete optional parts that would be empty.

## Template

```markdown
# Plan: [Feature or Refactor Name]

**PRD:** [link to the PRD]

## Goal

[1–3 sentences: what we're building or refactoring and what will be true when it's done.]

## Phase 1: [Short name]

**Done when:** [What's true for users once this phase ships.]

- [Optional: Set up the [feature] data model. Tables, enums, access policies, and permissions from data-model.md. Phase 1 only, or first in each phase when the data model is split by phase.]

- [Behavior or change, end to end. This becomes one ticket.]
  - [Detail the ticket needs]
  - [Detail the ticket needs]

- [Behavior or change.]

- [Behavior or change.]
  - [Edge case and what should happen]

## Phase 2: [Short name]

**Done when:** [What's true for users once this phase ships.]

**Depends on:** [Optional, e.g. "Phase 1 (workspace picker)". Delete if not needed.]

- [Behavior or change.]

- [Behavior or change. (needs: [item from an earlier phase])]
  - [Detail]

## Phase 3: [Short name]

**Done when:** [What's true for users once this phase ships.]

- [Behavior or change.]

- [Behavior or change.]

## Test plan

[When it runs, e.g. "Runs once Phase 3 is built, and must pass before the pilot."]

- [Load, acceptance, or pilot check.]
  - [What it measures or proves]

## Parallel work (outside phases)

- [Item that can start any time, with its ticket ID if one exists.]

## Open questions

1. [Question] — [Owner]

## Not in this plan

- [Deferred or excluded item]
```

## Example Phase

The level of detail to aim for:

```markdown
## Phase 3: Workflow archiving and revising

**Done when:** Staff can archive, revise, and browse past versions of a workflow without affecting cases already using it.

- Allow archiving a workflow.
  - Show an Archive button on published workflow cards.
  - Archived workflows move out of the main list into an Archived group.
  - Archived workflows can't be picked when creating new cases.
  - Cases already using the workflow are not affected.

- Replace Unpublish with Edit and Revise on workflow cards.
  - Draft workflows not used by active cases: Edit, Archive.
  - Published workflows: Revise, Archive. Revise duplicates the workflow as a new version and archives the original.
  - Draft workflows: Publish.

- Show the cases a workflow is assigned to in a side sheet, opened from the workflow card and workflow page, with a way to remove a case. (needs: archiving)
```
