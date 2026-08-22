# Task: rareBit 0.12 (theme + second-half preload) and served-app sync tooling

Repo: earp123/BangleApps · Work branch: `rarebit-012` (from `master`) · Version bump: 0.11 → 0.12

Trello refs: "Reconcile Global Dark theme setting", "Second half interval", "Keep auxillary apps updated".

## 1. Theme reconcile — `apps/rareBit/rareBit.app.js`

- Replace all hardcoded colors with `g.theme` (`fg`/`bg`/`dark`); read theme at draw time, clear with theme bg.
- AR flash indicators must remain high-contrast in both light and dark themes — pick flash colors relative to `g.theme.dark`, verify in emulator both ways.
- Respect theme immediately on app start (settings app reloads apps on theme change; no live listener needed — confirm and note in code comment).

## 2. Second-half preload — `apps/rareBit/rareBit.app.js`

Behavior (only while idle, i.e. timer not started/reset state):

- Tap on the **count-up (elapsed)** display: **toggle** the preloaded elapsed time between 00:00 and the currently selected interval. E.g. interval 45: tap → 45:00, tap again → 00:00. Additional taps never accumulate.
- Tap on the **countdown (interval)** display: unchanged — cycles interval selection.
- Requires tap-zone hit testing on touch y-coordinate; keep zones generous and non-overlapping.
- Starting the match runs the countdown normally; elapsed continues from the preload (45:00 → 90:00 for a second half).
- Changing the selected interval while a preload is active updates the preload to the new interval.
- Update `apps/rareBit/README.md` controls section; add ChangeLog entry.

## 3. Served-app sync tooling — `bin/sync-upstream-apps.mjs`

Goal: keep the non-rareBit served apps (`boot, antonclk, setting, widlock, widid, dtlaunch, widbt`) current with their upstream maintainers without full-repo merges.

- Script compares each served app's `metadata.json` version against `espruino/BangleApps` master (raw.githubusercontent) and reports out-of-date apps.
- `--pull` mode: copy the upstream app folder(s) over wholesale (never rareBit).
- Must also detect and pull `modules/` files the updated apps `require()` — the fork's modules dir predates current upstream and stale modules will break updated apps.
- Run it once in this task: report, pull updates, verify loader still serves all 8 apps cleanly (headless check + sanitycheck).
- Document usage in `docs/` (one short file) and add CHANGELOG entry.
- Cadence: manual, run before each release. **Deferred:** scheduled GitHub Action auto-PR — skip unless trivial; note in the changelog if deferred.

## Acceptance

- rareBit renders correctly in light and dark themes; AR flashes clearly visible in both.
- Second-half preload works per spec; tap zones don't interfere with interval selection or the started-match tap guard from 0.11.
- Sync script reports and pulls upstream versions for the 7 aux apps incl. required modules; loader serves all apps with no JS errors; sanitycheck + lint pass.
- rareBit ChangeLog + version 0.12; repo CHANGELOG updated.

## Defer & report

If any item can't be completed cleanly (e.g. an upstream aux app now requires loader/core features the fork lacks, or module conflicts), defer that item, leave its Trello checklist item unchecked, and note the blocker in the repo CHANGELOG under an "Open items" line.