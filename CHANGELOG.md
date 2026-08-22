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
