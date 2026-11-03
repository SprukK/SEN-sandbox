# SEN Sandbox — Change Log

UI-only fork of SEN Ballistics Main (SprukK/SEN-Balistic-Calculator). Physics, formulas, drag tables, solvers, device protocols and the storage schema are never changed here; `tests/runtests.js` runs unchanged as the guard.
Versions: `V{Main base}-r{n}`. Deploy set per release: `RTI-SEN-Ballistic-Calculator-V{x}-redesign.html` + `index.html` (byte-identical) + `sw.js` (CACHE bumped) + this file. Newest entry on top.

---

## V2.10.0-r2 — Device row; Field is the default; Android back button; r1 fixes

**Owner feedback on r1 (2026-11-03).** Field should be the default; the distance number sat low in its box; the on-screen keyboard covered the Set / Cancel buttons of the distance sheet; Android's back button closed the app.

**Change.**
- **Device row** (new Home module *Devices*, on in Field under Distance): **Weather · Range · Chrono** pills. Tap pauses / resumes that kind of sensor; with nothing connected the tap opens its popup. Hold (350 ms, a bar fills under the pill) → on release, a popup listing the connected devices, each with its own Pause / Resume, plus Connect / Station setup / Shot string · Xero import.
  - *Weather* — pause all = Main's Home live-wind switch off and the last wind kept as manual; resume = live on again (per-unit pauses cleared). A paused Calypso drops out of the wind stations exactly like a stale one ("paused"); a paused session unit holds the session wind as manual.
  - *Range* — pause all = Main's "each ranging fills" switch off; a paused unit still shows its ranging but fills nothing.
  - *Chrono* (LabRadar LX) — paused: a shot is shown, not added to the string. The popup shows the string (shots, MV, ES, SD) and **Use MV → session** (Main's own `chUse`); MV never changes otherwise.
  - Pill states, readable without colour: outline "connect" · filled with a live dot and the last value · dashed gold "MAN" · "1/2" when some units are paused.
- **Field is the default layout**: first and in use; the pre-r1 Home is kept as **Classic**. Runs once per store, so r1 installs move over (their Field gets the device row) and data imported from Main gets it too.
- **Android back**: one guard history entry; back closes an open sheet → returns to the previous page → goes Home → on Home shows "Press back again to close" and the next press closes the app.
- **Distance value centred** vertically (number + unit as one baseline block).
- **Keyboard**: sheets sit above the on-screen keyboard (`--kb` from the visual viewport).
- **Gestures**: the click Chrome sends after a long press is swallowed (300 ms), so a popup opened by a hold no longer closes under the finger.

**Files.** `index.html`, `RTI-SEN-Ballistic-Calculator-V2.10.0-r2-redesign.html` (identical), `sw.js` (CACHE `sen-sandbox-v2.10.0-r2`), `tests/devicetest.js` (new, 33), `tests/fieldtest.js` (37: + Field default, r1 → r2 upgrade, keyboard offset), `tests/homelayouttest.js` and `tests/rendertest.js` (switch to Classic first — they measure the pre-r1 Home; 18 modules), `tests/README.md`. Device hooks in Main code are one-line holds: `stnState` (paused reason), `updateBleDisplay` (paused session unit), `rfFrame` (paused unit), `lrShot` (paused LabRadar), plus `devSync()` after `calRender` / `rfRender` / `lrRender`.

**Verify.** All 31 Main suites + `fieldtest.js` 37/37 + `devicetest.js` 33/33 green; `runtests.js` unchanged 143/143. Chromium with CDP touch at 412 and 780 px: tap and hold on the pills (popup stays open), real `history.back()` closes sheets and walks pages, toast on Home, no sideways scroll, no errors. **On the Honor (owner):** keyboard no longer covers Set / Cancel; back button behaviour; pills with the real Calypsos, Titan and LabRadar.

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
