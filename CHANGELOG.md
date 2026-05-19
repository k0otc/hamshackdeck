# Changelog

All notable changes to HamShackDeck are documented in this file. The
format is loosely based on [Keep a Changelog](https://keepachangelog.com/),
and versions follow CalVer (`YY.M.patch.build`).

## [26.5.13] — 2026-05-19

### Fixed
- **TUNE button now actually triggers the tune carrier.** Long-standing
  bug, predates the rename — the action was sending the SmartSDR
  command `transmit set tune=1` (the settable-field form), which the
  radio silently rejects with `rc=NaN`. The correct command is the
  verb form `transmit tune on` / `transmit tune off`. These are two
  different surfaces in the SmartSDR TCP API: the radio publishes
  `tune=0/1` as a readable field in `STATUS transmit` frames, but
  flipping it requires the top-level `transmit tune <on|off>` verb,
  not the `transmit set <field>=<value>` form used for `rfpower`,
  `tunepower`, `dax`, etc. Fixed in both keypad modes (momentary and
  toggle) and the matching command-reference line in the README.

## [26.5.12] — 2026-05-19

### Fixed
- **Property Inspector changes now take effect immediately across all
  actions.** v26.5.11 fixed this for the Meter action. v26.5.12 rolls
  the same fix out to the other 12 actions that subscribe to live
  radio data: Antenna, Band, DAX TX, Filter, Frequency Dial, Mode,
  Noise Reduction, Profile Cycle, Smart Macro, TUNE, TX / MOX, and
  ON AIR Indicator. Changing host, slice, feature, mode, profile
  kind, etc. in the PI now tears down the old subscription and
  re-establishes the new one without the user having to remove and
  re-add the button.

### Changed
- Internal: added `src/flex/settings-watcher.ts`, a shared utility
  that gates `onDidReceiveSettings` → `onWillAppear` forwarding on
  user-visible field changes. Necessary because the Stream Deck SDK
  fires `onDidReceiveSettings` on every `getSettings()` call as well
  as on PI saves, and many actions call `getSettings()` from inside
  their status listeners. Without the gate, every status frame would
  trigger a teardown-and-rebuild.

## [26.5.11] — 2026-05-19

### Fixed
- **Changing the Meter dropdown in the Property Inspector now actually
  changes the meter.** Previously, picking a different meter (e.g.
  changing SWR to Reflected Power) saved the setting but the running
  action kept its old subscription and kept displaying the old meter
  — the title stayed on the old meter name, the value stayed on the
  old reading. The user had to remove and re-add the button to make
  it take effect. The Meter action now overrides `onDidReceiveSettings`
  to tear down its subscriptions and re-run setup whenever the PI
  saves new settings.

### Known issue / follow-up
- The same root cause affects every action that subscribes to live
  radio data (Antenna, Band, DAX TX, Filter, Frequency Dial, Mode,
  Noise Reduction, Profile Cycle, Smart Macro, TUNE, TX, TX Indicator).
  For now, changing those actions' configurable settings (host, slice,
  feature) after initial placement requires removing and re-adding
  the button. Symptoms are less visible because the things you'd
  reconfigure on those actions (host IP, slice) are typically set
  once. Will roll the same `onDidReceiveSettings` fix to those
  actions in a future release.

## [26.5.10] — 2026-05-19

### Fixed
- **Meter buttons could silently lose their meter selection.** A user
  who freshly placed a Meter action and picked SWR (or any meter)
  from the dropdown could find the button stuck on `Meter\n—` with
  nothing ever displayed, even during TX. This was the same
  `sdpi-select`-doesn't-persist trap that bit the Mode action in
  v26.5.1: Stream Deck's select element only writes the chosen value
  back to settings when the user explicitly clicks an option, so
  freshly-placed buttons could carry `meterName=undefined` and
  silently fail. Added `default="LEVEL"` to the Meter dropdown so
  newly-placed Meter buttons start as a useful S-meter the user can
  then tune to taste. Existing buttons with the broken state now show
  a clearer `set up` placeholder instead of `—` so the user knows
  what's needed.
- **Same persistence bug fixed preventively in six other dropdowns**
  across the rest of the plugin's Property Inspectors. Added explicit
  default values to: Band action (defaults to 20m), Frequency action's
  optional mode setter (defaults to "don't change"), Noise Reduction
  set-value (defaults to "on"), Profile type (defaults to "global"),
  and Voice Macro slice (defaults to "don't change"). No user-visible
  change for buttons that were already working — just inoculates
  fresh placements against the same trap.

## [26.5.9] — 2026-05-17

### Added
- **ON AIR Indicator can now display forward power.** A new "Show
  forward power" checkbox in the indicator's Property Inspector. When
  enabled and the radio is transmitting, the red button shows the
  live wattage (e.g. `95W`, `1.2kW`) instead of the on-air label. The
  blue (not-transmitting) face stays blank — no value, no label —
  consistent with the existing minimal RX presentation. Off by
  default, so existing buttons see no change unless the user opts in.
  Toggling the setting in the PI takes effect without reloading the
  action.

## [26.5.8] — 2026-05-17

### Changed
- **Meter titles on keys are now more compact.** v26.5.7 added a TX
  slice letter to global TX meters (Forward Power, Reflected Power,
  SWR, ALC, HW ALC) — but on key buttons, "Fwd Power · C" was long
  enough that the default Stream Deck title font wrapped to two lines
  and pushed the value text off the visible button area. Key titles
  now use shorter forms: `Fwd · A`, `Ref · A`, `SWR · A`, `ALC · A`,
  `HWALC · A`. Stream Deck+ dial titles keep the full form ("Fwd
  Power · A") since the LCD strip has more horizontal room.

## [26.5.7] — 2026-05-17

### Added
- **Meter action now shows the TX slice on global TX meters.** When the
  meter is set to Forward Power, Reflected Power, SWR, ALC, or HW ALC,
  the button title now appends the TX-slice letter (e.g. `Fwd Power · A`
  on a key, or "Fwd Power · A" on the Stream Deck+ LCD title). The
  letter reflects which slice is the designated TX slice (the one with
  `tx=1`), so when you're working split, swapping slices, or pivoting
  between voice/digital between QSOs, a glance at the meter button
  confirms which VFO is actually feeding the PA. Persists across the
  TX/RX boundary — the radio "remembers" the TX slice between
  transmissions, so the button does too.

### Changed
- The internal status listener signature now optionally receives the
  object id as a second argument (`Listener = (fields, id?) => void`).
  Backwards-compatible — existing listeners ignore the new arg. Used by
  future code that subscribes broadly (`status.subscribe(host, kind, "", ...)`)
  and needs to disambiguate frames within the kind.
- `STATUS slice <n>` log lines now include the `tx` field when the radio
  publishes it. Useful for debugging which slice is the TX slice without
  resorting to `S0|slice` raw frames.

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
