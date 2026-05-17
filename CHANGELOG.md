# Changelog

All notable changes to HamShackDeck are documented in this file. The
format is loosely based on [Keep a Changelog](https://keepachangelog.com/),
and versions follow CalVer (`YY.M.patch.build`).

## [26.5.6] — 2026-05-17

### Fixed
- **`Invalid subscription object name` warnings removed.** The plugin
  was sending `sub profiles all` and `sub interlock all` on every
  connection, both of which the radio rejects (`profiles` and
  `interlock` are not subscribable objects per the SmartSDR TCP API
  spec). Status frames for those objects were arriving anyway via the
  implicit global-scope broadcast that `sub client all` establishes,
  so functionally nothing changes — but the radio no longer logs error
  responses for our subscriptions, and the plugin no longer wastes two
  command slots on every connect.

### Added
- **Property Inspector "What's New" footer.** Every PI now shows a
  small, muted footer link at the bottom: `HamShackDeck vXX.X.X —
  What's New`. Opens the CHANGELOG on GitHub in a new tab so users can
  see what shipped in the build they're running without leaving the
  Stream Deck app. Subtle styling so it doesn't compete with the
  user's actual settings.
- **Test Connection result glyph.** Successful tests now show a `✓`
  prefix, failures show `✗`. Keeps the result legible without relying
  purely on the green/red color difference — useful in dim Stream Deck
  PIs and for users with reduced color sensitivity.

### Changed
- The shared PI helper script (`ui/shared/radio-picker.js`) now also
  drives the What's New footer and is loaded by all 31 Property
  Inspectors, including the lone System Volume PI that previously
  didn't need it (System Volume is the only action that controls the
  computer rather than the radio, so it has no radio-host field — but
  it still gets the footer now).

## [26.5.5] — 2026-05-17

### Fixed
- ON AIR Indicator was emitting every interlock status frame twice
  (1 ms apart) because the status-dispatch loop in `status.ts` collapsed
  to a single key when the subscription `id` was empty (the case for
  `interlock`, `profiles`, and other global-scope frames). Listeners are
  now called exactly once per frame.

### Changed
- Removed the temporary `TxIndicator` diagnostic logging introduced in
  v26.5.3 / v26.5.4 (`state transition`, `setState/setTitle completed`,
  `willAppear`, etc.). The one-line `TxIndicator: ... interlock state=...`
  summary is retained because it remains useful for support and
  troubleshooting. The `ERROR:` branch in `refreshDisplay` is preserved
  so silent throws still surface in the log.

## [26.5.4] — 2026-05-17

### Changed
- **Slice picker consistency.** The remaining 7 actions that still used
  a free-form text field for slice selection were converted to the same
  A / B / C / D dropdown used by Noise Reduction. All 15 slice-aware
  Property Inspectors now use the identical dropdown — no more
  textbox-vs-dropdown mix.

### Added
- Extended diagnostic logging in `TxIndicatorAction` (kept for one build
  to investigate the ON AIR Indicator regression):
  `state transition <prev> -> <new>`, `refreshDisplay ... setState(...)
  title=...`, and `setState/setTitle completed` / `ERROR:` lines. Removed
  again in v26.5.5.

## [26.5.3] — 2026-05-17

### Added
- Initial diagnostic logging inside `TxIndicatorAction.subscribe()` and
  `refreshDisplay()` to investigate a regression where the ON AIR
  Indicator button didn't visually turn red on PTT after the rename.

## [26.5.2] — 2026-05-17

### Fixed
- **Mode button self-heal.** Mirror fix in `ModeAction.onWillAppear` for
  any deck where a Mode button was placed before v26.5.1: if settings
  still contain `mode=undefined`, fall back to `USB` at runtime so the
  button works without forcing the user to re-open the Property
  Inspector.

## [26.5.1] — 2026-05-17

### Fixed
- **Fresh Mode buttons showed yellow-triangle errors.** Stream Deck's
  `sdpi-select` element only persists a value once the user explicitly
  clicks an option, so newly-placed Mode buttons carried
  `mode=undefined` and threw on press. Added `default="USB"` to the
  Mode Property Inspector dropdown so the default is written out the
  moment the button is created.

## [26.5.0] — 2026-05-17

### Changed
- **Renamed from FlexDeck to HamShackDeck** at FlexRadio's request.
  Affected areas:
  - Plugin UUID changed from `com.flex.streamdeck` to
    `com.k0otc.hamshackdeck`.
  - Plugin display name, marketing copy, README, getting-started guide,
    helper scripts, and GitHub repo all updated.
  - SmartSDR client identifier (`client gui_client_id ...`) updated to
    identify as HamShackDeck.
  - A migration playbook (`MIGRATION.md`) was added so existing FlexDeck
    users can install side-by-side, verify, then uninstall the old
    plugin.
- "FlexRadio", "SmartSDR", and the FLEX-6400 / 6500 / 6600 / 6700 /
  8400 / 8600 model names are preserved throughout — only the
  third-party plugin name was changed.

### Notes
- Because the plugin UUID changed, Stream Deck treats HamShackDeck as
  an entirely new plugin. Existing FlexDeck buttons remain on the deck
  under the old plugin and have to be re-created using HamShackDeck
  actions. The two plugins can coexist during migration.
