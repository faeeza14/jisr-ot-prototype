# Jisr · Shifts & Scheduling — Plan OT & Submissions

A prototype of a shift-scheduling manager screen with three core flows:

- **Scheduler** — a weekly employee × 7-day roster grid
- **Plan OT** — an entry-builder that feeds a live "draft" rail, then submits OT requests for approval
- **Submissions** — an approvals inbox (OT requests table + expandable shift-plan cards) with approve/reject and live stats

## Status

This repo currently holds a **complete, self-contained HTML prototype** (`index.html`) plus the
planning docs for rebuilding it with the real Jisr design system.

| Deliverable | State |
|---|---|
| Working HTML prototype (`index.html`) | ✅ Done — runnable, no build step |
| Plan / manifest (`plan.md`) | ✅ Done |
| Engineer build spec (`build-spec.md`) | ✅ Done |
| Decisions & blockers (`CLAUDE.md`) | ✅ Done |
| Rebuild with real `@jisr-hr/ds-web` components | ⛔ Blocked — see below |

## How to run

The prototype is a single self-contained file (only external dependency is the Onest Google Font):

```bash
open index.html          # macOS
# or serve it:
python3 -m http.server 8000   # then visit http://localhost:8000
```

All screens, mock data, and interactions (mode toggles, draft rail, validation,
approve/reject, live stats, toasts) run inline — no install, no build.

## Blocker: real design-system build

The intended next step is to rebuild `index.html` with **real** `@jisr-hr/ds-web` components
(tokens only, no invented styles). This is currently **blocked on access**:

- `jisr-hr/jisr-frontend-monorepo` → 404 (the current GitHub account is not in the `jisr-hr` org)
- `@jisr-hr/ds-web` → not on the public npm registry

Unblocking requires **`jisr-hr` org membership + a token/SSO with org access**. Once that lands,
follow `build-spec.md` (§0 repo reads first) to fill the verification table and build screen by screen.

## Files

- `index.html` — the working prototype (source of truth for the flow)
- `plan.md` — prototyping plan: flow overview, screen-by-screen states, component manifest, build order
- `build-spec.md` — engineer-facing build spec mapping each element to a DS component + token intents
- `CLAUDE.md` — locked decisions, blockers, and workflow rules
