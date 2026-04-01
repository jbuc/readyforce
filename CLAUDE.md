# CLAUDE.md — READYFORCE

This file guides AI assistants working in this repository.

## Project Overview

**READYFORCE** is a single-page web application (SPA) that helps aspiring firefighters in St. Louis County navigate the hiring process. It is a personal educational tool built by Jacob White.

- **Purpose**: Interactive guide covering the EMT/paramedic roadmap, CPAT physical prep, and interview/vetting prep
- **Tech stack**: Vanilla HTML/CSS/JS — no framework, no build step, no package manager
- **Entry point**: `index.html` (the entire application — 691 lines)

## Repository Structure

```
readyforce/
├── index.html      # The entire application (HTML + embedded CSS + embedded JS)
├── README.md       # Brief project description
├── LICENSE         # Apache 2.0
└── CLAUDE.md       # This file
```

There are no subdirectories, no build artifacts, no test files, and no config files beyond what git provides.

## Architecture

Everything lives in `index.html`. It is organized as follows:

| Block | Location | Notes |
|---|---|---|
| `<head>` | Lines ~1–30 | Meta, CDN links (Tailwind, Google Fonts, Material Symbols), Tailwind config |
| `<style>` | Lines ~31–130 | Custom CSS (scrollbar, checkboxes, shadows, accordion transitions) |
| `<body>` | Lines ~131–650 | All HTML markup |
| `<script>` | Lines ~651–691 | ~120 lines of vanilla ES6+ JS |

### External CDN Dependencies

These are the only dependencies. They are loaded at runtime from the internet — there is no local copy or lock file.

- **Tailwind CSS** — `https://cdn.tailwindcss.com` (utility classes)
- **Google Fonts** — Space Grotesk (headlines) + Inter (body)
- **Material Symbols Outlined/Filled** — icon font

## Design System

The app uses a **brutalist** aesthetic. Tailwind is configured with a custom color palette and shadow utilities:

| Token | Value | Usage |
|---|---|---|
| `background` | `#f5f0e8` | Cream page background |
| `primary` | `#ffcc00` | Bright yellow accents |
| `secondary` | `#e63b2e` | Bright red (warnings, CTAs) |
| `tertiary` | `#0055ff` | Bright blue (links) |
| `dark` | `#1a1a1a` | Near-black text/borders |
| `light` | `#ffffff` | White surfaces |
| `surface` | `#eee9e0` | Light secondary surface |

Custom shadow classes: `shadow-brutal`, `shadow-brutal-hover`, `shadow-brutal-lg`, `shadow-brutal-red`, `shadow-brutal-blue`

Heavy borders (2–8px solid `dark`) are a consistent visual motif.

## Application Sections

### 1. Roadmap (`#roadmap`)
Three-phase card layout (Education & Med → Academy → Application) plus a collapsible 3-year timeline accordion.

### 2. Physical (`#physical`)
CPAT event readiness tracker — 8 checkboxes, progress bar. State is persisted to `localStorage` under key `readyforceCpatState` (JSON array of 8 booleans).

### 3. Vetting (`#interview`)
Mock oral board questions (accordion), psych eval info, disqualifier card (opens modal), background/district research cards.

### Modals
- `#checklist-modal` — PDF download confirmation
- `#disqualifier-modal` — Lists hard disqualifiers (felonies, DUI, domestic violence, drug use)

## JavaScript Conventions

- **Vanilla ES6+** — no frameworks, no imports/modules
- `camelCase` for variables and functions
- `kebab-case` for HTML IDs and data attributes
- DOM selected via `querySelector` / `querySelectorAll`
- Event listeners attached directly (no delegation pattern)
- `localStorage` used for state; serialize with `JSON.stringify` / `JSON.parse`
- Scroll spy updates sidebar nav highlighting on scroll
- Accordions enforce single-open-at-a-time behavior
- Sidebar toggle is responsive: fixed on mobile, sticky on desktop (`window.innerWidth`)

## CSS Conventions

- Tailwind utility classes are the primary styling method
- Custom classes in `<style>` are used only for things Tailwind cannot express (scrollbar, custom checkboxes, keyframe animations)
- Class names follow BEM-adjacent naming: `.nav-top-link`, `.accordion-btn`, `.brutal-checkbox`
- Responsive breakpoints: `sm`, `md`, `lg` (Tailwind defaults)

## Making Changes

Because this is a single-file app with no build step:

1. **Edit `index.html` directly.** There is no compilation, bundling, or transpilation.
2. **Test by opening in a browser.** No server required for basic functionality.
3. **LocalStorage state** will persist across reloads. Clear it via DevTools > Application > Local Storage if testing fresh-start behavior.
4. **Do not introduce a build system or package manager** unless there is a clear, deliberate decision to change the architecture.
5. **Do not add new CDN dependencies** without checking that the new library is compatible with the brutalist design and doesn't conflict with Tailwind.

## State Management

The only persistent state is CPAT checkbox progress:

```javascript
// Save
localStorage.setItem('readyforceCpatState', JSON.stringify(stateArray));

// Load
const savedState = JSON.parse(localStorage.getItem('readyforceCpatState'));
```

There is no backend, no API, no user authentication, and no database.

## Git Workflow

- Main branch: `main`
- Active development branch convention: `claude/<description>-<id>`
- Commit messages are imperative, sentence-case (e.g., `First Draft Setup`)
- All work should be committed and pushed to the designated feature branch

## What This Project Is Not

- Not a Node/npm project — do not run `npm install`
- Not a Python project — do not create a virtualenv
- Not a framework app (no React, Vue, Svelte, etc.)
- Not deployed via CI/CD — no `.github/workflows/` exists
- Not connected to any external API or database

## Common Tasks

| Task | How |
|---|---|
| Add a new CPAT event | Add a `<li>` to the CPAT list in `#physical`, update the JS array length check |
| Add an interview question | Add an accordion item inside `#interview`'s mock boards section |
| Change a color | Update the Tailwind `theme.extend.colors` config in `<head>` |
| Add a new section | Add HTML block in `<body>`, add nav link in both the top nav and sidebar, update scroll spy targets in `<script>` |
| Add a disqualifier | Add a list item inside `#disqualifier-modal` |
