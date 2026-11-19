# Changelog

## 2026.11.19-2133

- Added the TradingView saved-script version (`28.0`) to the chart-visible short title.
- Confirmed the saved script's four main VWAP and label defaults are enabled. Existing chart
  instances can retain their saved input values after a source update; remove and re-add the
  indicator to initialize it with the current defaults.

## 2026.11.19-1235

- Set the default Asia session to 08:00–17:00 Asia/Tokyo, following the common NQ futures window
  from Globex reopen toward the London open. Updated the custom-hours default and described the
  one-hour manual adjustment for US daylight saving time.
- Enabled Daily, Asia, Europe, and New York VWAPs by default, along with each main VWAP value label.
  Standard-deviation bands remain off by default.
- Synced the Pine source with the current reset and plot-order fixes, including session occurrence
  resets on session-only charts and Daily VWAP initialization on the first loaded bar.
- Updated the README to describe the presets, visible defaults, and Asia-hours convention.
- TradingView's Pine validator accepts the source with no errors or warnings. The saved chart
  instance has not yet been visually checked after applying it.
