# SEN Sandbox — Change Log

UI-only fork of SEN Ballistics Main (SprukK/SEN-Balistic-Calculator). Physics, formulas, drag tables, solvers, device protocols and the storage schema are never changed here; `tests/runtests.js` runs unchanged as the guard.
Versions: `V{Main base}-r{n}`. Deploy set per release: `RTI-SEN-Ballistic-Calculator-V{x}-redesign.html` + `index.html` (byte-identical) + `sw.js` (CACHE bumped) + this file. Newest entry on top.

---

## V2.10.0-r4 — Type scale and 48 px tap targets

**Problem.** UI review items 4 and 5: on the outer screen 1,218 of 1,323 visible texts were under 12 px (smallest 8 px, median 10 px, mostly spaced-out monospace capitals) and 281 of 296 controls were under 48 px tall (profile row actions 23 px, Settings toggles 36 px). Hard to read in sun and to hit with a cold thumb.

**Change.** One CSS layer at the end of the stylesheet (sizes only), plus five inline styles that were out of its reach:
- **Type.** Labels and small titles in sans, sentence case, 12.5–14 px; notes and flags 13 px with 1.5 line height; values 13 px mono; tables 12 px; inputs 16 px (stops a phone zooming into a field); bottom-nav labels 12 px. Monospace kept for numbers, inputs and tables. `--text3` (quiet text) lifted from #7a776e to #948f84 for ≥ 4.5 : 1 on the panels. Large / XL text still scale everything.
- **Taps.** Buttons, inputs, selects, segment toggles and disclosure rows ≥ 48 px; More-sheet tiles 56 px. The small "from profile" source chips keep their look with an invisible ≥ 44 px hit area.
- **Knock-on fixes.** Profile rows: actions drop under the name when narrow (no sideways scroll). Panel headers with "+ Add": flex row instead of a float the taller button pushed into the list. Inner screen: rail and divider end 68 px up, above the bottom nav that grew with its labels.
- **Removed** the temporary back-button diagnostic in Settings (back confirmed on the Honor, 2026-11-04).

**Measured** (all pages, session set; outer / inner): text under 12 px 1,218 → 0 / 1,313 → 0; controls under 48 px 281 → 8 / 317 → 8, the 8 being the source chips (hit area 51 px); inputs under 48 px 69 → 0; no sideways scroll; no errors.

**Files.** `index.html`, `RTI-SEN-Ballistic-Calculator-V2.10.0-r4-redesign.html` (identical), `sw.js` (CACHE `sen-sandbox-v2.10.0-r4`), `tests/typetest.js` (new, 18 — real Chromium, every page at 412 / 780 px and with Large text; fails on Main V2.10.0 with 9 of 18), `tests/devicetest.js` (diagnostic gone), `tests/README.md`.

**Verify.** All 31 Main suites + `fieldtest.js` 43 + `devicetest.js` 41 + `typetest.js` 18 green; `runtests.js` unchanged 143/143. **On the Honor (owner):** readability in daylight, whether anything now feels too big on the outer screen.

---

## V2.10.0-r3 — Back button that works on Android; draggable pane divider; no pull-to-refresh with devices connected

**Owner feedback on r2 (2026-11-03).** On the Honor the back button never showed the "press back again" toast; the inner-screen divider should be draggable; pull-to-refresh reloads the app and drops every Bluetooth link.

**Cause (back).** Chrome's history-manipulation intervention: an entry added without a user gesture makes the entry it was added from "skippable", and back then jumps straight past it — out of the app. r2 added its guard at boot and again inside the back handler, both without a gesture. Desktop Chromium in the tests did not show it.

**Change.**
- **Back button.** Guards are added only from a real gesture (`pointerup`, `keydown`, `click` capture listeners — where Chrome grants the gesture) and topped up to three; a back press never adds one. Back: close the sheet → previous page → Home → "Press back again to close". If guards are left at the toast they are dropped with one `history.go(−n)` (its popstate ignored), so the next press exits. No timers.
- **Diagnostic** in Settings (temporary): "Back button: guards · history length · gesture · last action" — a screenshot shows what happened on the device.
- **Pane divider** (inner screen, any two-pane page): a handle on the pane edge; drag left / right resizes both panes live (right pane 30–70 % of the screen), double-tap resets to the page's default width. One value for all pages, stored as a share of the width (survives rotation). The reticle grows with its pane.
- **Pull-to-refresh** is blocked (`overscroll-behavior-y: contain`) while any Calypso, rangefinder or LabRadar is connected; allowed again when none is. **Settings → Reload app** reloads on purpose, asking first when devices are connected.

**Files.** `index.html`, `RTI-SEN-Ballistic-Calculator-V2.10.0-r3-redesign.html` (identical), `sw.js` (CACHE `sen-sandbox-v2.10.0-r3`), `tests/devicetest.js` (41: back guards only from a gesture, top-up, drain, pull-to-refresh), `tests/fieldtest.js` (43: divider drag, clamp, restart, double-tap reset). Main code touched: `applyPane` / `applyLayout` / `setPaneMode` call `divPos()`; `openSheet` arms guards.

**Verify.** All 31 Main suites + `fieldtest.js` 43/43 + `devicetest.js` 41/41 green; `runtests.js` unchanged 143/143. Chromium 780 × 720 with CDP touch: divider drag 452 → 392 px and double-tap back to 452, no sideways scroll; tap → sheet → real `history.back()` closes it → back on Home shows the toast and drops the spare guards; no errors. **On the Honor (owner):** back walks sheets / pages, toast on Home, second press closes; divider with the thumb; pull-down does nothing with a Calypso connected.

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
