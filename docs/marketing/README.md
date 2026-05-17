# HamShackDeck

**A Stream Deck plugin for the FlexRadio FLEX 6000 or 8000 series SDR.**

Turn your Elgato Stream Deck or Stream Deck+ into a control surface for
your FLEX. Tune frequencies, switch slices, adjust noise reduction,
toggle DAX, run profiles, and more — all from physical buttons and
dials sitting in front of you.

![HamShackDeck](docs/marketing/02-thumbnail.png)

> **Note**: HamShackDeck was previously known as FlexDeck. The project was
> renamed in v27.0.0 at FlexRadio's request to remove "Flex" from the
> product name. If you were using FlexDeck, see the [v27.0.0 release notes](../../releases/tag/v27.0.0)
> for migration steps — you'll need to install the new plugin and rebuild
> your decks (the internal UUID changed too, so the old buttons won't
> auto-migrate).

---

## Features

- **Every common radio operation, one tap away** — frequency, band,
  mode, filter, antenna, slices, transmit, audio, noise reduction,
  profiles, macros, and live meters
- **Auto-discovery** — HamShackDeck listens for your radio's broadcasts
  and pre-populates a "pick your radio" dropdown in every action.
  No IP address required.
- **Test Connection button** — every action's properties panel has
  a one-click probe that tells you whether the radio is reachable
  and reports its model and firmware version
- **Stream Deck+ ready** — rotary dials with LCD feedback for tuning,
  audio levels, NR adjustment, filter width, antenna selection, and more
- **Preview-then-commit dials** — for Band, Mode, Filter, and Antenna,
  rotate to preview your choice (LCD turns amber) and press to commit.
  No relay-click chatter mid-QSO, no accidental band-jumps.
- **Live status sync** — buttons reflect the radio's current state
  even when other clients (SmartSDR, AetherSDR) change it
- **Cross-page sync** — Profile Cycle and other stateful actions stay
  consistent across Stream Deck pages
- **Red-at-zero audio** — AF, Headphone, Sidetone, and Mic Gain dials
  turn red when their level hits 0. No more wondering why nobody's
  hearing you.
- **DAX TX BUSY recovery** — one-press kick to clear stuck DAX streams
- **System Volume control** — adjust your computer's Windows volume
  with a dial, with per-device targeting

See [the full action catalog in the Getting Started guide](HamShackDeck-Getting-Started.md#the-full-action-catalog).

---

## Gallery

![Every common operation, one tap away](docs/marketing/03-gallery-action-grid.png)

![Live Stream Deck+ feedback](docs/marketing/04-gallery-lcd-strip.png)

![Built for serious operators](docs/marketing/05-gallery-features.png)

![Tune from the dial, lock on the touch](docs/marketing/06-gallery-spectrum.png)

![Find your radio automatically](docs/marketing/07-gallery-discovery.png)

![Preview, then commit](docs/marketing/08-gallery-preview-dials.png)

![Live status across every page](docs/marketing/09-gallery-live-status.png)

---

## Requirements

- FlexRadio FLEX 6000 or 8000 series radio (FLEX-6400 / 6500 / 6600 / 6700 / 8400 / 8600 / 8000) on your LAN
- SmartSDR firmware v4.x (tested on v4.2.18)
- Windows 11
- Elgato Stream Deck software v6.9 or newer
- Any Stream Deck hardware (Mini / MK.2 / XL / + / Plus)

---

## Quick start

1. **Download** the latest `hamshackdeck-vXX.X.X.zip` from the
   [Releases page](../../releases).
2. **Extract** the zip — you'll get one file:
   `com.k0otc.hamshackdeck.streamDeckPlugin`.
3. **Double-click** the extracted file. Stream Deck will install it.
4. **Open** the Stream Deck app — HamShackDeck's actions appear in the
   right panel under "HamShackDeck".
5. **Drag** an action onto a button or dial slot.
6. **Pick your radio** from the Discovered radios dropdown in the
   action's properties — no need to type an IP. Click **Test
   connection** to confirm it's working.

The full **[Getting Started guide](HamShackDeck-Getting-Started.md)** walks
you through your first five buttons, your first five dials, and all
the configuration details.

---

## Building from source

If you want to build HamShackDeck yourself rather than use the released
installer:

```bash
# Prerequisites: Node.js 20+ and the Elgato Stream Deck CLI
# https://nodejs.org
# npm install -g @elgato/cli

git clone https://github.com/<your-username>/hamshackdeck.git
cd hamshackdeck
npm install
npm run build
streamdeck pack com.k0otc.hamshackdeck.sdPlugin --output dist --force
```

The built plugin will be at `dist/com.k0otc.hamshackdeck.streamDeckPlugin`.
Double-click to install.

---

## Reporting bugs

Please open a [GitHub issue](../../issues/new) and include:

- Your HamShackDeck version (first line of `C:\Users\<You>\hamshackdeck.log`)
- Your SmartSDR firmware version
- What you were doing when the bug happened
- The contents of `hamshackdeck.log` (sanitize your radio's IP if you'd
  prefer; it's just for your own debugging)

---

## License

HamShackDeck is released under the [MIT License](LICENSE). You're free to
use, modify, redistribute, and sell it — just keep the copyright notice.

---

## Acknowledgments

Built on the official [Elgato Stream Deck SDK](https://docs.elgato.com/streamdeck/sdk/).

Tested on a FLEX-8600 running SmartSDR firmware 4.2.18, with Stream
Deck and Stream Deck+ hardware on Windows 11.

**73**, and enjoy.

---

*HamShackDeck is not affiliated with or endorsed by FlexRadio Systems or
Elgato. FlexRadio, FLEX, SmartSDR, and DAX are trademarks of FlexRadio
Systems. Stream Deck is a trademark of Corsair Memory, Inc.*
