# CLAUDE.md

Guidance for Claude Code (and any other AI assistant) working in this repository.

## Stack & Conventions

This project has two hard constraints that must never be violated:

1. **Single-file project.** The entire project must live in one `index.html`
   file, with all CSS and JavaScript inlined via `<style>` and `<script>`
   tags — never in separate `.css` or `.js` files, and never split across
   additional `.html` pages. Linking external images, and external CSS or
   JavaScript libraries (e.g. via a `<link>` tag or a `<script src="https://...">`
   tag pointing at a CDN), is allowed. This constraint exists so the finished
   project can be copy-pasted as a single file for sharing in class and on
   single-file code platforms (e.g. CodePen, JSFiddle).
2. **Vanilla only, no build step.** Use plain HTML, CSS, and JavaScript only.
   Do not introduce frameworks or libraries that require a build step (React,
   Vue, Svelte, TypeScript, JSX, Sass/Less, bundlers like Webpack/Vite, npm
   dependencies, etc.). The file must run by simply opening it in a browser —
   no compilation, transpilation, or installation step of any kind.

## Tech stack (hard constraints — do not deviate)
- Vanilla HTML, CSS, and JavaScript only. No React, Vue, or any JS framework.
- Tailwind CSS for all styling (via CDN only).
- No backend, no database. Fully static site.
- A toggle for light and dark theme, with the choice remembered
  across visits.

## Design Direction (hard constraint — do not deviate)

"Studio Light": clean, academic/editorial. Applies to the whole portal,
every phase, both themes. Express these tokens as Tailwind arbitrary-value
utilities (e.g. `bg-[oklch(98%_0.01_70)]`) or a small inline
`tailwind.config.theme.extend` color block — never hand-rolled CSS files
(that would violate the single-file/no-build constraints above).

- **Palette — light** (oklch):
  - Page background: `oklch(98% 0.01 70)`
  - Card surface: `oklch(100% 0.003 70)`
  - Ghost/placeholder card surface: `oklch(98% 0.006 70)`
  - Hairline border: `oklch(90% 0.01 70)`; dashed ghost-card border:
    `oklch(85% 0.01 70)`
  - Text primary: `oklch(22% 0.02 250)`
  - Text secondary/muted: `oklch(45% 0.02 250)`; footer/caption text:
    `oklch(55% 0.02 70)`
  - Accent (single, teal, hue 200): `oklch(45% 0.09 200)` — used for
    links, icons, active toggle state, eyebrow labels. Tinted
    backgrounds: 10% opacity for icon chips, 6% for decorative blobs.
  - Card shadow: soft, e.g. `0 2px 10px oklch(0% 0 0 / 0.04)`.
- **Palette — dark** (derived to match the required theme toggle; not
  lifted from a mockup — treat as a starting point, adjust if it doesn't
  look right once built):
  - Page background: `oklch(20% 0.015 250)`; card surface:
    `oklch(24% 0.02 250)`
  - Hairline border: `oklch(32% 0.02 250)`
  - Text primary: `oklch(95% 0.01 250)`; text secondary/muted:
    `oklch(70% 0.02 250)`
  - Accent: same hue, lightened for contrast: `oklch(75% 0.09 200)`;
    tinted backgrounds at 14% opacity.
