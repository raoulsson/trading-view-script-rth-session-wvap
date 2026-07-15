# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file **TradingView Pine Script v6** indicator (`rth-session-vwap.pine`) that plots
timezone-correct session VWAPs (Asia, London, New York) plus a continuous 24h/daily VWAP, with
optional standard-deviation bands. There is no build system, test suite, package manager, or CLI —
the code runs inside TradingView's Pine Editor. "Running" it means pasting the `.pine` file into
TradingView (Pine Editor → Add to chart). All editing/review happens here; validation happens on TV.

It is an MPL-2.0 derivative of "Koalafied VWAP Session/Day" by TJ_667. Preserve the license header
(lines 1–5) on any edit.

## Architecture

The script is one linear top-to-bottom program (Pine has no modules). Sections in order:

1. **Functions** — `computeVWAP` (used only by the daily VWAP), `compute_stDEV` (turns a vwap+stdev
   into ±1/±2 band values), `f_drawLabel` (draws a persistent right-edge label via `var label`).
2. **Inputs** — grouped `Display` / `Calc` / `Sessions`. Every input sets `display = display.none`
   and every plot sets `display = display.all - display.status_line` — this is deliberate, to keep
   the chart legend/status line uncluttered. Preserve these flags when adding inputs or plots.
3. **Session detection** — the core mechanism. Each session calls `time(timeframe.period,
   session + ':1234567', tz)` with an **explicit market timezone** (`tzAsia/tzLondon/tzNY`). This is
   the whole point of the fork: sessions are correct regardless of the chart's timezone, and DST is
   automatic. `time()` returns `int`-or-`na`; v6 removed implicit int→bool casting, so results are
   wrapped: `inX = not na(tX)` (in-session) and `newX = inX and na(tX[1])` (first bar = reset point).
4. **Per-session VWAP calc** — Asia/London/NY blocks are near-identical copy-paste. Each accumulates
   `p_X` (price·volume) and `vol_X` (volume), resetting to the current bar's values on `newX`,
   using Welford-style running variance (`Sn_X`) for the stdev. When editing one session, apply the
   same change to all three.
5. **Plots + std-dev bands** — session VWAPs and their bands. Visibility is gated by nested toggles
   (e.g. NY plots only when `showvS and showNY and inNY`).
6. **24h / Daily VWAP** — separate path using `computeVWAP` anchored to `ta.change(time("1D"))`
   (the exchange trading-day / Globex boundary), plus its own std-dev bands gated on `show24h`.
   Independent of the session VWAPs. `src` (the "VWAP Source" input) feeds both this and all three
   session VWAPs — keep them in sync if you change the source wiring.

## Editing conventions

- **Indentation is whitespace-sensitive and must be internally consistent per block.** Pine v6
  rejects a function body that mixes tabs and spaces. `computeVWAP` is tab-indented; `compute_stDEV`
  and `f_drawLabel` are 4-space-indented — that's fine (consistency is required *within* a block,
  not across functions). When editing a body, match the leading whitespace of that same body exactly.
- **Three parallel session blocks.** Asia/London/NY VWAP logic, plots, and label calls are triplicated.
  Changes to VWAP math or plot style almost always need to be made in all three.
- Session hours are typed in each session's own market timezone (NY default `0930-1600` = RTH cash open).
- **Keep string literals pure ASCII.** A non-ASCII char (em-dash `—`, smart quote, etc.) inside a
  `tooltip`/`title`/label string desyncs Pine's tokenizer and surfaces as a misleading
  `CE10015 Missing closing parenthesis` reported at the *end* of the file, not at the bad line.
  Non-ASCII in comments is tolerated (see the `©` header), but never put it in a string.
- **Timezone dropdown values must be real IANA canonical zone names.** The Sessions dropdowns encode
  the zone as `IANA/Zone (HHMM-HHMM[, default])`; `f_tz` extracts the zone and passes it to `time()`.
  An invented city that is not an IANA identifier (e.g. `Europe/Frankfurt` — Germany's zone is
  `Europe/Berlin`) causes a hard runtime error at the `time()` call. Only add zones that exist in the
  IANA tz database. Custom hours per session come from a `Use custom hours` checkbox + `input.session`
  field, applied via `useCustom<Session> ? custom<Session> : f_hours(sel<Session>)`.
- After changing input defaults/presets, note in the README that users must **Reset settings** on TV
  for new defaults to take effect (existing saved settings override code defaults).
