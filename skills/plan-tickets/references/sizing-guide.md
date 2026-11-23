# Sizing Guide

Size by **delivery friction**, not code volume. Agents do the coding.

## Label

`feat` (new behavior) · `fix` (bug) · `refactor` (state the benefit) ·
`spike` (findings + recommended approach + risks + breakdown, no shipped code)

## Size

| Size | Target | Meaning |
|------|--------|---------|
| XS | 1–4h | Bounded, near-mechanical: one field, a toggle, a small UI tweak |
| S | 0.5d | Small and clear: one endpoint, small CRUD, contained UI + backend change |
| M | 1d | Standard vertical slice across several files |
| L | 2d | Multi-step workflow with permissions, validation, backend, and UI |
| XL | 3–5d | Too big. Split before writing the ticket |

Parent phase tickets are always L.

## Deciding the size (in order)

1. **Ambiguity:** unclear requirements → bigger, higher risk, or a spike first.
2. **Blast radius:** schema change, shared infra, security surface → bump up.
3. **Integration:** cross-layer, jobs, email, PDF, external providers → bump up.
4. **Validation surface:** many flows or procedures to verify → bump up.
5. **Pattern fit:** copies an existing pattern → size down.
6. **Default:** smaller. A traditional M is often S here.

## Risk

- **Low:** clear, isolated, easy to validate and roll back.
- **Medium:** some ambiguity, spans layers, moderate regression risk.
- **High:** incomplete requirements, large blast radius, fragile area, hard
  rollback. Consider a split or a spike first.

## Sizing rationale

One line naming the main drivers, e.g. "Cross-layer slice reusing the
existing archive pattern" (S/Low) or "Schema foundation kept together to
avoid migration dependency pain" (S/High).

## Distribution

Use the full range. If every ticket is M, the sizing is wrong. Keep a
ticket whole only when splitting it would create shared migration or
dependency pain.
