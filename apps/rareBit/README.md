# rareBit

Referee app for rareBit Flag integration and match timers.

## Firmware requirement

This app scans for rareBit Flags on the BLE **coded PHY** (long range), which
`NRF.setScan` only supports on **firmware 2v26 or later** (nRF52840 devices,
i.e. Bangle.js 2). On older firmware the scan will fail to start. Extended
advertising is enabled automatically whenever the PHY is not `1mbps`.

## Usage

Both tap actions below only work while the app is idle (the match has never
been started, or has been fully reset). Once the timer has been started,
tapping no longer changes or resets the clocks.

- **Tap the countdown** (the big clock in the middle) to cycle the half length
  in 5 minute steps (10–45 minutes).
- **Tap the count-up** (the small clock underneath) to toggle a **second-half
  preload** between 00:00 and the currently selected interval — e.g. with 45
  selected, one tap shows 45:00, another returns it to 00:00. Repeated taps
  never stack. Start the match and the countdown runs the full interval as
  usual while the count-up carries on from the preload, so a second half ends
  at 90:00. Changing the interval while a preload is showing updates the
  preload to match.
- **Press the button** to start or pause the timer. Timing is derived from a
  start timestamp, so it stays accurate across pause/resume.
- When the countdown reaches 00:00 the watch buzzes once and the count-up
  clock keeps running — that's your stoppage time.
- The first two rareBit Flags seen are assigned **AR1** and **AR2** (double
  buzz on assignment). A recognised flag raise gives a distinct buzz pattern
  per AR (long buzz for AR1, tap burst for AR2) and flashes its label.
- Exit with the back icon in the top-left corner; this stops the timers and
  the BLE scan.

## Theme

The app follows the global Dark/Light theme from the Settings app — all text
and backgrounds come from `g.theme`, read at draw time. The AR flash indicator
uses a theme-relative accent (yellow on dark, blue on light) so a raised flag
stays obvious either way. Changing the theme in Settings reloads the running
app, so the new theme takes effect the next time rareBit is started.

## Power

The app keeps the screen on and scans constantly (passive scan, filtered on
the rareBit service UUID) so flag alerts are not missed. Constant scanning
draws roughly 12 mA while the radio is in RX, so expect significantly higher
battery drain than normal watch use — charge before match day.
