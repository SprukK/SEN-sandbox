# SEN Sandbox — Change Log

UI-only fork of SEN Ballistics Main (SprukK/SEN-Balistic-Calculator). Physics, formulas, drag tables, solvers, device protocols and the storage schema are never changed here; `tests/runtests.js` runs unchanged as the guard.
Versions: `V{Main base}-r{n}`. Deploy set per release: `RTI-SEN-Ballistic-Calculator-V{x}-redesign.html` + `index.html` (byte-identical) + `sw.js` (CACHE bumped) + this file. Newest entry on top.

---

## V2.10.0-r1 — Field screen: summary, answer, drag-or-type distance

**Problem.** On the outer screen the distance input sat at 1291 px, below the nav bar (852 px), and the clicks sat under a ~420 px reticle. Changing distance meant scrolling or swiping the chip bar. (UX review, 2026-11-03.)

**Change.**
- **New "Field" Home layout**, offered once at the end of the layout list (fresh installs and data imported from Main): Session summary · Answer · Distance · Reticle · Turret. Default and every other layout look exactly as before.
- **Three new Home modules.** *Session summary* — one line (rifle · projectile · MV · temperature); tap opens the six session pickers; the chip bar hides on Home while it is shown. *Answer* — large elevation / windage clicks with mrad and the status / warning line (Sg, cant, out of range), copied from the same solve as the reticle. *Distance* — tap opens the number sheet; hold 350 ms then drag: ← → ±1, ↑ ↓ ±10 per step (12 px / 16 px), dominant axis since the last step, ×2 / ×5 only on quick drags, clamped 1 unit to 1500 m, vibration on Android, one-time hint. Angle and wind buttons under it.
- **Reticle module** shows only the reticle while Answer is on (its clicks would repeat the Answer).
- **Distance sheet** (also from the chip): unit switch m / yd / ft that *is* Settings → Range — no second copy; Enter sets. Storage stays metres.
- **§20E:** after a rangefinder fill, a dragged or typed distance shows "✎ manual"; a matching one shows "⊙ <rangefinder>".
- **Slim header:** brand + page name. Version and solver info moved to the Settings note.
- **Fix:** the layout sheet's active layout was invisible (text and background both the accent colour) — also present in Main V2.10.0.
- Gestures: one Pointer Events helper (tap acts on the following click so a sheet never closes under the finger; `touch-action:none`, pointer capture, context menu and text selection suppressed on the control only).

**Files.** `index.html`, `RTI-SEN-Ballistic-Calculator-V2.10.0-r1-redesign.html` (identical), `sw.js` (CACHE `sen-sandbox-v2.10.0-r1`), `tests/fieldtest.js` (new), `tests/homelayouttest.js` (expects six layouts and 17 modules — intended UI change), `tests/README.md`.

**Verify.** All 31 Main suites + `fieldtest.js` (34/34) green; `runtests.js` unchanged (143/143). Real Chromium with CDP touch at 412 × 915 and 780 × 720: long press, drag ±1 / ±10, quick swipe ignored, tap → sheet → yd, no page scroll during drag, no console errors. **On the Honor (owner):** thumb hold-drag on both screens, no Chrome long-press menu, vibration felt.
