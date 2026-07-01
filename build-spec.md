# Build Spec — Shifts & Scheduling: Plan OT & Submissions

Engineer-facing build spec for rebuilding `~/Downloads/jisr-plan-ot-submissions.html` with real
`@jisr-hr/ds-web` components. Pairs with `plan.md` (manifest) and `CLAUDE.md` (decisions/blockers).

`⚠ confirm in repo` = value I could not verify without repo access. Resolve before/while building.

---

## 0. Do these repo reads FIRST (they unblock every `⚠` below)

1. `packages/ds-web/package.json` → confirm package name is `@jisr-hr/ds-web` and export surface.
2. `packages/ds-foundation/dist/web-css/jisr/jisr-main.css` → copy exact CSS var names for the
   color / spacing / radius intents listed in §1. **These replace every `⚠ token` marker below.**
3. For each component, open its `?path=/docs/<...>--docs` page (URLs in `plan.md`) OR read
   `packages/ds-web/src/components/<Name>/<Name>.stories.tsx` `title:` + props. Confirm prop names.
4. **Decision #3:** grep the repo for a `Combobox` / `Select` / `MultiSelect` / `Autocomplete`
   component. If one exists, use it for the employee picker (§S2, #16). If not, compose
   `Input + Dropdown + Tag` — but confirm with designer before composing.

---

## 1. Token intents → map to real `jisr-main.css` vars

Typography (CONFIRMED from skill resources — use as-is):
| Use | Token class |
|---|---|
| Display stat numbers (28–32px) | `typography-headline-medium` |
| Screen titles (`h1`, 20–22px) | `typography-title-large` |
| Card titles (15–16px) | `typography-title-medium` |
| Section labels / employee names | `typography-title-small` |
| Body text | `typography-body-medium` |
| Table cells, captions, meta | `typography-body-small` |
| Bold labels (badge/field labels) | `typography-label-small` / `-medium` |

Color / spacing / radius (INTENT — `⚠ confirm names in jisr-main.css`):
| Intent in mock | Value in mock | Map to |
|---|---|---|
| App background | `#f6f6f7` | `⚠ --color-background-*` (page/muted surface) |
| Card / surface | `#ffffff` | `⚠ --color-background-default` |
| Hairline border | `#ececec` / `#e3e3e6` | `⚠ --color-border-*` |
| Primary ink | `#101010` | `⚠ --color-text-primary` |
| Muted text | `#8a8a90` | `⚠ --color-text-secondary/tertiary` |
| Status: pending | amber `#f59e0b` | `⚠ --color-*-warning` |
| Status: approved | green `#16a34a` | `⚠ --color-*-success` |
| Status: rejected | red `#e5484d` | `⚠ --color-*-danger` |
| Paid OT badge | blue `#4783FC` | `⚠ --color-*-info/brand` |
| TOIL badge | lavender `#7c5cc4` | `⚠ --color-*-accent` (may be missing — flag) |
| Radius (cards/inputs) | 9–12px | `⚠ --radius-*` |
| Spacing scale | 6/8/12/16/22px | `⚠ --spacing-*` |

**Open design question — shift-cell palette:** the roster uses decorative shift colors
(peach `#fdead9`, pink `#fbdbe1`, yellow `#fdf1cd`, lavender `#ece3f8`). These likely have **no DS
token**. Decide: (a) add shift-type tokens, or (b) map to nearest DS accent surfaces. Needs designer.

---

## 2. Global setup (all screens)

- **Font:** load `Onest`; set `body, #root { font-family: var(--typography-font-family, 'Onest', sans-serif) }`.
- **App shell:** `Sidebar` (`molecules-sidebar`, v2 story) for the left icon rail. Active item = Scheduling.
- **Top bar** (#2, Atomic): compose `Avatar` (user) + `Dropdown` (org switcher, user menu) + `Button`
  (ghost, help). No DS app-shell topbar — keep composition minimal, tokens only.
- **Tabs:** `Tabs` (`molecules-tabs`) for Scheduler / Plan OT / Submissions / Shift Settings.
  Submissions tab shows a live count `Badge`.
- **Toasts:** mount `Toast` provider once (`molecules-toast`); `appearance="success"` for confirmations,
  `"warning"` for rejects. ⚠ confirm the toast API shape (imperative `toast()` vs component).
- **Mock data:** port verbatim from the HTML `<script>` — `EMPLOYEES`, `SCHED`, `OT`, `SHIFTPLANS`,
  plus helpers `fmtH`, `iniOf`, `daysBetween`. Keep them in a `mock.ts`.

---

## S1 — Scheduler

Frame intent: week label + actions row, controls row, then the roster grid, then a legend footer.

| Element | Component | Variant intent | Notes |
|---|---|---|---|
| Week label "Jun 28 — Jul 04" | `Typography` | title-small/medium | Static in prototype |
| Prev / calendar / next | `Button` | `appearance` icon/tertiary, `size=s` ⚠ | Icon-only |
| Upload / Assign / Publish | `Button` | ghost, ghost, **primary** | Assign opens Drawer |
| Weekly select | `Dropdown` | default | "Weekly" |
| Search employees | `Input` | search variant | |
| Filters | `Dropdown` or `Button` | | |
| **Roster grid** | `Table` (`organisms-table`) | fixed layout, sticky header ⚠ | See below |
| — Employee cell | `Avatar` (initials) + `Typography` | avatar `size` ⚠ | name / role / hours |
| — Shift cell | custom cell → `Tag` or `Card` | shift-type color (see §1 open q) | time + location |
| — Day-off cell | `Tag` / `Badge` | muted/neutral | "Day Off" |
| — Empty cell | empty custom cell | | |
| Assign drawer | `Drawer` (`molecules-drawer`) | right side, `with-form` shape ⚠ | header + body + footer buttons |
| Legend footer | `Badge` + `Typography` | | Unpublished / Unavailable / Unscheduled counts |

Grid build: use `Table` with columns = `[Employee, Sun..Sat]`; each day column uses a **custom cell
renderer** returning the shift/day-off/empty block. Data = `SCHED` (each row has `cells[7]`). Do NOT
build a bare `<div>` grid — decision #1 locked to DS `Table`.

**CHECKPOINT after S1.**

---

## S2 — Plan OT

Two-column layout: entry-builder `Card` (left) + draft rail `Card` (right, sticky).

| Element | Component | Variant intent | Notes |
|---|---|---|---|
| Page header + Import Excel | `PageHeader` + `Button` (ghost) | | title "Plan Overtime" + subtitle |
| Entry card | `Card` (`molecules-card`) | static | |
| Single / Date range toggle | `Tabs` | segmented (decision #2) | switches field set |
| **Employee multi-select + dept bulk-add** | ⚠ decision #3 | | Combobox if exists, else Input+Dropdown+Tag. Dropdown has a **Departments** section: selecting a dept adds all its members as chips (individual select kept) |
| — selected chips | `Tag` (`with-avatar` + `with-close-button`) | | avatar-colored chips (dept-added members appear as normal removable chips) |
| Date / From / To | `Calendar` in `Popover`, or `Input` | | mock uses native date inputs |
| Shift select | `Dropdown` | | Morning/Night/Evening/Flexible |
| OT hours | `NumberInput` (`molecules-numberinput`) | decimal, min 0 | validation: > 0 |
| OT type Paid/TOIL | `Tabs` | segmented (decision #2) | |
| Reason / notes | `Input` (textarea) or `Field` | optional | |
| Range note | `Banner` (`molecules-banner`) | `appearance=info`, mid-emphasis | live preview text |
| Validation error | `Field` error / `Banner` danger | | ported from `showErr()` |
| Add to draft | `Button` | primary, full-width | |
| Draft rail | `Card` + count `Badge` | | **collapsible summary grouped by department** — one `Accordion`/collapsible row per dept |
| — dept header | collapsible header + `Badge`/meta | | `<Dept> · N emp · total hrs`; chevron toggles; collapsed by default; expand state persists |
| Draft item | `Item` (`molecules-item`) + `Avatar` + `Badge` | | inside expanded dept: date · shift / hours / Paid|TOIL badge / remove |
| Paid OT / TOIL badge | `Badge` | info / accent (⚠ TOIL color) | |
| Totals + Submit | `Typography` + `Button` (primary) | | "Submit all · <total>h" |

Behavior to port (from HTML JS): mode toggle shows/hides field sets; `daysBetween` range preview;
`add-draft` validation (employees ≥1, date, shift, hours > 0); range mode = one entry per employee
per day; **draft grouped by department** (collapsible; header shows distinct-employee count +
summed hours; expanded rows sorted show date · shift); dept bulk-add pushes all a department's
members into the selection; `Submit` pushes each draft item into `OT` as `status:'pending'` and
re-renders Submissions; toasts on add/submit.

**CHECKPOINT after S2.**

---

## S3 — Submissions

Stats row → sub-tabs → search + filter → OT table + shift-plan accordions.

| Element | Component | Variant intent | Notes |
|---|---|---|---|
| Page header + Export CSV | `PageHeader` + `Button` (ghost) | | |
| Stat cards ×4 | `Card` + `Typography` | headline-medium numbers | Pending/Approved/Rejected/Total OT hrs |
| Sub-tabs All/Shift/OT | `Tabs` + `Badge` counts | | switches panels |
| Search | `Input` | search | filters by employee/shift/type |
| Status filter | `Tabs` | segmented (decision #2) | All/Pending/Approved/Rejected |
| **OT table** | `Table` (`organisms-table`, `with-actions`) | | columns per mock |
| — employee cell | `Avatar` + `Typography` | | name + dept |
| — type | `Badge` | info (Paid) / accent (TOIL) | |
| — status | `Badge` | success / warning / danger | pending/approved/rejected |
| — actions | `Button` (tertiary/link) | Approve (success), Reject (danger), Details | pending → Approve/Reject; else Details |
| **Shift-plan cards** | `Accordion` (`molecules-accordion`) | leading `Avatar`/icon + trailing `Badge` | expands to assignment lines |
| — expanded lines | `Item` or `Typography` rows | | who / when |
| Empty state | `Empty` (`molecules-empty`) | with-icon | when a filter yields nothing |
| Toast | `Toast` | success on approve, warning on reject | |

Behavior to port: live stats recompute from `OT` + `SHIFTPLANS` on every action; sub-tab switches
panel (All = OT table + shift accordions, OT = table only, Shift = accordions only); status filter +
search both narrow results; Approve/Reject mutate item status and re-render; "Approve all" on a shift
plan sets it approved; empty states toggle when a list is empty.

**CHECKPOINT after S3.**

---

## Handoff checklist for the build environment
- [ ] `@jisr-hr/ds-web` import + Vite alias resolves
- [ ] `jisr-main.css` imported globally; all `⚠ token` intents mapped to real vars
- [ ] Onest font loads globally
- [ ] Decision #3 (employee picker) resolved against repo
- [ ] Shift-cell palette question resolved with designer
- [ ] Each component's props confirmed from its `/docs/` page before use
- [ ] One screen at a time; screenshot + designer approval per checkpoint
