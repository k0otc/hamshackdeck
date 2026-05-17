# HamShackDeck — Getting Started Guide

**HamShackDeck** is a Stream Deck plugin that turns your Stream Deck or
Stream Deck+ into a control surface for the FlexRadio FLEX 6000 or
8000 series SDR. Tune frequencies, switch slices, adjust noise
reduction, toggle
DAX, run profiles, and more — all from the buttons and dials in front
of you.

This guide walks you through the first 30 minutes: what you need, how
to install, and how to configure your first half-dozen useful buttons.

> **Audience:** This guide assumes you have a FlexRadio FLEX 6000 or
> 8000 series rig connected to your network, and you're running
> SmartSDR or AetherSDR somewhere on the same LAN. It does **not**
> assume you've used a Stream Deck before.

---

## Contents

1. [What you need](#what-you-need)
2. [Stream Deck basics in 60 seconds](#stream-deck-basics-in-60-seconds)
3. [Installing HamShackDeck](#installing-hamshackdeck)
4. [The one setting every action needs: Radio Host](#the-one-setting-every-action-needs-radio-host)
5. [Slices: a quick primer](#slices-a-quick-primer)
6. [Your first five buttons (key actions)](#your-first-five-buttons-key-actions)
7. [Your first five dials (Stream Deck+ only)](#your-first-five-dials-stream-deck-only)
8. [The full action catalog](#the-full-action-catalog)
9. [Pages, profiles, and getting fancy](#pages-profiles-and-getting-fancy)
10. [Troubleshooting](#troubleshooting)
11. [Frequently asked questions](#frequently-asked-questions)

---

## What you need

| Item | Required | Notes |
|---|---|---|
| FlexRadio FLEX 6000 or 8000 series radio | ✅ | FLEX-6400 / 6500 / 6600 / 6700 / 8400 / 8600 / 8000 |
| Radio on your network | ✅ | Wired Ethernet recommended |
| SmartSDR v4.x firmware on the radio | ✅ | Verified on v4.2.18 |
| A Windows 11 PC | ✅ | macOS support is not yet tested |
| Stream Deck hardware | ✅ | Stream Deck Mini/MK.2/XL/+/Plus all work |
| Stream Deck software v6.9 or newer | ✅ | Download from elgato.com |

> **Stream Deck+ specifically** unlocks the four dial encoders and the
> LCD touchscreen. Several HamShackDeck actions (frequency tuning, audio
> levels, meters) shine here — but every action also works as a key
> button on a regular Stream Deck.

---

## Stream Deck basics in 60 seconds

If you've never used a Stream Deck:

- A **Stream Deck** is a small USB device with 6, 15, or 32 LCD buttons
  arranged in a grid. Each button can be programmed to do something
  when pressed.
- A **Stream Deck+** also has 4 rotary dials (encoders) along the
  bottom and an LCD strip above them. Dials can be rotated, pressed
  (push-click), and touched (tap on the LCD).
- The **Stream Deck app** on your computer is where you configure
  what each button or dial does. Open it, drag actions from the right
  panel onto buttons, then configure each action's settings.
- **Pages**: each Stream Deck can hold many "pages" of buttons. Use a
  page-navigation button to switch between them. We'll come back to
  this in [Pages, profiles, and getting fancy](#pages-profiles-and-getting-fancy).

If you're new, the Stream Deck app's built-in tutorial (Help → Tutorial)
covers the basics in five minutes.

---

## Installing HamShackDeck

1. **Quit the Stream Deck app** if it's running. (Right-click its tray
   icon → Quit.)
2. **Download** the latest `hamshackdeck-vXX.X.X.zip` from the
   [Releases page](../../releases).
3. **Extract the zip** (right-click → Extract All). You'll get a single
   file: `com.k0otc.hamshackdeck.streamDeckPlugin`.
4. **Double-click** the extracted file. Stream Deck will prompt to
   install it — click **Install**.
5. **Launch the Stream Deck app.** HamShackDeck's actions now appear in
   the right-hand panel under the **HamShackDeck** category.

You can verify the install by checking the log at:

```
C:\Users\<YourName>\hamshackdeck.log
```

The first line should say something like:

```
=== HamShackDeck plugin process started (v26.5.0.0) ===
```

That's the plugin's startup heartbeat — if you ever wonder whether the
plugin is alive and which version is running, this file is the place
to look.

---

## The one setting every action needs: Radio Host

Every HamShackDeck action talks to your radio over your LAN. To do that,
it needs to know which radio to talk to. HamShackDeck makes this easy in
two ways:

### Auto-discovery (the easy way)

Every action's properties panel has a **Discovered radios** dropdown
above the Radio host field. Within a second or two of opening the
properties, HamShackDeck listens for the discovery broadcasts every
FlexRadio sends on your LAN and populates the dropdown with whatever
it finds. Pick your radio from the list and the IP fills in
automatically.

This works alongside SmartSDR, AetherSDR, FRStack, SliceMaster, and
any other discovery-listening tools — HamShackDeck shares the discovery
port instead of claiming it exclusively.

If your radio doesn't appear, click **Rescan** to retry. If it still
doesn't show up, the radio is probably on a different subnet (e.g.
across a VPN or router); type the IP manually in the field below.

### Finding your radio's IP (if needed)

If auto-discovery can't see your radio:

- **SmartSDR**: top-left menu → Radio → Radio Info → IP address
- **AetherSDR**: top bar shows the radio in the format `Name (IP)`
- **Router**: log into your router's DHCP client list and find the
  device named something like `FlexRadio-8600-xxxxx`

A typical IP looks like `192.168.1.50` or `192.168.8.86` — four
numbers separated by dots. Type it into the **Radio host** field —
no port number, no "http://", just the IP.

### Test Connection

Below the Radio host field, every action's properties panel has a
**Test connection** button. Click it to verify HamShackDeck can actually
reach the radio. A successful test shows a green message like
`Connected to FLEX-8600 (API v1.x.x.x)` within a second; a failure
shows a red message with a useful hint ("Host unreachable",
"Connection refused", "Could not resolve").

Use this any time a button isn't working as expected. It eliminates
"is it the IP or is it the action?" as a question.

### What if my radio is offline?

If the radio is unreachable, buttons will flash a red ❌ on press
instead of working. Dials simply won't update. Bring the radio online
and HamShackDeck will reconnect automatically — no need to restart the
plugin.

---

## Slices: a quick primer

A **slice** is one of the radio's independent receivers. SmartSDR
labels them **A**, **B**, **C**, **D** internally numbered **0**, **1**,
**2**, **3**. Each slice has its own frequency, mode, filters, and
noise reduction settings.

When a HamShackDeck action says **"Slice"**, that's the slice number it
acts on. Most users will pick:

- **A (slice 0)** — your main receive slice. The one you do most
  things with.
- **B (slice 1)** — secondary slice, often used for spotting or
  monitoring a calling frequency.

If you only run one slice, leave it at **A (slice 0)** everywhere.

> **TX slice vs. active slice:** Only one slice can be your *transmit*
> slice at a time — that's the one the radio actually transmits on
> when you key MOX. Slice **A** is also the *active* slice (the one
> SmartSDR's UI focuses on). These can be different! Many HamShackDeck
> actions act on whichever slice you specifically tell them to,
> not on "the active one."

---

## Your first five buttons (key actions)

Here are five buttons that will be useful no matter how you operate.
For each one, drag the named action onto a Stream Deck button and
fill in the settings shown.

### 1. Tune to a favorite frequency

**Action:** **Frequency**
**Use:** A one-press shortcut to jump to a specific frequency and mode
(e.g. 14.074 MHz USB for FT8, or your local repeater's input frequency).

**Settings:**
- Radio host: `192.168.1.50` *(your radio's IP)*
- Slice: `A (slice 0)`
- Frequency: `14.074`
- Mode: `DIGU` *(or LSB/USB/CW/AM/FM as needed)*
- Filter: *leave blank or set if you want a specific filter width*

Press the button — slice A jumps to 14.074 MHz DIGU.

### 2. Toggle a band

**Action:** **Band**
**Use:** One-press band-switch on the active slice (e.g. jump to 40m).

**Settings:**
- Radio host: *your IP*
- Slice: `A (slice 0)`
- Band: `40m`

Repeated presses on the same Band button can be configured to cycle
through your favorite frequencies on that band. See the action's
property panel for details.

### 3. PTT / MOX

**Action:** **TX / MOX**
**Use:** Engage transmit. Hold the button = transmit; release = receive.
Or, configure it as a tap-to-toggle if you prefer.

**Settings:**
- Radio host: *your IP*
- Mode: `Hold (press-to-talk)` *or* `Toggle`

Use with caution — your radio will key up the antenna the instant you
press this. Make sure your antenna is connected and your output power
is sane.

### 4. TUNE

**Action:** **TUNE**
**Use:** Engages the radio's built-in tune carrier at the configured
tune power (set in SmartSDR). Useful for tuning an external amp or
verifying RF output.

**Settings:**
- Radio host: *your IP*

Press once to start tuning, press again to stop. The button glows
amber while tuning is active.

### 5. DAX TX (the busy-fix button)

**Action:** **DAX TX**
**Use:** Toggle the radio's DAX transmit audio stream. The killer
feature: in **Kick mode**, this button **cycles DAX TX off-then-on**
to recover from the dreaded `BUSY` indicator in the DAX Control Panel.

**Settings:**
- Radio host: *your IP*
- Mode: `Kick` *(recommended)*

Tap once when DAX is acting up — the button briefly flashes off, then
back on, and your digital-mode audio is flowing again.

---

## Your first five dials (Stream Deck+ only)

If you have a Stream Deck+, drop these onto the four dial slots for an
instant rig front-panel.

### 1. Frequency tuning dial

**Action:** **Frequency Dial**
**Use:** Rotate to tune the slice up or down. Press the dial to cycle
the tuning step (1 kHz / 100 Hz / 10 Hz / 1 Hz). Touch the LCD to lock.

**Settings:**
- Radio host: *your IP*
- Slice: `A (slice 0)`
- Step: *let it default — you can change it with a press*

This is the dial that turns your Stream Deck+ into a poor man's K3
front panel.

### 2. AF Gain (volume)

**Action:** **AF**
**Use:** Rotate to adjust the slice's audio (receive) volume. Press
to mute.

**Settings:**
- Radio host: *your IP*
- Slice: `A (slice 0)`
- Step: `2` *(percent per tick)*

The LCD shows the AF level numerically and as a bar. When the level
hits zero — whether you turned it there or muted the slice — the
text turns red and a mute icon appears. The same red-at-zero
treatment applies to Headphone, Sidetone, Mic Gain, and System
Volume, so "no audio" is always visually obvious at a glance.

### 3. Filter bandwidth dial

**Action:** **Filter**
**Use:** **Rotate** to widen or narrow the slice's receive filter
in 50 Hz steps, symmetric around its current center. **Press** to
snap to the next preset for the current mode (CW: 500 / 250 / 150 /
100 / 50 Hz; USB: 2400 / 2700 / 2900 Hz; etc).

**Settings:**
- Radio host: *your IP*
- Slice: `A (slice 0)`
- Leave the low/high fields blank to use the preset cycle

The LCD shows the current bandwidth (e.g. `2.4 kHz` or `250 Hz`).
If you're chasing DX, narrow the filter on the fly; press to snap
back to a standard width when you're done. Same dial works for SSB,
CW, AM, FM, RTTY, DIGU/DIGL — the presets are mode-aware.

### 4. Noise Reduction (NR)

**Action:** **Noise Reduction**
**Use:** **Rotate** to adjust NR level 0–100. **Press** to toggle NR
on/off. (Also works for NB and ANF.)

**Settings:**
- Radio host: *your IP*
- Slice: `A (slice 0)`
- Feature: `NR — Noise Reduction`
- Behavior: `Toggle one feature`

> **Note about advanced denoisers:** The newer denoisers (NRS, NRL,
> RNN, ANFL, ANFT, NRF) are **binary on/off only** at the radio's
> public API on current firmware. Press toggles them; rotating does
> nothing. If FlexRadio adds level adjustment to a future firmware,
> we'll add it back.

### 5. System Volume

**Action:** **System Volume**
**Use:** Adjust your computer's Windows volume — *not* the radio's AF.
Useful for muting Discord/Teams notifications without leaving the rig.

**Settings:**
- Audio device: pick from the dropdown (or leave on "Windows default
  device" to track whatever Windows has set as default)
- Step per dial tick: `2`
- Touch preset (optional): `30` *(touching the LCD snaps to 30%)*

The dropdown lists every active output device on your PC, with the
current Windows default marked. Pick **Edifier Speakers** to control
just those speakers, **Headphones** to control just those, etc. The
**Rescan devices** button refreshes the list (handy after plugging in
a new USB headset).

---

## The full action catalog

Every common operation, one tap away. Here are HamShackDeck's actions
grouped by what they do.

### Frequency, mode, and band

| Action | Key | Dial | What it does |
|---|---|---|---|
| **Frequency** | ✅ | — | Jump to a specific frequency/mode |
| **Band** | ✅ | ✅ | Switch slice to a band (dial scrubs, press commits) |
| **Mode** | ✅ | ✅ | Change slice's mode (dial scrubs, press commits) |
| **Filter** | ✅ | ✅ | Adjust receive filter bandwidth; dial widens/narrows |
| **Frequency Dial** | — | ✅ | Rotate to tune, press to change step |
| **Memory** | ✅ | — | Save/recall a frequency snapshot |

### Slices, RIT/XIT, and antennas

| Action | Key | Dial | What it does |
|---|---|---|---|
| **Antenna** | ✅ | ✅ | Pick the slice's RX, TX, or paired antenna |
| **RIT** | ✅ | ✅ | Receive Incremental Tuning |
| **XIT** | ✅ | ✅ | Transmit Incremental Tuning |

### Transmit

| Action | Key | Dial | What it does |
|---|---|---|---|
| **TX / MOX** | ✅ | ✅ | Hold-to-talk or tap-to-toggle PTT |
| **TUNE** | ✅ | ✅ | Engage tuning carrier |
| **RF Power** | ✅ | ✅ | Set RF output power |
| **Tune Power** | ✅ | ✅ | Set tune-carrier power |
| **Mic Gain** | ✅ | ✅ | Set microphone gain |
| **DAX TX** | ✅ | ✅ | Toggle DAX TX (with BUSY-fix kick mode) |

### Audio

| Action | Key | Dial | What it does |
|---|---|---|---|
| **AF** | ✅ | ✅ | Slice AF (audio) gain |
| **AGC** | ✅ | ✅ | AGC mode + threshold |
| **Sidetone** | ✅ | ✅ | CW sidetone level |
| **Headphone** | ✅ | ✅ | Headphone output level |
| **System Volume** | ✅ | ✅ | Windows system playback volume |

### Noise mitigation

| Action | Key | Dial | What it does |
|---|---|---|---|
| **Noise Reduction** | ✅ | ✅ | NR/NB/ANF (level adj) + NRS/NRL/RNN/etc (toggle) |

### Profiles, macros, scripting

| Action | Key | Dial | What it does |
|---|---|---|---|
| **Profile** | ✅ | — | Load a specific named profile |
| **Profile Cycle** | ✅ | — | Cycle through a list of profiles |
| **Voice Macro** | ✅ | — | Play a DVK voice memory |
| **CW Macro** | ✅ | — | Send a CW macro (CWX) |
| **CW Speed** | ✅ | ✅ | Adjust CW speed |
| **Macro** | ✅ | — | Send any free-form radio command |
| **Smart Macro** | ✅ | — | Multi-step macros with conditions |
| **Raw Command** | ✅ | — | Send a literal TCP command for advanced users |

### Display / meters

| Action | Key | Dial | What it does |
|---|---|---|---|
| **Meter** | ✅ | ✅ | Display S-meter / SWR / forward power / etc. |
| **ON AIR Indicator** | ✅ | — | Lights when transmitting |

---

## Pages, profiles, and getting fancy

Once you have your basic layout working, Stream Deck's organizational
features let you scale up.

### Pages

Stream Deck supports multiple pages of buttons. Add a button using the
built-in **Switch Page** action (under System in the action panel) to
navigate. A common layout:

- **Page 1**: HF SSB — frequency presets, mic gain, NR
- **Page 2**: HF Digital — FT8/FT4 frequencies, DAX TX kick, RF power
- **Page 3**: CW — CW macros, CW speed dial, sidetone
- **Page 4**: Contest — band buttons, memory recalls, voice macros

**Profile Cycle and pages: HamShackDeck handles cross-page sync.** If you
have a Profile Cycle button on page 1 and another on page 2, pressing
either one updates both — they share state. You don't have to worry
about them getting out of sync.

### Stream Deck profiles

Stream Deck itself has its own concept of "profiles" (separate from
SmartSDR profiles). You can have one profile for **everyday operating**
and switch to a **contest profile** with different buttons for, say,
the November Sweepstakes weekend. Set this up via the Stream Deck app's
Profile menu.

### SmartSDR profiles vs. Stream Deck profiles

These are different things and easy to confuse:

| | SmartSDR profile | Stream Deck profile |
|---|---|---|
| Where it lives | On the radio | On your computer |
| What it controls | Radio settings (audio routes, TX settings, etc.) | Which buttons appear on your Stream Deck |
| HamShackDeck action that uses it | Profile, Profile Cycle | n/a |
| Triggered by | Pressing a HamShackDeck Profile button | Switching profiles in Stream Deck app |

Use HamShackDeck's **Profile Cycle** action to switch SmartSDR profiles
(e.g. "Default" ↔ "AMP" ↔ "Headphones-only"). Use the Stream Deck
app's profile picker to switch the overall button layout.

---

## Troubleshooting

### Buttons don't do anything

1. **Click the Test connection button** in the action's properties.
   This is the fastest way to confirm whether the radio is reachable.
   Green message: radio is fine, look elsewhere. Red message: the IP
   or network is the problem — the message will tell you which.
2. Check `C:\Users\<YourName>\hamshackdeck.log`. The first line tells you
   the plugin version.
3. Verify the IP address in the action's properties matches the radio.
   The **Discovered radios** dropdown is the easiest way — pick your
   radio from it.
4. Confirm SmartSDR/AetherSDR can reach the radio. If they can't, neither
   can HamShackDeck.
5. Try opening port 4992 in Windows Firewall if you have a strict setup.

### Dial LCD shows no value

- Make sure you've **picked a Feature** (for NR/NB/etc) or **picked
  a Meter** (for the Meter action) in the action's properties. If
  these are left blank, the LCD will show "Pick feature" or be blank.
- Some meters (SWR, Forward Power, ALC) only have values during TX.
  At idle they show "—" or "TX only" — that's normal.

### NRS / NRL / RNN dial rotation does nothing

That's expected. The advanced denoisers are binary-only at the radio
API on current firmware. Press still works (toggles on/off).

### "DAX is BUSY" in the DAX Control Panel

Press your **DAX TX** button (configured in Kick mode). It cycles
DAX off-then-on, which clears the BUSY state.

### Profile Cycle shows wrong profile after page switch

This was fixed in v26.5.2. If you see this on an older version,
upgrade.

### How do I uninstall?

1. Quit the Stream Deck app.
2. Use the included **wipe-hamshackdeck.cmd** helper, OR manually delete
   `%APPDATA%\Elgato\StreamDeck\Plugins\com.k0otc.hamshackdeck.sdPlugin`.
3. Relaunch Stream Deck.

---

## Frequently asked questions

### Does HamShackDeck work with non-Flex radios?

Not currently. HamShackDeck speaks FlexRadio's native TCP API directly,
which gives us per-slice control, live meters, and everything else
FlexRadio-specific. Support for generic CAT radios via Hamlib has
been discussed but isn't built yet — it would be a significant subset
of features.

### Does it work over the internet (multiFLEX, SmartLink)?

Tested mostly on LAN. SmartLink will likely work over the same TCP
session SmartSDR uses, but isn't a tested configuration. multiFLEX
bind setups are handled (HamShackDeck mirrors writes to the local cache
so multiple Stream Decks stay in sync even when echo messages don't
arrive).

### Can multiple Stream Decks all control the same radio?

Yes. They all talk to the same radio independently. State sharing
between them happens at the radio level, so they'll generally stay
coordinated.

### Does it work with the Stream Deck Mobile / Mobile XL apps?

The plugin only runs where the Stream Deck *software* runs — Windows
and (eventually) macOS. The mobile apps mirror that hardware-attached
desktop, so technically yes, but HamShackDeck has only been tested with
physical Stream Deck units on Windows.

### How do I get help / report bugs?

1. Capture `C:\Users\<YourName>\hamshackdeck.log` — that's the most useful
   thing to share when reporting a problem.
2. Note your HamShackDeck version (first line of the log), SmartSDR
   firmware version, and what you were doing when the issue happened.
3. *(Link to your issue tracker / forum / email here.)*

---

## Acknowledgments

HamShackDeck is developed by **K0OTC** and built on the official
[Elgato Stream Deck SDK](https://docs.elgato.com/streamdeck/sdk/).

Tested primarily on a FLEX-8600 running SmartSDR firmware 4.2.18, with
Stream Deck and Stream Deck+ hardware on Windows 11.

73, and enjoy.

---

*HamShackDeck is not affiliated with or endorsed by FlexRadio Systems or
Elgato. FlexRadio, FLEX, SmartSDR, and DAX are trademarks of
FlexRadio Systems. Stream Deck is a trademark of Corsair Memory, Inc.*
