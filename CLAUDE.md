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
- Built in `index.html`: home view lists tool cards grouped by category
  heading. Clicking a card hides the home view and shows that tool's
  section; a back button returns home. Verified working (Playwright
  click-through: card → tool view → back → home).
- Dark/light toggle persisted in `localStorage`, defaulting to OS
  preference until the user explicitly chooses. Verified working.
- **Data model**: `TOOLS_DATA = [{ category, tools: [{ id, name,
  description }] }]` drives the home grid. Each tool's markup lives in its
  own `#tool-view-<id>` section. Adding a future tool = one data entry + one
  section + its own calculation wiring — navigation/theme code untouched.
- **Key flows**: delegated click handling (`data-tool-id` → show tool,
  `data-action="show-home"` → show home) — implemented and proven. The
  shared dynamic add/remove-row pattern (`<template>`-based, min 1 row,
  default 2 rows) is still to be built when the first calculator's real
  logic is implemented.
- Both tools currently render as placeholders ("Calculator coming soon")
  inside their `#tool-view-<id>` sections — proves the navigation
  mechanism without any real calculation logic yet.

### Phase 1 — Force & Moment (status: in progress — Force Calculator done, Moment Calculator not started)
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
- **Moment Calculator**: dynamic list of {magnitude, angle, x, y} rows →
  per-row moment `x*Fy − y*Fx` → net moment (labeled CCW/CW/balanced) +
  per-force breakdown table.
- **Conventions/assumptions**: angle 0° = +x axis, CCW positive; units N /
  ° / m / N·m; 2-decimal display rounding computed from full-precision
  sums; an invalid/blank row blocks calculation and highlights that row;
  no URL/hash routing.

### Phase 2+ — future tools (not started)
- Extend by appending to `TOOLS_DATA` and adding a `#tool-view-<id>`
  section with its own logic. No changes to shared navigation/theme/grid
  code.
