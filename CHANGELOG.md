App Loader ChangeLog
====================

Changed for individual apps are listed in `apps/appname/ChangeLog`

* `Remove All Apps` now doesn't perform a reset before erase - fixes inability to update firmware if settings are wrong
* Added optional `README.md` file for apps
* Remove 2v04 version warning, add links in About to official/developer versions
* Fix issue removing an app that was just installed (fix #253)
* Add `Favourite` functionality
* Version number now clickable even when you're at the latest version (fix #291)
* Rewrite 'getInstalledApps' to minimize RAM usage
* Added code to handle Settings
* Added espruinotools.js for pretokenisation
* Included image and compression tools in repo
* Added better upload of large files (incl. compression)
* URL fetch is now async
* Adding '#search' after the URL (when not the name of a 'filter' chip) will set up search for that term
* If `bin/pre-publish.sh` has been run and recent.csv created, add 'Sort By' chip
* New 'espruinotools' which fixes pretokenise issue when ID follows ID (fix #416)
* Improve upload of binary files
* App description can now be markdown
* Fix `marked is not defined` error (and include in repo, just in case)
* Fix error in 'Install Default Apps' if Flash storage is full enough that erasing takes a while
* Fixed animated progress bar on app removal
* Added ability to specify dependencies (used for `notify` at the moment)
* Fixed Promise-based bug in removeApp
* Fixed bin/firmwaremaker and bin/apploader CLI to handle binary file uploads correctly
* Added progress bar on Bangle.js for uploads
* Provide a proper error message in case JSON decode fails
* Check you're connecting with a Bangle.js of the correct version
* Allow 'data' style app files to be uploaded with the app (and switch over settings files for various apps)

rareBit fork
------------

2026-08-22

* rareBit 0.11: fixed the loader install path so the app source actually ships
  (the old storage block uploaded raw image data under a wrong-case name);
  regenerated `rareBit.img` as a proper heatshrink-compressed JS icon; added
  per-app ChangeLog and README (2v26+ firmware requirement, scan power draw)
* rareBit 0.11 app fixes: timestamp-based match timing (no drift across
  pause/resume), tap can no longer reset the clocks mid-match, count-up
  continues past 00:00 as stoppage time with a single full-time buzz, scan
  callback lock leak removed (scanner no longer goes permanently silent),
  AR1/AR2 flash indicators wired to recognised flags, clean exit via the
  back icon (clears intervals, stops the scan, restores LCD timeout)
* Loader now serves the Desktop Launcher (`dtlaunch`) and Bluetooth widget
  (`widbt`); `Install default apps` fixed to install only apps this loader
  actually carries (it previously always failed with "Not all apps found")
* Updated `core` (EspruinoAppLoaderCore) and `webtools` (uart.js 1.14 → 1.27)
  submodules to the commits current upstream BangleApps pins, for firmware
  2v29 support; merged upstream index.html/loader.js changes while keeping
  the rareBit theme
* Removed `renderCustomTabs`, which threw `AppLibrary is not defined` on
  every page load
* Repo tooling made to pass on this restricted fork: sanitycheck skips the
  absent locale app, lint-exemption sync prunes entries for removed apps
* Known issue: if connecting sticks at "Getting device info", connect once
  with the Web IDE (espruino.com/ide), disconnect, then retry the loader
  (see https://github.com/orgs/espruino/discussions/7042)

2026-08-22 (later)

* rareBit 0.12: the app now follows the global Dark/Light theme — every colour
  comes from `g.theme`, read at draw time, and the screen is cleared with the
  theme background. The AR flash indicator uses a theme-relative accent
  (yellow on dark, blue on light) rather than a hardcoded black/white invert;
  both give ≥8:1 contrast against the screen background in their theme. The
  settings app reloads the running app on a theme change, so no live listener
  is needed (noted in a code comment).
* rareBit 0.12: second-half preload. While idle, tapping the count-up (elapsed)
  display toggles it between 00:00 and the selected interval — repeated taps
  never stack — and tapping the countdown still cycles the interval, now via
  y-coordinate tap zones (top 40 px reserved for the AR labels, countdown zone
  below it, count-up zone from y=124 down). Match time and displayed elapsed
  time are now tracked separately, so a 45:00 preload still runs a full 45 min
  countdown and the count-up finishes a second half at 90:00. Changing the
  interval while a preload is showing updates the preload to match. The 0.11
  guard still holds: neither tap does anything once the match has started.
* New `bin/sync-upstream-apps.mjs`: reports and pulls upstream versions of the
  seven auxiliary apps this loader serves, deriving each app's file list from
  its upstream `metadata.json` over raw.githubusercontent (no GitHub API or
  full clone), and syncing the `modules/` files those apps `require()`.
  `rareBit` is never touched. Documented in `docs/sync-upstream-apps.md`.
* Ran the sync: `boot` 0.65→0.69, `dtlaunch` 0.28→0.29, `antonclk` 0.11→0.12,
  `setting` 0.78→0.84, `widbt` 0.09→0.10, `widlock` 0.08→0.09, `widid`
  0.03→0.04, plus the new `modules/launch_utils.js` that `dtlaunch` 0.29
  requires. All 8 served apps still resolve cleanly (every storage file
  present, no duplicate names for BANGLEJS2, no unresolved module or app
  dependencies); sanitycheck and both lint runs are clean.
* `bin/sanitycheck.js` refreshed from upstream — the updated aux apps use
  metadata the fork's older copy rejected (`author`, `requires_firmware`,
  `BANGLEJS3`, `BANGLEJS3_COMPAT`). The fork's local tweak (skip the locale
  checks, since the restricted app set has no `locale` app) is re-applied.
  `apps/rareBit/metadata.json` gained the now-required `author` field.

Open items
----------

* rareBit 0.12 theme and flash rendering were **not** verified in the Bangle.js
  emulator: `espruino.com` is unreachable from the sandbox this was built in,
  so the emulator can't load. Verified instead by code inspection, a headless
  run of the draw/timer/tap logic against a stubbed `Graphics`/`Bangle`, and a
  contrast calculation for the flash colours in both themes. Worth an eyeball
  in the emulator or on-device before release.
* Scheduled GitHub Action to run `sync-upstream-apps.mjs` and open an auto-PR:
  deferred, as noted in `docs/sync-upstream-apps.md`. Cadence stays manual —
  run the script before each release.
