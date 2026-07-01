# Prototyping Plan: Shifts & Scheduling — Plan OT & Submissions

## Flow overview
A shift-scheduling manager screen with four tabs. The prototype focuses on three: **Scheduler**
(a weekly employee × 7-day roster grid), **Plan OT** (an entry-builder card that feeds a live
"draft" rail, then submits OT requests for approval), and **Submissions** (an approvals inbox —
an OT requests table plus expandable shift-plan cards, each with approve/reject and live stats).
Goal: reproduce the flow using only real Jisr DS components.

## Connector & resource status
- Figma MCP: token expired (403) — HTML mock used as spec instead
- GitHub MCP: authenticated as `faeeza14`, no access to `jisr-hr/jisr-frontend-monorepo` (404) — blocks Phase 2/3
- Storybook: index available locally; live pages are JS-rendered (not fetchable as text)
- Spec source: `/Users/faeeza/Downloads/jisr-plan-ot-submissions.html`
- Local monorepo checkout: none

## Screen-by-screen flow
| # | Screen | Purpose | Key states / variants |
|---|---|---|---|
| S1 | Scheduler | Weekly roster grid, employees × 7 days | Shift cell, Day-off cell, empty cell; assign drawer open |
| S2 | Plan OT | Build OT entries → draft rail → submit | Single vs. Range mode; Paid OT vs. TOIL; validation error; empty draft |
| S3 | Submissions | Approve/reject OT + shift plans | Sub-tabs (All/Shift/OT); status filter; pending vs. approved/rejected; empty state; toast |

## Locked decisions
1. Scheduler grid (#8) → DS `Table` with custom cell renderers.
2. Segmented controls (#15, #27) → `Tabs` styled as segments.
3. Employee multi-select (#16) → UNDECIDED; confirm repo Combobox/Select first, else compose Input+Dropdown+Tag.

## Component manifest
Legend: Full = complete DS component · Atomic = composed from atoms (needs OK) · Decision = flagged

| # | Element (from mock) | Screen | Status | DS component / atoms | Notes |
|---|---|---|---|---|---|
| 1 | Left icon rail | all | Full | Sidebar (`molecules-sidebar`, v2) | Slim icon nav |
| 2 | Top bar (org, help, user) | all | Atomic | Avatar + Dropdown + Button | No app-shell topbar component |
| 3 | Page title + tab bar | all | Full | PageHeader + Tabs (`molecules-tabs`) | |
| 4 | Tab count pill | all | Full | Badge (`atoms-badge`) | |
| 5 | Buttons (primary/ghost/icon) | all | Full | Button (`atoms-button`) | |
| 6 | Search field | S1, S3 | Full | Input (`atoms-input`, search) | |
| 7 | Weekly / Shift / filter selects | S1, S2 | Full | Dropdown (`molecules-dropdown`) | |
| 8 | Weekly roster grid | S1 | Decision→Table | Table (`organisms-table`) + custom cells | LOCKED: DS Table |
| 9 | Shift cell (colored block) | S1 | Atomic | Tag or Card + Typography | Time + location |
| 10 | Day-off / empty cell | S1 | Full | Tag / Badge | |
| 11 | Employee avatar | S1–S3 | Full | Avatar (`atoms-avatar`, initials) | |
| 12 | Grid legend footer | S1 | Atomic | Badge + Typography | |
| 13 | Assign drawer | S1 | Full | Drawer (`molecules-drawer`) | |
| 14 | Entry-builder card | S2 | Full | Card (`molecules-card`) | |
| 15 | Single/Range + Paid/TOIL toggles | S2 | Decision→Tabs | Tabs (`molecules-tabs`) | LOCKED: Tabs |
| 16 | Employee multi-select | S2 | Decision | Input + Dropdown + Tag (chips) | UNDECIDED — check repo Combobox |
| 17 | Date pickers | S2 | Full | Calendar in Popover, or Input | |
| 18 | OT hours input | S2 | Full | NumberInput (`molecules-numberinput`) | Decimal |
| 19 | Field labels / errors | S2 | Full | Field (`molecules-field`) + Label | |
| 20 | Reason textarea | S2 | Full | Input (textarea) | |
| 21 | Info/range note | S2 | Full | Banner (`molecules-banner`, info) | |
| 22 | Draft rail | S2 | Full | Card + count Badge | |
| 23 | Draft item rows | S2 | Full | Item (`molecules-item`) + Avatar + Badge | Grouped by day |
| 24 | Paid OT / TOIL badge | S2, S3 | Full | Badge / Tag | |
| 25 | Stat cards (×4) | S3 | Full | Card + Typography | |
| 26 | Sub-tabs + counts | S3 | Full | Tabs + Badge | |
| 27 | Status filter chips | S3 | Decision→Tabs | Tabs | LOCKED: Tabs |
| 28 | OT requests table | S3 | Full | Table (`organisms-table`, with-actions) | |
| 29 | Status badge | S3 | Full | Badge (success/warning/danger) | |
| 30 | Shift-plan expandable cards | S3 | Full | Accordion (`molecules-accordion`) | Avatar + trailing badge slots |
| 31 | Empty state | S3 | Full | Empty (`molecules-empty`) | |
| 32 | Toast notifications | S2, S3 | Full | Toast (`molecules-toast`) | success/warning |

## Phase 2: Verification table (to be filled by Claude Code — BLOCKED on GitHub)
Every row's Package / Import / Storybook-confirmed / Token-path must be filled from the real repo
before any code. Currently blocked: GitHub access to `jisr-frontend-monorepo` returns 404.

## Build order (Phase 3, once unblocked)
1. Shared atoms: Avatar, Badge, Button, Tag, Input
2. Composites: Card, Tabs, Table cells, Item, Accordion, Toast, Drawer
3. Screens: S1 Scheduler → CHECKPOINT · S2 Plan OT → CHECKPOINT · S3 Submissions → CHECKPOINT

## Open questions
- Employee multi-select component (decision #3) — resolve after GitHub read.
- GitHub org access for `faeeza14` — hard blocker for Phase 2/3.
