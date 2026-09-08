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