- **Validation/error state** (added when the first form-based tool needed
  it — reuse for every future tool's input validation):
  - Danger (light): `oklch(55% 0.18 25)`; danger (dark): `oklch(72% 0.18 25)`.
  - Used for invalid-input borders and error message text. Same
    palette-construction method as the accent (single hue, oklch).
- **Typography**:
  - Display/headings: Newsreader (serif), weight 600. Sizes: site title
    22px, page/category heading 38px, card title 19px, ghost-card title
    16.5px.
  - Body/UI: Public Sans, weights 400/500/600. Sizes: body copy
    14–15.5px, eyebrow label 12px (uppercase, letter-spacing 0.12em,
    weight 600, accent color), caption/footer 12–13px.
  - Google Fonts: `https://fonts.googleapis.com/css2?family=Newsreader:ital,wght@0,400;0,500;0,600;1,400&family=Public+Sans:wght@400;500;600&display=swap`
  - Fallback stacks: `'Newsreader', serif` / `'Public Sans', system-ui, sans-serif`.
- **Spacing/layout**:
  - Page horizontal padding: 60px (desktop).
  - Header vertical padding: 30px; content top padding: 48px; grid
    section padding: 38px 60px.
  - Card padding: 30px; grid gap: 26px.
  - Radii: card 14px, icon chip 12px, pill toggle 999px (fully round).
  - Icon chip size: 46×46px.
  - Borders: 1px solid hairline (real content); 1.5px dashed (ghost
    cards/placeholders).
- **Reusable component patterns** (apply automatically to new tools too):
  - Eyebrow label: uppercase, tracked, accent-colored (e.g. "PHASE 1 · 2
    of a growing set").
  - Icon chip: rounded-12px square, tinted accent background (10%
    light / 14% dark), accent-colored line icon (24px viewBox, ~1.75
    stroke-width, no emoji).
  - Ghost/"coming soon" card: dashed border, muted icon circle, italic
    caption — the standard pattern for any future placeholder/empty
    state.
  - Theme toggle: pill-shaped, two icon buttons (sun/moon), active
    state gets a tinted accent background.

## Working conventions
- Before implementing any non-trivial feature, ask clarifying
  questions about scope, edge cases, and constraints first —
  don't propose a plan until you've asked.

## Feature Plan

Living roadmap for the portal, broken into phases. Keep each phase a tight
bullet list, not prose. When a phase is finished, don't delete it — collapse
it to a one-line summary (e.g. "Phase 1 — Force & Moment: shipped. See git
history for details.") so the section stays skimmable as it grows.

### Portal shell (status: done — applies to every phase)
- Built in `index.html`. **Four view layers, one visible at a time**:
  `#home-view` → `#section-view-<id>` → `#topic-view-<id>` /
  `#tool-view-<id>`. Home shows the 3 section cards (Concepts and Theory,
  Tools, Virtual Experiments); a section's own back button returns Home; a
  topic's or tool's back button returns to its section. Generic
  `showHome()`/`showSection(id)`/`showTopic(id)`/`showTool(id)` all route
  through one `hideAllViews()` (hides `#home-view` + every
  `[data-section-view]` + every `[data-topic-view]` + every
  `[data-tool-view]`, then un-hides the target). Verified working
  (Playwright click-through: Home → Tools → Force Calculator → back → Tools
  → back → Home; Home → Concepts and Theory → Resolve and Resultant Force
  → back → back → Home; the empty Virtual Experiments section → back →
  Home).
- Dark/light toggle persisted in `localStorage`, defaulting to OS
  preference until the user explicitly chooses. Verified working.
- **Data model**: `SECTIONS_DATA = [{ id, name, description }]` (+
  `SECTION_ICONS`) drives the homepage's 3 section cards via
  `renderSectionsGrid()`. Inside a section, a `[{ category, <items>: [{ id,
  name, description }] }]`-shaped array (+ an icon map) drives that
  section's own grid via one shared `renderItemGrid(containerId, data,
  groupKey, itemsKey, iconMap, datasetKey, ghostTitle)` — `TOOLS_DATA`/
  `TOOL_ICONS` → `renderToolsGrid()` → `#tools-grid-container` (dataset key
  `toolId`) and `CONCEPTS_DATA`/`CONCEPT_ICONS` → `renderConceptsGrid()` →
  `#concepts-grid-container` (dataset key `topicId`) are both thin wrappers
  around it. Each tool/topic's markup lives in its own `#tool-view-<id>` /
  `#topic-view-<id>` section. Adding a future tool/topic = one data entry +
  one section + its own content/logic; adding a future section = one
  `SECTIONS_DATA` entry + one `#section-view-<id>` — navigation/theme code
  untouched either way.
- **Key flows**: one delegated click listener, checked in this order so a
  button's `data-action` always wins over a bare card attribute:
  `[data-action="show-home"]` → `showHome()`; `[data-action="show-section"]`
  (reads `data-section-id`) → `showSection(...)` (used by topic/tool back
  buttons, and by inline cross-links in topic content); `[data-action=
  "remove-row"]` (unchanged); `[data-tool-id]` → `showTool(...)`;
  `[data-topic-id]` → `showTopic(...)`; bare `[data-section-id]` (home's
  section cards) → `showSection(...)`. The shared dynamic add/remove-row
  pattern
  (`<template>`-based, min 1 row, default 2 rows, `addRow`/`removeRow`/
  `updateRemoveButtons` + a `rows-changed` event for listeners) is built
  and used by both calculators, unaffected by the sections change.

### Phase 1 — Force & Moment (status: done)
- **Force Calculator** (done): dynamic list of {magnitude, angle} force rows
  (`<template>`-based, min 1 row, default 2, shared `addRow`/`removeRow`
  helpers) → sum to components → resultant magnitude + angle (normalized
  to [0°, 360°)), components shown too. Explicit "Calculate" button
  trigger (not live-as-you-type). Balanced forces (magnitude ≈ 0) show
  "direction undefined" instead of a bogus angle. Verified with a
  headless-browser click-through (row add/remove floor, a known 3-4-5
  case, the balanced case, and the invalid-row block/highlight/clear
  cycle).
- **Force Calculator diagrams** (done): two inline-SVG vector diagrams
  (axes + light grid + per-vector magnitude/angle labels) — "Individual
  Forces" plots every valid row as its own arrow from the origin;
  "Resultant Force" plots just the resultant with dashed Rx/Ry projection
  lines. Both update live on every keystroke/row add/remove, independent
  of the Calculate button and its validation (invalid/blank rows are
  silently skipped in the diagrams rather than blocking them). Each
  diagram auto-scales to its own max magnitude. Reusable pieces for the
  Moment Calculator: `polarToXY`, `readForceRows`-style row parsing, and
  the `rows-changed` custom event a container dispatches after
  `addRow`/`removeRow` so a tool can listen and redraw its own diagrams.
- **Moment Calculator** (done): dynamic list of {magnitude, angle, x, y}
  rows (same shared row-management pattern) → per-row moment
  `x*Fy − y*Fx` → net moment, labeled CCW/CW/"balanced (no net rotation)"
  + a per-force breakdown table (magnitude, angle, position, moment
  contribution) built from the same values used for the sum. Same
  invalid-row block/highlight policy and explicit "Calculate" trigger as
  the Force Calculator.
- **Moment Calculator diagrams** (done): "Applied Forces" plots each valid
  force as an arrow starting at its (x, y) position (a dot marks the
  point, a separate dot marks the pivot at the origin) — position and
  force-arrow length auto-scale independently, since they're different
  units (m vs. N) and aren't meant to be read against the same scale.
  "Net Moment" draws a fixed-radius rotation arc around the pivot whose
  sweep direction (CCW/CW) matches the net moment's sign, with the value
  labeled above it; shows just the pivot dot + a "balanced" caption when
  net ≈ 0. Both live-update the same way as the Force Calculator's
  diagrams. Verified with a headless-browser run: default row count, a
  worked two-force case (moments 20.00 + 6.00 = 26.00 N·m CCW, matching
  the breakdown table), a force applied exactly at the pivot (0
  contribution), and the invalid-row block/highlight cycle.
- **Conventions/assumptions**: angle 0° = +x axis, CCW positive; units N /
  ° / m / N·m; 2-decimal display rounding computed from full-precision
  sums; an invalid/blank row blocks calculation and highlights that row;
  no URL/hash routing.

### Sections (status: done)
- Homepage shows 3 top-level sections instead of items directly:
  **Concepts and Theory** holds topics (see below) and its own back
  button → Home; **Tools** holds the Phase 1 calculators (see above) and
  its own back button → Home; **Virtual Experiments** is still a
  placeholder (ghost/"coming soon" pattern, back button → Home). See
  Portal shell above for the `SECTIONS_DATA`/navigation details.

### Concepts and Theory (status: in progress — 1 topic)
- **Resolve and Resultant Force** (done): a `CONCEPTS_DATA` category
  "Forces" → topic `resolve-and-resultant-force`, rendered at
  `#topic-view-resolve-and-resultant-force`. Static content (text,
  formulas, tables, hand-authored inline-SVG diagrams — no interactive
  inputs; this is theory, not a calculator):
  1. Resolving a force into components (F<sub>x</sub>=F cos θ, F<sub>y</sub>
     =F sin θ), each worked example's diagram marking θ itself as an arc
     (drawn as a precomputed polyline, not an SVG elliptical-arc command —
     see the angle-arc pitfall below) and reading out the numeric F<sub>x</sub>/
     F<sub>y</sub> values directly on the diagram (bottom-corner legend,
     positioned to stay clear of the vector regardless of its direction).
     Two basic examples, same magnitude (F=50 N), different quadrants: 40°
     (first quadrant — arc swept CCW from the positive x-axis) and 130°
     (second quadrant, F<sub>x</sub> negative). **Convention for non-first-
     quadrant angles**: use the *basic angle* only (the acute angle, 0–90°,
     between the force and the *nearest* x-axis — here 180°−130°=50°) for
     both the diagram's arc/label and the worked trig, and read the sign of
     each component off the force's actual direction rather than off the
     trig function — e.g. "50 × cos 50° = 32.14 N, but the force points
     left, so F<sub>x</sub> = −32.14 N". Same magnitude in both examples so
     the sign flip is the obvious takeaway. Then an inclined-body example:
     one force (P=100 N at 20° above a 30° incline surface, "perpendicular"
     bolded in the intro), acting on the right (up-slope-facing) surface of
     a small rotated-rect block drawn resting on top of the incline surface
     in both diagrams — the vector/arc anchor point is that edge's rotated
     midpoint (not the block's center), so P visibly attaches to the block
     rather than passing through it, and every other coordinate in the
     scene (local axes, projections, angle arcs) is computed relative to
     that same anchor. Both diagrams also mark the
     30° incline angle itself as an arc at the ramp's base corner (shared
     between diagrams). The horizontal/vertical diagram splits the total
     50° into its two constituent arcs/labels (30° + 20°, at two radii so
     they read as separate) instead of one combined "50°", making the
     "add the incline's 30° to P's own 20°" point visible rather than
     just stated; the parallel/perpendicular diagram keeps its single 20°
     arc (the angle it actually uses directly) alongside the same 30°
     incline-angle arc for context. Together this makes concrete why
     matching axes to the problem's geometry means less setup (add
     30°+20° first, or just use the 20° you were given). The
     horizontal/vertical diagram also has a dotted line through the
     anchor point running parallel to the incline surface (labeled
     "incline"), so the 30° tilt is visible right where P acts, not only
     down at the ramp's base corner. Both diagrams read out P's numeric
     components directly on the diagram (top-left corner legend: Px/Py in
     the H/V diagram, P<sub>∥</sub>/P<sub>⊥</sub> in the other), matching
     the on-diagram-legend convention from the basic examples above, AND
     draw each component as its own muted solid arrow from the anchor
     point (Fx horizontal + Fy vertical in the H/V diagram; P<sub>∥</sub>
     along the incline + P<sub>⊥</sub> away from it in the other) — not
     just the passive dashed "closing" lines that were already there.
     Component arrows share the same tip coordinates as those dashed
     lines by construction (e.g. the Fx arrow's tip is exactly where the
     vertical dashed drop-line from P's tip lands), so the whole picture
     reads as one consistent component rectangle/parallelogram with P as
     its diagonal. The comparison table splits P<sub>x</sub>/P<sub>∥</sub> and
     P<sub>y</sub>/P<sub>⊥</sub> onto their own rows rather than combining
     both components of a frame into one cell. (An earlier version of
     this example also included a weight force W to contrast "which force
     needs trig in which frame" — removed for simplicity; one force is
     enough to show the axis-choice point, and the block shape now
     carries the "this
     force acts on a block" context instead.)
  2. Finding the resultant of several forces (ΣF<sub>x</sub>, ΣF<sub>y</sub>
     → magnitude/angle via hypot/atan2), worked with 4 forces at 0°/90°/
     200°/300° — one force per quadrant, so every sign combination of
     (F<sub>x</sub>, F<sub>y</sub>) appears — plus an inline cross-link
     (`data-action="show-section" data-section-id="tools"`) inviting the
     reader to reproduce the same numbers in the Force Calculator. The
     resultant is labeled F<sub>R</sub> (not bare "R"), and the formula/
     worked-example text use two small reusable inline-CSS constructs
     instead of plain characters: a full-width square root (a `√` glyph
     beside a span whose top border supplies the bar over the whole
     enclosed expression, so it visibly spans `ΣFx² + ΣFy²` rather than
     just sitting next to parentheses) and a stacked fraction (numerator
     span over a bottom-bordered denominator span) for ΣF<sub>y</sub>⁄
     ΣF<sub>x</sub> inside `tan⁻¹(...)` in place of `atan2(a, b)`. The
     "Resultant Force" SVG diagram label needs the same F<sub>R</sub>
     subscript but can't use HTML `<sub>` inside `<text>` — it uses a
     smaller-font-size `<tspan>` with a `dy` offset instead (no sqrt/
     fraction there, it's just the final numeric readout). F3/F4 (200°/
     300°, both >90°) get their basic angle noted in the table ("200°
     (basic 20°)", "300° (basic 60°)") and marked as an arc + label on the
     "Individual Forces" diagram itself — from the *nearest* x-axis (180°
     for F3, 360°/0° for F4), same precomputed-polyline-arc technique and
     same basic-angle convention as section 1. F1/F2 (0°/90°) don't need
     it, already ≤90°. The "Resultant Force" diagram also marks its own
     angle (8.85°, straight from the positive x-axis — already ≤90°, no
     basic-angle conversion needed) as the same kind of arc; since the
     angle is small the arc is a short tick right at the origin, and the
     "8.85°" label sits below the x-axis rather than beside the arc,
     which would otherwise overlap the resultant vector itself (it
     nearly runs along the x-axis at this angle).
  All diagrams reuse the site's existing visual language (accent vectors,
  dashed projection/"shadow" lines, muted axes) but are hand-coded static
  SVG with precomputed coordinates (no JS rendering — the numbers don't
  change). **Angle-arc pitfall**: an SVG elliptical arc (`A rx,ry ...`)
  between two points on a circle of a *given* radius is ambiguous — two
  different circles of that radius can pass through the same two points,
  and which one the `large-arc-flag`/`sweep-flag` combination selects
  depends on the specific chord/radius ratio, not just the flag values.
  This looked fine at a small sweep (40°) and rendered the wrong way at
  a larger one (130°, chord close to the diameter). Draw angle arcs
  around a *known* center as a precomputed polyline (many small `L`
  segments stepping the angle from 0 to θ via the same math→SVG-point
  formula used for vectors) instead of an SVG arc command. Verified by
  rendering and visually checking every diagram (this angle-arc bug, plus
  one earlier label-overflow bug also fixed here) plus a full navigation
  regression across all sections/topics/tools.

### Phase 2+ — future tools, topics, and sections (not started)
- New tool: append to `TOOLS_DATA` and add a `#tool-view-<id>` section
  with its own logic.
- New Concepts and Theory topic: append to `CONCEPTS_DATA` (existing
  "Forces" category or a new one) and add a `#topic-view-<id>` section.
- New section, or real content for Virtual Experiments: append to
  `SECTIONS_DATA` and add a `#section-view-<id>` with its own content;
  replace the ghost placeholder once there's something to show.
- None of the above touch shared navigation/theme/grid code.
