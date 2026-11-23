# Modeling Guide

Conventions to discover and design checks to apply when proposing a data
model. The codebase wins over this guide: find each convention in the
existing schema first, and use the defaults below only where the project has
none.

## Codebase Conventions

Find how the project handles each area before drafting. Look at the schema
files of whatever ORM or migration tool it uses (Drizzle, Prisma, TypeORM,
SQLAlchemy, Django, Rails, Ecto, plain SQL, ...), and match what nearby tables
do.

| Area | What to find | Default if the project has none |
| --- | --- | --- |
| Location | Where schema files, enums, relations, and access policies live. | One schema file per domain. |
| Naming | Table and column casing, singular or plural tables, constraint naming. | snake_case plural tables; columns in the ORM's casing; descriptive constraint names (`<table>_<purpose>_unique`). |
| Primary key | Key type and how it's generated. | `id uuid`, time-ordered (UUIDv7) when available. |
| Tenancy | The ownership key on tenant-owned tables (e.g. `organizationId`, `workspaceId`, `accountId`). | Every tenant-owned table has a NOT NULL tenant FK (cascade). Repeat the owner key on child rows when it helps access checks and filtering. |
| Audit | Created/updated timestamps and actor columns. | `createdAt`, `updatedAt` (auto-update), `createdBy`, `updatedBy` → `users.id` (set null). |
| Soft delete | Whether rows are soft-deleted, and how. | `deletedAt` (+ `deletedBy`) only when rows must stay recoverable or referenced. Partial unique indexes then exclude deleted rows. |
| Ordering | How user-ordered lists are stored. | `sortOrder` integer, default 0, indexed with the parent id. |
| Timestamps & dates | Moment vs calendar-date types. | Timezone-aware timestamps for moments; `date` for calendar dates. |
| Value sets | DB enums, check constraints, or lookup tables. | DB enum for stable sets; varchar + check constraint for states that change often; a lookup table only when tenants configure values. |
| JSON columns | Whether JSON/JSONB is used and how it's typed. | Only for config, snapshots, metadata, or provider payloads — never for values you filter, join, or aggregate on in hot paths. Typed with a shared schema. Nest at most one level (`total.inputTokens`, not `total.tokens.input`). |
| Types | How app types relate to the schema. | Derive row types from the schema; don't hand-write them. |
| Access control | Row-level security policies, or authorization in the app layer. | Every table has a stated access rule. Child tables inherit through their parent. Reuse existing policy helpers. |
| Migrations | How migrations are generated and run. | Use the project's migration tool; don't hand-edit generated SQL. Backfills move old rows to the new shape; no runtime fallbacks for old data after backfill. |
| Generic engines | Shared, config-driven tables (forms, workflows, custom fields). | Never add columns specific to one instance. Per-instance behavior is config. |

## Design Checks

Run each proposed table through these before writing it down.

**Fit and reuse**
- Can an existing table hold this with a column or two? Search the schema before adding a table.
- Does the name match the domain vocabulary already used in the schema, API, and UI?
- Does it belong to an existing domain file, or is it a new domain?

**Shape**
- What does one row represent? Write it as one sentence. If you can't, the table is doing two jobs.
- Is every one-to-many real? Prefer one row per component over a parent + component child table when the parent adds nothing.
- Many-to-many needs a join table with a unique pair and indexes on both sides.
- Values that repeat per row and are queried belong in columns or child rows, not JSON arrays.
- Prefer direct columns + enums over lookup tables until tenants need configurable values. Note the future path in Decisions if configurability is likely.

**Integrity**
- Ownership drift: when a child stores the tenant/owner key and a parent id, enforce it with a composite FK `(parentId, tenantId) → parent(id, tenantId)` backed by a unique on the parent.
- Pick `onDelete` deliberately: cascade for owned children, set null for optional references and actor columns, restrict for provenance you must not lose.
- Add unique constraints for every business rule like "one per X" (partial unique for "one active per X").
- Add check constraints for cheap invariants: non-negative counts, `endDate >= startDate`, paired nullable columns.
- Business rules that need other tables or app context belong in the service layer, not the DB. Say which layer owns each rule.

**History and lifecycle**
- Does the record change after it is "final" (signed, completed, published)? Then either make it immutable and version it, or snapshot the values that must be reproducible.
- Status columns: list every state and the allowed transitions. One status column, not several booleans that can contradict each other.
- Need to know who changed what? Audit columns cover the last change; an activity/history table covers the full trail.

**Scale and queries**
- List the 3–5 most common reads (lists, filters, rollups, reports) and make sure each has a supporting index, leading with the filter columns (usually the tenant or owner key).
- Rollups that count or sum should read the table where the fact lives (e.g. participant rows, not session rows).
- Long-running or retried work needs idempotency keys and claim/lease or revision columns.
- Large text/payloads (documents, OCR, AI output) stay out of hot list queries: separate table or storage path.

**Privacy**
- Mark columns holding personal data or user-uploaded content as sensitive. Don't put prompts, signed URLs, tokens, or raw provider payloads in metadata columns.
- Every table needs an access rule; note which policy or check covers it.

## Knowledge File

When `.agents/knowledge/data-model.md` is missing and `AGENTS.md` /
`CLAUDE.md` don't cover these conventions, record what you found in the
schema so later runs start from it (see `project-knowledge.md`). Fill every
area from the Codebase Conventions table with the project's actual answer,
with a file path or example where it helps. Write `TBD` where the schema
shows no pattern.

````markdown
# Data Model Conventions

> Recorded by design-data-model from the schema. Edit freely; the code wins
> when they disagree.

| Area | Convention |
| --- | --- |
| Location | [e.g. `src/db/schema/<domain>.ts`; enums in `enums.ts`; policies in `src/db/policies/`] |
| Naming | [table casing and plurality, column casing, constraint naming, with an example] |
| Primary key | [type and generator] |
| Tenancy | [tenant key, its FK and onDelete; owner keys repeated on child rows] |
| Audit | [audit columns and their FKs] |
| Soft delete | [columns, and how unique indexes exclude deleted rows] |
| Ordering | [column and index] |
| Timestamps & dates | [types used for moments and calendar dates] |
| Value sets | [DB enums, check constraints, or lookup tables, and where they live] |
| JSON columns | [how they're typed and validated; what they may hold] |
| Types | [how app types are derived from the schema] |
| Access control | [RLS policy helpers by name, or the app-layer authorization pattern] |
| Migrations | [generate and run commands; rules for generated SQL and backfills] |
| Generic engines | [config-driven subsystems and their boundaries] |

## Project Rules

- [Rules the schema doesn't show, e.g. "Never add columns specific to one form to the forms engine tables."]

## Further Reading

- [Deeper database docs, rules, or skills in the repo, with paths]
````
