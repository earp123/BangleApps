# rareBit

Referee app for rareBit Flag integration and match timers.

## Firmware requirement

This app scans for rareBit Flags on the BLE **coded PHY** (long range), which
`NRF.setScan` only supports on **firmware 2v26 or later** (nRF52840 devices,
i.e. Bangle.js 2). On older firmware the scan will fail to start. Extended
advertising is enabled automatically whenever the PHY is not `1mbps`.

## Usage

- **Tap** the screen (before the match has started) to cycle the half length
  in 5 minute steps (10–45 minutes). Once the timer has been started, tapping
  no longer changes or resets the clocks.
- **Press the button** to start or pause the timer. Timing is derived from a
  start timestamp, so it stays accurate across pause/resume.
- When the countdown reaches 00:00 the watch buzzes once and the count-up
  clock keeps running — that's your stoppage time.
- The first two rareBit Flags seen are assigned **AR1** and **AR2** (double
  buzz on assignment). A recognised flag raise gives a distinct buzz pattern
  per AR (long buzz for AR1, tap burst for AR2) and flashes its label.
- Exit with the back icon in the top-left corner; this stops the timers and
  the BLE scan.

## Power

The app keeps the screen on and scans constantly (passive scan, filtered on
the rareBit service UUID) so flag alerts are not missed. Constant scanning
draws roughly 12 mA while the radio is in RX, so expect significantly higher
battery drain than normal watch use — charge before match day.
