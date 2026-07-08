# RTH Session VWAP

A TradingView (Pine Script **v6**) indicator that plots timezone-correct session VWAPs
(New York, London, Asia) plus a continuous 24h/daily VWAP, with optional standard-deviation bands.

Built for intraday futures trading where the chart is viewed from a timezone that differs
from the exchange — the sessions compute in explicit market timezones, so they plot correctly
no matter what timezone your chart is set to (e.g. Manila), and daylight saving is handled
automatically.

- **Website:** https://raoulsson.com
- **Repo:** https://github.com/raoulsson/trading-view-script-rth-session-wvap

## Attribution

This is a modified derivative of **"Koalafied VWAP Session/Day"** (published as *Session VWAP*)
by **TJ_667**, released under the Mozilla Public License 2.0. All original credit goes to TJ_667.
This project keeps the same MPL-2.0 license (see [`LICENSE`](./LICENSE)).

## What changed vs. the original

- **Timezone-correct sessions.** Each session is computed in an explicit timezone
  (`America/New_York`, `Europe/London`, `Asia/Tokyo`) via the timezone argument of `time()`.
  The original relied on the chart/exchange timezone, which broke session boundaries when the
  chart was viewed from another timezone. DST is now handled automatically.
- **New York = RTH.** The NY session defaults to the regular cash session, **09:30–16:00 ET**,
  so the NY VWAP anchors to the 09:30 open.
- **Pine v6 migration.** `time()` results (int-or-`na`) are converted to real booleans, since v6
  removed implicit `int`/`float` → `bool` casting; the daily reset uses an explicit boolean
  condition rather than a numeric one.
- **24h VWAP.** An always-on VWAP line anchored to the trading day, drawn independently of the
  session VWAPs.
- **Per-session toggles.** Independent show switches for each session (Asia and London off,
  New York on by default), plus a master switch.
- **Presets.** Line colors and width preset (Asia yellow, London blue, NY green, 24h amber),
  session/daily labels off by default.
- **Clean legend.** All inputs and helper plots are hidden from the status line, so the chart
  legend stays uncluttered while the values remain in the Data Window and Inputs tab.

## Installation

1. Open TradingView → **Pine Editor**.
2. Paste the contents of [`RTH_Session_VWAP.pine`](./RTH_Session_VWAP.pine).
3. Click **Add to chart**.
4. If you had a previous version loaded, open the indicator settings and use
   **Defaults → Reset settings** (or remove and re-add) so the presets take effect.

## Settings

- **Display:** master + per-session VWAP toggles, 24h VWAP, daily VWAP, std-dev bands, labels.
- **Calc:** std-dev multipliers, VWAP source, label offset.
- **Sessions:** per-session timezone and session hours.

Session hours are entered in each session's own market timezone (e.g. NY `0930-1600`).

## Notes

- On futures, "24h/Daily" anchors to the exchange trading-day boundary (Globex reset), which is
  the standard daily VWAP anchor.
- Std-dev bands are off by default; enable them under Display.

## License

Mozilla Public License 2.0 — see [`LICENSE`](./LICENSE).
Original © TJ_667. Modifications © 2026 raoulsson.
