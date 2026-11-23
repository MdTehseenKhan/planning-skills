# Sizing Guide

Agents write the code, and they write it fast. Size by the **human time** a
ticket needs: specifying it, reviewing the diff, verifying the behavior, and
waiting on things outside the code (deploys, providers, evaluations, data
backfills). Never size by code volume.

## Label

`feat` (new behavior) · `fix` (bug) · `refactor` (state the benefit) ·
`spike` (findings + recommended approach + risks + breakdown, no shipped code)

## Size

| Size | Target | Meaning |
|------|--------|---------|
| XS | ≤ 2h | One agent session and a quick review: a migration copied from the data model, a field, a toggle, docs, or a slice that copies an existing pattern |
| S | ~0.5d | Contained slice with a few rules to verify: one endpoint and its UI, a config or infra change, a small new pattern |
| M | ~1d | Cross-layer slice with real verification: a new pattern, background jobs, an external provider, an evaluation, or many rules and states to test |
| L | ~2d | Rare. High-risk core change with heavy verification that can't be split without breaking it. Name it as a split candidate in the report |
| XL | — | Too big. Split before writing the ticket |

## Phase (parent) size

Roll a parent up from its children: XS = 0.25 day, S = 0.5 day, M = 1 day,
L = 2 days. Then pick:

| Total | Parent size |
|-------|-------------|
| ≤ 1 day | S |
| ≤ 2 days | M |
| ≤ 4 days | L |
| > 4 days | Split the phase |

The parent's sizing rationale lists its children's sizes and the total, e.g.
"Rolled up from its tickets (XS, S, XS): about 1 day of review and
verification."

## Deciding the size (in order)

1. **Default one size smaller than instinct.** What used to be an M is
   usually an S now, and what used to be an L is usually an M.
2. **Ambiguity:** unclear requirements → a spike first, or higher risk, not
   a bigger size.
3. **Verification surface:** many states, rules, or flows to check by hand
   → bump up one size.
4. **Outside waiting:** external providers, infra cutovers, evaluations,
   data backfills on production volume → bump up one size.
5. **Pattern fit:** copies an existing pattern → size down.
6. **Simple implementation stays small, whatever the risk.** Config
   changes, route moves, schema copied from the data model, reason codes and
   copy, docs, a new state on an existing component, and slices that reuse
   an existing service path end to end are XS. Mark their risk instead.

Most tickets should land at XS–S. M is only for slices with a long
verification or outside wait (an evaluation, a high-blast-radius write path).
L should almost never appear on a child ticket.

## Risk

Risk is separate from size. A two-hour ticket can be High risk.

- **Low:** clear, isolated, easy to validate and roll back.
- **Medium:** some ambiguity, spans layers, moderate regression risk.
- **High:** incomplete requirements, large blast radius, fragile area, hard
  rollback. Consider a split or a spike first.

## Sizing rationale

One line naming the main drivers, e.g. "Copies the existing archive
pattern across layers" (XS/Low), "New external provider with retries and
compliance limits" (M/High), or "Schema foundation kept together to avoid
migration conflicts" (XS/Medium).

## Distribution

Use the full range. If most tickets are M or L, the sizing is wrong. Keep a
ticket whole only when splitting it would create shared migration or
dependency pain.
