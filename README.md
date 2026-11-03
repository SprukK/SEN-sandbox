# SEN-sandbox — UI redesign fork

UI-only fork of the SEN Ballistic Calculator (Main, SprukK/SEN-Balistic-Calculator).

- Base: Main **V2.10.0** (commit efd3da4, 2026-10-31), copied byte-for-byte as `RTI-SEN-Ballistic-Calculator-V2.10.0-redesign.html`.
- Scope: layout, styling and interaction only. Physics, formulas, drag tables, solvers and device drivers are not touched.
- Guard: every suite in `tests/README.md` must stay green against the redesign file, e.g. `node tests/runtests.js RTI-SEN-Ballistic-Calculator-V2.10.0-redesign.html`.
- Installable separately from Main on the same origin (sprukk.github.io): own IndexedDB `sen_sandbox`, localStorage `sen_sandbox_v1`, caches `sen-sandbox-*`, manifest id `/sen-sandbox-sprukk/`, header "SEN·Sandbox". Main's data is never read or written.
- To work with real profiles: Main → Export, then Sandbox → Import (one-way).
- Tests: the storage-name constants in 12 suites were renamed to the sandbox names; migration suites read old Main releases under Main's key (`OLD_SK`). Previous-release builds are taken from the Main repo, e.g. `node storagetest.js ../index.html <main-repo>/RTI-SEN-Ballistic-Calculator-V1.17.2.html`.
