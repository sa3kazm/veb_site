# Guidance for AI coding agents working on this repository

Quick orientation (what this repo is)
- A small static site for "ITveb Academy" located at the repository root: `index.html`, `style.css`, and `logo.png`. The app is plain HTML/CSS with inline JavaScript in `index.html` (no build system).
- The live UI is driven by DOM ids and classes (no frameworks). Changes are visible by opening `index.html` in a browser (or using a static server / Live Server extension).

High-level architecture & why things are structured this way
- Single-page, static HTML with sections stitched together. The primary reasoning is a simple marketing site: content sections (hero, courses, reviews, teachers, benefits, FAQ) are in `index.html` and toggled via inline JS.
- Visual/interactive rules live in `style.css`. Animations rely on CSS classes like `.fade-in-section`, `.fade-in`, and `@keyframes`.
- Persistence for mock auth uses `localStorage` keys (see javascript helpers) — there is no backend.

Key files and important IDs/classes (use these when making edits)
- `index.html` — single source of truth for DOM structure and inline JS. Important IDs:
  - `main-categories` — the top-level category grid
  - `btn-programming`, `btn-design`, `btn-english`, `btn-kids-it`, `btn-kids-soft` — category entry cards
  - `programming-courses`, `design-courses`, `english-courses`, `kids-it-courses`, `kids-soft-courses` — course lists shown when clicking categories
  - `course-select-panel` — (new) overlay used for Programming to choose level (e.g., beginner/advanced)
  - `course-detail` — (new) single-course detail view shown after selection or direct click
- `style.css` — visual styles. Reuse existing classes when possible:
  - `.course-card` — card component used everywhere for courses
  - `.fade-in-section` and `.fade-in` — used for CSS-driven appearance animations
  - `.back-btn` — styling for "back" buttons

Developer workflows / how to test locally
- Fast manual test: open `index.html` in a browser. For consistent results (CORS for some remote assets), run a tiny static server:
  - Python 3: `python -m http.server 8000` in repo root.
  - Or use VS Code Live Server extension (recommended for iterative changes).
- Use browser DevTools console to inspect runtime JS state. The inline JS exposes localStorage keys described below.

Project-specific conventions and patterns (do not invent):
- Inline script pattern: Most behavior is implemented inline in `index.html` near the bottom. When adding behavior, prefer grouping new DOM handlers inside the same script block (keep the file single-page and simple).
- ID-based toggling: UI navigation hides/shows entire containers by setting `element.style.display = 'none' | 'block'` (or `grid`). Follow the existing pattern so state transitions remain predictable.
- Course wiring: course cards are discovered using container-specific queries (e.g. `document.querySelectorAll('#programming-courses .course-card')`). To add a new course, add another `.course-card` element inside the appropriate container.
- Programming special-case: Programming courses show a level selection overlay first (`#course-select-panel`) and only after selecting a level the single-course detail view (`#course-detail`) is shown. Other directions go directly to the course detail view.
  - If you need to change which container uses the selection step, update the small `courseContainers` array in the inline script (it controls `requireSelection`).

Integration points & external dependencies
- No backend or package manager. External images are hotlinked in `index.html`.
- Mock authentication uses `localStorage` keys:
  - `itevb_logged_user` — currently logged-in user's object (see `getLoggedUser` / `setLoggedUser` in `index.html`).
  - `itveb_registered_users` — array of registered users (mock registration flow).
- To integrate a real backend, replace localStorage calls with fetch/XHR to your API and keep the JS hooks.

Concrete examples from the codebase (copy/paste-able snippets)
- Add a new course to Programming (HTML):
  - Put a new `.course-card` inside the `#programming-courses .course-grid` block. The click handler reads the `<h3>` text as the course name.
- Where selection behavior lives (change to immediate detail):
  - Inline script includes `const courseContainers = [...]` mapping `id` -> `requireSelection`. Set `requireSelection: false` to skip the selection overlay.

Known gaps and safe next steps
- There is no test suite. Changes should be validated manually in-browser.
- If you add larger interactive features, consider moving JS into a small separate file (e.g. `app.js`) and load it with a `<script src="app.js"></script>` so it's easier to test and version.

If something in this guide is unclear or missing (for example you want concrete examples for adding a new category, or prefer the selection UI to be a modal instead of an overlay), tell me which part and I will update this file.
