# Jisr Prototype — Shifts & Scheduling: Plan OT & Submissions

Prototype rebuilding `~/Downloads/jisr-plan-ot-submissions.html` with **real Jisr DS components** via the `ds-prototyping` workflow.

## Spec source
- The HTML mock is the source of truth (no Figma flow file). Figma MCP token was expired at plan time.
- Mock: `/Users/faeeza/Downloads/jisr-plan-ot-submissions.html`
- Full plan: `./plan.md`

## Resources
- **DS package:** `@jisr-hr/ds-web` — `import { X } from '@jisr-hr/ds-web'` (Vite alias → `packages/ds-web/src`)
- **Repo:** `jisr-hr/jisr-frontend-monorepo` (private, read via GitHub MCP)
- **Token file:** `packages/ds-foundation/dist/web-css/jisr/jisr-main.css`
- **Storybook base:** `https://main--6821f275192eacaff04edf1e.chromatic.com` (append `?path=/docs/<level>-<name>--docs`)
- **Figma DS file:** `dENVT3cpolQRwvDxU35Jvs` (Wasl DS)

## Locked decisions
- **Scheduler grid** → DS `Table` (`organisms-table`) with custom cell renderers for shift/day-off cells. Not a custom grid.
- **Segmented controls** (Single/Range mode, Paid OT/TOIL type, status filter) → `Tabs` (`molecules-tabs`), styled as segments.
- **Employee multi-select** → UNDECIDED. Check repo for a real Combobox/Select/MultiSelect first; fall back to `Input + Dropdown + Tag` composition only with designer OK.

## Blockers (must clear before Phase 2/3)
- **GitHub access:** authenticated as `faeeza14`, which is NOT in the `jisr-hr` org → repo reads 404. Blocks verification of props/tokens and the build. Needs org membership + `repo`-scoped PAT.
- **Figma token expired (403):** reconnect Figma MCP if DS-file reads become needed.
- **No local monorepo checkout** → `@jisr-hr/ds-web` imports can't resolve locally until repo is available.

## Workflow rules (from ds-prototyping skill)
- Plan → verify → build, in that order. Verification table must be fully filled before any code.
- Tokens only (from `jisr-main.css`) — no hex, no hardcoded px, no invented tokens.
- Read each component's `/docs/` page + Figma variant props before use; don't write components from memory.
- One screen at a time; screenshot + designer approval before the next.
