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
  untouched either way. A group's `category` is optional: `renderItemGrid`
  only renders the uppercase category heading when it's truthy, so a
  grid can group its items under headings (Tools' "Force & Moment") or
  leave `category: ""` to render everything flat with no heading
  (Concepts and Theory's topics all sit directly under the section, with
  no "Forces"/"Moments"-style grouping).
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

### Concepts and Theory (status: in progress — 3 topics, no categories)
- **Resolve and Resultant Force** (done): a `CONCEPTS_DATA` topic
  `resolve-and-resultant-force` (flat, no category grouping — see Portal
  shell above), rendered at `#topic-view-resolve-and-resultant-force`. Static content (text,
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
- **Moment** (done): a `CONCEPTS_DATA` topic `moment` (flat, no category —
  appended to the same topics array as Resolve and Resultant Force, per
  the "remove the categories" change below), rendered at
  `#topic-view-moment` (sibling of the Resolve topic inside
  `#topic-views-container`), `CONCEPT_ICONS['moment']` reuses the Moment
  Calculator's rotate/torque icon path. Static content (no interactive
  inputs), in order:
  1. **What is a moment?** — definition (turning/rotational effect of a
     force about a point) + wrench-turning-a-bolt analogy + the sign
     convention (CCW positive, CW negative) already used everywhere on the
     site. One schematic diagram: two small pivots side by side, each with
     a precomputed-polyline arc + arrowhead — left one sweeping CCW
     (labeled "+"/"CCW (positive)"), right one sweeping CW (labeled
     "−"/"CW (negative)") — purely conventional, not tied to numbers.
  2. **Calculating a moment** — leads with the **general formula**
     M = F × d, where d is the perpendicular distance from the pivot to
     the force's **line of action** (not along the force itself, unless
     that's the shortest path); 2a and 2b are then framed as two ways of
     finding that distance, not two unrelated formulas. This intro has
     its own small generic (non-numeric) diagram: a pivot dot, a force
     arrow that does *not* pass through the pivot, its line of action
     extended as a dashed line in both directions, and a dashed
     perpendicular dropped from the pivot to that line with a small
     right-angle tick where they meet, labeled "d" — the first
     appearance on this page of a right-angle tick mark (two short
     segments off the foot point, along the line direction and the
     perpendicular direction) and of showing a force whose line of
     action doesn't pass through the pivot at all.
     - **2a. Perpendicular case (M = F × d)**: wrench example — pivot
       (bolt) with a handle of length d = 0.25 m along +x, force F = 60 N
       straight down at the end → M = 60 × 0.25 = 15.00 N·m, deliberately
       **CW** (reinforces the sign convention from §1 with a real
       calculation rather than another CCW result). Diagram: pivot dot,
       thick handle bar, perpendicular force arrow, a small CW rotation
       arc near the pivot, and a top-left numeric corner-legend ("d = 0.25
       m" / "F = 60 N") with short inline tags ("d", "F") at the
       handle/arrow themselves — the corner-legend pattern (established on
       the Resolve topic's inclined-body diagrams) is what fixed an
       initial bug here where a full "F = 60 N" inline label overflowed
       past the card's right edge at this viewBox width. Lead sentence
       explicitly connects back to the general formula ("the given
       length is already the perpendicular distance d"). Diagram card
       widened from `sm:max-w-[280px]` to `sm:max-w-[420px]` (matching
       the §1/§2-intro schematic diagrams' size) so it isn't noticeably
       smaller than the other diagrams on this page — the SVG's
       `viewBox`/coordinates are untouched, since scaling a
       `width:100%` SVG up is purely a container-size change.
     - **2b. Force at an angle to the handle** (renamed from "Angled
       force: resolve first" — the old heading undersold the new
       content). Deliberately **drops all "position vector" framing**
       (no r-vector, no x/y coordinates) since that reads as confusing to
       students at this stage; reuses 2a's exact wrench/handle picture
       instead (same pivot, same d = 0.25 m handle) with F = 80 N applied
       at 60° to the handle — deliberately **CCW** this time (opposite
       sign from 2a) — and presents **two methods**, both landing on the
       same M ≈ +17.32 N·m. Each method is its own **diagram-left/
       text-right row** (2a's own layout pattern, `w-full sm:max-w-[420px]`
       diagram card, same widened size as 2a + `flex-1` text column),
       stacked one above the other,
       rather than the two diagrams sitting side-by-side with shared text
       below — this puts "Method 1"/"Method 2" as an **eyebrow label**
       (the site's existing uppercase/tracked/accent-colored pattern,
       documented under Design Direction, normally used for things like
       "PHASE 1 · 2 of a growing set") at the top of each text column,
       directly beside its own diagram, rather than baked into the
       diagram's own caption. Each diagram's own small caption above it
       is trimmed to just the descriptive title ("Extend the Line of
       Action" / "Resolve into Components"), since "Method 1"/"Method 2"
       itself now lives beside it in the text column.
       - *Method 1 — extend the line of action*: draw the force's line of
         action extended (dashed) until a perpendicular from the pivot
         can reach it, drop that perpendicular (dashed, with the
         right-angle tick from the §2 intro diagram). The diagram marks
         the **60° angle itself** (precomputed-polyline arc + label,
         never an SVG arc command) at the handle end, between the handle
         and the backward-extended line of action — this is the angle in
         the right triangle (pivot / handle-end / foot-of-perpendicular)
         whose hypotenuse is d and whose opposite side is d<sub>⊥</sub>,
         so marking it there is what visually justifies the
         d<sub>⊥</sub> = d sin 60° step rather than just asserting it.
         Corner legend shows all three numbers together, including the
         derivation inline rather than just the final value: "F = 80 N" /
         "d = 0.25 m" / "d<sub>⊥</sub> = 0.25×sin60° ≈ 0.217 m". A short
         "d" tag also sits on the handle bar itself (matching 2a). The
         diagram also separately marks the 60° angle **from the x-axis
         (the handle's own forward direction) to F itself** — a short
         dashed reference tick continuing the handle past its end, plus
         a second precomputed-polyline arc + "60°" label between it and
         F — distinct from (though numerically equal to, as vertically
         opposite angles) the triangle-construction 60° described above;
         this is the intuitive "how far above horizontal is the force"
         reading, reusing the exact arc Method 2 already draws between
         F<sub>∥</sub> and F.
       - *Method 2 — resolve into components*: split F into F<sub>∥</sub>
         (along the handle, = F cos 60° = 40.00 N) and F<sub>⊥</sub>
         (perpendicular to it, = F sin 60° ≈ 69.28 N) at the point of
         application (muted component arrows + dashed closing lines,
         same convention as the Resolve topic's inclined-body diagrams);
         the same 60° angle is marked here too, between the handle/
         F<sub>∥</sub> direction and F itself. F<sub>∥</sub> needs no
         extra markup to show it passes through the pivot — it's drawn
         continuing the same straight line as the solid handle bar
         itself, so the zero-arm claim is visually obvious — so its
         moment is 0, F<sub>⊥</sub> × d = 69.28 × 0.25 ≈ 17.32 N·m, total
         17.32 N·m. Corner legend: "d = 0.25 m" / "F<sub>∥</sub> = 40.00 N"
         / "F<sub>⊥</sub> = 69.28 N", plus the same "d" tag on the handle.
       Both diagrams reuse the same CCW rotation arc + "M = 17.32 N·m
       (CCW)" label placement (pivot-centered, lower-left quadrant) from
       the old single r-based 2b diagram. One bug caught and fixed here:
       Method 1's "d<sub>⊥</sub>" label first sat directly on top of the
       thick handle bar (offset chosen in the wrong direction off the
       perpendicular segment) — fixed by re-offsetting it to the open
       space below the handle instead.
  3. **Net moment from several forces** — ΣM = M₁ + M₂ + …, same sign
     convention. Worked example, 3 forces with deliberately mixed CCW/CW
     contributions (echoes the "one force per quadrant" approach from the
     Resolve topic's resultant example):
     - F1 = 40 N @ 90°, position (0.5, 0) m → M1 = +20.00 N·m
     - F2 = 30 N @ 0°, position (0, 0.4) m → M2 = −12.00 N·m
     - F3 = 25 N @ 180°, position (−0.3, −0.2) m → M3 = −5.00 N·m
     - Net = +3.00 N·m (CCW) — small and mixed-sign, showing real
       cancellation rather than everything agreeing.
     Table (Force / Magnitude / Angle / Position / **d** / **Moment
     (F × d)** + a Σ row) — rather than the algebraic
     M = x·F<sub>y</sub> − y·F<sub>x</sub> formula, the table uses 2a's
     own perpendicular-distance method directly: each of the three
     forces is exactly horizontal or vertical by construction, so d
     (the perpendicular distance from the pivot to the force's line of
     action) is just the *other* coordinate of its point of application
     (F1 is vertical → d is its x; F2/F3 are horizontal → d is the size
     of their y) — a fact spelled out in the paragraph right before the
     table, not just asserted. The Moment column shows the substituted
     F × d equation per row (e.g. F1: "40 × 0.5 = +20.00 N·m"), not just
     the final value, and the Σ row shows the summing equation the same
     way ("20.00 − 12.00 − 5.00 = +3.00 N·m"), so the table demonstrates
     how each moment (and the net) is computed rather than only
     reporting results — while staying consistent with 2a's method
     instead of introducing the separate x·F<sub>y</sub> − y·F<sub>x</sub>
     formula (tried first, then replaced per feedback that all three
     forces here don't need it). Plus two
     diagrams in the same "Applied Forces" / "Net Moment" two-panel style
     the Moment Calculator tool itself uses — "Applied Forces" plots each
     force as an arrow from its position (dot marks the point, a larger
     dot + italic "pivot" caption marks the origin); "Net Moment" draws a
     large fixed-radius rotation arc around the pivot swept CCW (matching
     the net moment's sign) with a gap left open for the numeric label.
     Closing paragraph + inline cross-link
     (`data-action="show-section" data-section-id="tools"`) inviting the
     reader to reproduce it in the Moment Calculator.
  All diagram coordinates were precomputed with a small Node script (same
  approach as the Resolve topic) before writing the SVG markup. Verified
  by rendering every diagram at 2x scale and visually checking each one
  (catching and fixing the corner-legend overflow bug above) plus a full
  navigation regression across every section/topic/tool.
- **Categories removed** (done): the "Forces"/"Moments" category headings
  were removed from Concepts and Theory — both topics now sit in one flat
  `CONCEPTS_DATA` group with `category: ""`, rendered with no heading at
  all above the topic cards. `renderItemGrid` only skips the heading when
  `group[groupKey]` is falsy, so Tools' "Force & Moment" heading (and any
  future grid that wants categories) is unaffected. Verified visually
  (no stray heading/spacing) plus the same full navigation regression.
- **Equilibrium** (done): a `CONCEPTS_DATA` topic `equilibrium` (flat, no
  category — appended after `moment`), rendered at
  `#topic-view-equilibrium` (sibling of Resolve/Moment inside
  `#topic-views-container`); `CONCEPT_ICONS['equilibrium']` is a new
  simple balance-scale glyph (24×24 viewBox, stem + crossbar + two
  hanging pans as plain line/path elements) since no existing icon fit —
  first new icon added since the portal shell's initial set. **2D
  equilibrium only** (stated up front), split into concurrent vs.
  non-concurrent per the user's own framing, both worked examples solve
  for unknowns (confirmed via clarifying questions, not just "verify a
  fully-given system sums to zero"):
  1. **What is equilibrium?** — net force AND net moment both zero ⇒ a
     body stays at rest; explains why concurrent systems only need the
     two force equations (a force through a point has zero moment about
     it, so moment balance there is automatic) while non-concurrent
     systems need the moment equation too. One schematic diagram (no
     numbers, same style as the Moment topic's §1 CCW/CW schematic):
     three equal-length arrows from a common point at 0°/120°/240°
     (symmetric ⇒ sums to zero), labeled "ΣF = 0" — this is the first
     diagram on the page where three *arrows fanning out from one point*
     is the whole point (no axes, no pivot-vs-force distinction), so the
     "ΣF = 0" label needed to be moved off to one side (clear of all
     three arrow lines) after an initial placement sat right on the
     downward-left arrow.
  2. **Concurrent force systems** — formula block ΣF<sub>x</sub> = 0,
     ΣF<sub>y</sub> = 0 (explicitly no ΣM term). Worked example: a weight
     W = 100 N hangs from a junction held by two cables (30° and 60°
     above horizontal — clean 30-60-90 numbers), solving the 2×2 system
     for both tensions: **T1 = 50.00 N**, **T2 = 86.60 N**. Diagram: the
     junction point with three labeled force arrows at their true global
     angles (T1 up-left @150°, T2 up-right @60°, W straight down @270°),
     small angle arcs (precomputed polyline, not SVG arc) between a
     dashed horizontal reference line through the junction and each
     cable. The corner legend deliberately shows **"T1 = ?" / "T2 = ?"**
     rather than the solved values — this is a "find the unknowns"
     problem, so the diagram poses it rather than spoiling the answer;
     only W (given) shows a number. Per the same logic, no dot marks the
     T1/T2 arrow tips (a plain dot there — used elsewhere on the site for
     a *known* point of application, e.g. the beam diagram's support
     points below — would misleadingly suggest something about those
     forces is already fixed/known). Closing cross-check by substitution
     + inline cross-link to the Force Calculator (plug in all three
     forces, resultant ≈ 0).
  3. **Non-concurrent force systems** — formula block adds ΣM = 0,
     explicitly framed as "one more condition than the concurrent case."
     Worked example: the standard **pin + roller beam** problem — beam
     length L = 6 m, pinned at A (unknowns A<sub>x</sub>, A<sub>y</sub>),
     roller at B (unknown B<sub>y</sub> only), downward load P = 120 N at
     4 m from A. Solved in the standard order — ΣM<sub>A</sub> = 0 first
     (ties directly back to the Moment topic's 2a: M = F × d, and both
     pin reactions have zero moment arm about A so they drop out) →
     **B<sub>y</sub> = 80.00 N**; then ΣF<sub>y</sub> = 0 →
     **A<sub>y</sub> = 40.00 N**; then ΣF<sub>x</sub> = 0 →
     **A<sub>x</sub> = 0.00 N**, called out explicitly as a legitimate
     result (the pin *could* resist horizontal load, there's just none to
     balance here) rather than left looking like an omission. Diagram:
     horizontal beam bar, a pin-support glyph at A (triangle + ground
     hatching — new minimal symbol, first appearance of an engineering
     support glyph on this site) and a roller-support glyph at B
     (triangle on two small circles + ground line), the load arrow, a
     dimension line with two segment labels (4 m / 2 m), and **all three**
     reaction arrows drawn (A<sub>x</sub>, A<sub>y</sub>, B<sub>y</sub>) —
     A<sub>x</sub> is genuinely zero here, but it's still drawn (a short
     horizontal arrow just above the beam at A, offset up ~7px so it
     doesn't sit directly on top of the thick beam bar/collinear with
     it) so the diagram shows all three reactions being checked, not
     just the two nonzero ones; the corner legend's "Ax = 0.00 N" line
     states the actual result. None of the three reaction arrows are
     drawn to force-proportional length (unlike the load arrow, which
     is) — they're fixed at a uniform, easily-legible length instead,
     since a real-to-scale Ax arrow would be literally zero pixels long
     and unrenderable. One bug caught and fixed here: the diagram
     originally packed a 4-line numeric legend directly above a
     dimension line only 6px below it, so "Ax = 0.00 N" ran straight
     into the "4 m" label — fixed by shifting the entire
     beam/support/dimension geometry down 20px to clear the legend, not
     by shrinking the legend. Cross-check via moments about B instead +
     inline cross-links to both the Force Calculator (ΣFx/ΣFy) and Moment
     Calculator (ΣM about A).
  All diagram coordinates precomputed with a small Node script (same
  approach as the Resolve and Moment topics). Verified by rendering every
  diagram at 2x scale — this time including a proper `sm:` breakpoint
  media query in the test stylesheet (a gap in earlier verification runs
  for this file, caught while revisiting the Moment topic's 2a/2b) so the
  true side-by-side desktop layout was actually checked, not just the
  mobile-stacked fallback — plus the full navigation regression and a
  check that both new cross-links to Tools work.

### Phase 2+ — future tools, topics, and sections (not started)
- New tool: append to `TOOLS_DATA` and add a `#tool-view-<id>` section
  with its own logic.
- New Concepts and Theory topic: append to `CONCEPTS_DATA`'s single flat
  topics array (no categories) and add a `#topic-view-<id>` section.
- New section, or real content for Virtual Experiments: append to
  `SECTIONS_DATA` and add a `#section-view-<id>` with its own content;
  replace the ghost placeholder once there's something to show.
- None of the above touch shared navigation/theme/grid code.
