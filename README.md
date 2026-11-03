# SEN-sandbox — UI redesign fork

UI-only fork of the SEN Ballistic Calculator (Main, SprukK/SEN-Balistic-Calculator).

- Base: Main **V2.10.0** (commit efd3da4, 2026-10-31), copied byte-for-byte as `RTI-SEN-Ballistic-Calculator-V2.10.0-redesign.html`.
- Scope: layout, styling and interaction only. Physics, formulas, drag tables, solvers and device drivers are not touched.
- Guard: every suite in `tests/README.md` must stay green against the redesign file, e.g. `node tests/runtests.js RTI-SEN-Ballistic-Calculator-V2.10.0-redesign.html`.
- Not a PWA here yet: `sw.js`, `manifest.json` and `index.html` are deliberately left out. This repo would sit on the same origin as Main (sprukk.github.io), so it would share Main's IndexedDB (`sen_main`), CacheStorage and manifest id.
