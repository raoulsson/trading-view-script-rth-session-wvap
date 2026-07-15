# "RTH Session VWAP" Indicator - TradingView Pine Script 

A TradingView (Pine Script **v6**) indicator that plots timezone-correct session VWAPs
(New York, Europe, Asia) plus a continuous 24h/daily VWAP, with optional standard-deviation bands.

Built for intraday futures trading where the chart is viewed from a timezone that differs
from the exchange — the sessions compute in explicit market timezones, so they plot correctly
no matter what timezone your chart is set to (e.g. Manila), and daylight saving is handled
automatically.

- **Website:** https://raoulsson.com
- **Repo:** https://github.com/raoulsson/trading-view-script-rth-session-wvap

## Screenshots

MNQ (Micro E-mini Nasdaq) 15m — session VWAPs with a volume-profile overlay for context.
The NY VWAP anchors to 09:30 ET; the 24h VWAP runs continuously across the trading day.

![RTH Session VWAP on MNQ 15m — session VWAPs with volume profile](./img/rth-session-vwap-1.png)

![RTH Session VWAP on MNQ 15m — session VWAPs overlaid with intraday price line](./img/rth-session-vwap-2.png)

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
- **Daily VWAP.** An always-on VWAP line anchored to the trading day (24h/Globex), drawn
  independently of the session VWAPs.
- **Per-session config blocks.** Each session (Asia, Europe, NY) is one block: a dropdown that sets
  the timezone **and** its hours, a per-session **Use custom hours** checkbox, and a custom from-to
  field applied only when that checkbox is on.
- **Per-session toggles.** Independent show switches for each session (Asia and Europe off,
  New York on by default).
- **Presets.** Line colors and width preset (Asia yellow, Europe cyan, NY green, Daily amber),
  labels off by default.
- **Clean legend.** All inputs and helper plots are hidden from the status line, so the chart
  legend stays uncluttered while the values remain in the Data Window and Inputs tab.

## Installation

Two ways to get it on your chart:

**A) From TradingView's Indicators library (easiest)**

1. Open TradingView, click **Indicators** in the top toolbar.
2. Search for **"RTH Session VWAP"** and add it to your chart.

**B) From source (this repo)**

1. Grab the Pine code &mdash; either copy the contents of
   [`rth-session-vwap.pine`](./rth-session-vwap.pine) directly, or clone the repo:
   ```
   git clone https://github.com/raoulsson/trading-view-script-rth-session-wvap.git
   ```
2. In TradingView, open **Pine Editor**, paste the code, and click **Add to chart**.
3. If you had a previous version loaded, open the indicator settings and use
   **Defaults → Reset settings** (or remove and re-add) so the presets take effect.

## Settings

<pre>
Sessions & timezone behaviour
─────────────────────────────
Each session (Asia, Europe, NY) is one block:
  • Zone & preset hours — a dropdown that sets the timezone AND default hours,
                          e.g. "America/New York (0930-1600, default)".
  • Use custom hours    — a checkbox; when on, the Custom hours field below
                          replaces the dropdown hours (read in the same zone).
  • Custom hours        — your own from-to session window.

Hours are read in the session's OWN market timezone, not your chart timezone —
they are passed explicitly to Pine's time():

    tNY = time(timeframe.period, NY_session_full + ':1234567', tzNY)

Consequences:
  • 0930-1600 always means New York local wall-clock — whether your chart is
    set to Manila, UTC, or anything else.
  • Daylight saving is handled automatically; you never re-enter hours in spring/fall.
  • Zones are a curated dropdown of real IANA names (e.g. Asia/Hong Kong,
    Europe/Berlin, America/Chicago). Only genuine IANA identifiers work — a
    non-IANA name such as "Europe/Frankfurt" (Germany is Europe/Berlin) errors,
    so pick from the list.

Defaults: Asia = Asia/Tokyo 0900-1500 · Europe = Europe/London 0800-1630 ·
          NY = America/New York 0930-1600 (RTH cash session).
</pre>

- **Display:** Daily VWAP, per-session VWAP toggles, per-session labels, per-session + Daily std-dev
  band toggles.
- **Asia / Europe / NY Session:** zone+hours dropdown, "Use custom hours" checkbox, custom hours field.
- **Calculation:** std-dev multipliers, VWAP source (applies to all VWAPs), label offset.

## Notes

- On futures, "24h/Daily" anchors to the exchange trading-day boundary (Globex reset), which is
  the standard daily VWAP anchor.
- Std-dev bands are off by default; enable them under Display.

## License

Mozilla Public License 2.0 — see [`LICENSE`](./LICENSE).
Original © TJ_667. Modifications © 2026 raoulsson.
