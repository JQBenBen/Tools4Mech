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

## Working conventions
- Before implementing any non-trivial feature, ask clarifying
  questions about scope, edge cases, and constraints first —
  don't propose a plan until you've asked.

## Feature Plan

Living roadmap for the portal, broken into phases. Keep each phase a tight
bullet list, not prose. When a phase is finished, don't delete it — collapse
it to a one-line summary (e.g. "Phase 1 — Force & Moment: shipped. See git
history for details.") so the section stays skimmable as it grows.

### Portal shell (applies to every phase)
- Single `index.html`. Home view lists tool cards grouped by category
  heading. Clicking a card hides the home view and shows that tool's
  section; a back button returns home.
- Dark/light toggle persisted in `localStorage`, defaulting to OS
  preference until the user explicitly chooses.
- **Data model**: `TOOLS_DATA = [{ category, tools: [{ id, name,
  description }] }]` drives the home grid. Each tool's markup lives in its
  own `#tool-view-<id>` section. Adding a future tool = one data entry + one
  section + its own calculation wiring — navigation/theme code untouched.
- **Key flows**: delegated click handling (`data-tool-id` → show tool,
  `data-action="show-home"` → show home); shared dynamic add/remove-row
  pattern (`<template>`-based, min 1 row, default 2 rows) reused across
  calculators.

### Phase 1 — Force & Moment (status: not started)
- **Force Calculator**: dynamic list of {magnitude, angle} force rows → sum
  to components → resultant magnitude + angle (normalized to [0°, 360°)),
  components shown too.
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
