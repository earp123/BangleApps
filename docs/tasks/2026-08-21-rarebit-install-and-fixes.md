# Task: rareBit loader install path + app bug/compat fixes

Repo: earp123/BangleApps · Work branch: `rarebit-fixes` (from `master`) · App: `apps/rareBit` · Version bump: 0.10 → 0.11

## 1. Fix loader install path — `apps/rareBit/metadata.json`

Current storage block uploads `rareBit.img` as the app source under a wrong-case name (`rarebit.app.js`); the real source never ships. Replace with:

```json
"storage": [
  {"name":"rareBit.app.js","url":"rareBit.app.js"},
  {"name":"rareBit.img","url":"rareBit.img","evaluate":true}
]
```

- Remove the nonstandard `"src"` key.
- Delete checked-in `apps/rareBit/rareBit.info` — the loader auto-generates `.info`.
- Add `"tags": "tool,bluetooth,timer"`, set `"version": "0.11"`.
- Verify `rareBit.img` is a valid evaluate-able icon string (e.g. `require("heatshrink").decompress(atob("..."))`). If it is raw image data, regenerate the icon JS from `rareBit.png` per upstream convention.
- Add `apps/rareBit/ChangeLog` (`0.11: Fix loader install path; timer + scanner fixes`) and a short `README.md` noting firmware requirement (see §3).

## 2. App fixes — `apps/rareBit/rareBit.app.js`

1. **Scan lock leak**: the `if (!mac) return;` path exits with `lock=true`, permanently silencing the scanner. Ensure no code path leaves the lock held — or remove the lock entirely (Espruino runs the setScan callback from the event loop; it is not reentrant).
2. **Tap-while-paused reset hazard**: touch currently changes `setMinutes` and zeroes the clocks whenever `!isRunning`, including mid-match pause. Only allow duration change when the timer has never started or was fully reset (`elapsed == 0 && !isRunning`).
3. **Timer drift**: derive `remaining`/`elapsed` from a start timestamp (`Date.now()`), using the 1 s interval only to redraw. Target ≤1 s error over 45 min including pause/resume.
4. **Stoppage time**: when `remaining` hits 0, buzz once and let `elapsed` continue counting up (count-up = stoppage time). Remove the dead `finished` flag.
5. **AR flash indicators**: wire `startFlashingAR(1|2)` to the `AR1flash`/`AR2flash` events; delete the commented-out flash code in `draw()`.
6. **Exit path + cleanup**: single `Bangle.setUI({...})` call (remove the duplicate empty call) with `back` (top-left exit icon; coexists with `btn`) and `remove` handlers. On exit: clear all intervals, `NRF.setScan()` to stop scanning, restore `Bangle.setLCDTimeout` default, then `load()`.

## 3. Firmware compatibility

- `NRF.setScan` with `phy:"coded"` requires **firmware 2v26+** (nRF52840). Document in README. `extended` auto-enables when phy ≠ 1mbps.
- Keep passive scan + 128-bit service filter (`23210001-28d5-4b7b-bad0-7dee1eee1b6d`) — filter matches per-packet against the primary adv payload. Flag-side adv content verification is out of scope here.
- Leave scan duty-cycle (`window`/`interval`, 2v26+) at default constant scan for alert reliability; note ~12 mA RX draw in README.

## Acceptance

- App installs from the restricted app loader onto a 2v26+ Bangle.js 2 and launches from the launcher.
- Timer: accurate over a 45-min half, pause/resume works, no reset from tap while paused, count-up continues past 0:00.
- Scanner: distinct buzz patterns + AR1/AR2 flash on recognized flags; survives malformed/empty adv packets.
- Exit via back icon leaves no running intervals or active scan.
- `bin/sanitycheck.js` passes; ChangeLog + version bump present.
