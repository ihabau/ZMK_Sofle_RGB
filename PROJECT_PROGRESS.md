# PandaKB Sofle Choc V1 - ZMK Firmware Project

## Project Overview

**Hardware:** PandaKB Sofle Choc V1 (Nice!Nano v2)
**Repository:** https://github.com/ihabau/ZMK_Sofle_RGB
**Last Updated:** 2026-07-12

---

## Goals

1. ✅ **4 Layers** - QWERTY, Lower, Raise, Adjust
2. ⚠️ **Per-Key RGB** - Individual LED per switch on pin D0 (P0.08) - `per-key-rgb` branch
3. ⚠️ **LCD on Both Sides** - Bongo cat, WPM, battery indicator, BLE/USB indicator, layer indicator - `main` branch
4. ✅ **GitHub Build Workflow** - Automated firmware builds

---

## Branch Strategy

**Important:** Per-key RGB (darknao/zmk fork) and nice_oled display module are **incompatible**. They modify different ZMK versions.

### Branch: `main`
- **ZMK:** zmkfirmware/zmk v0.3
- **Display:** nice_oled module (bongo cat, WPM, battery, BLE/USB, layer, modifiers, HID)
- **RGB:** Standard underglow (all 29 LEDs same color per layer)
- **Build:** Uses zmkfirmware reusable workflow

### Branch: `per-key-rgb`
- **ZMK:** darknao/zmk rgb-layer-25.08 (based on moergo-sc/zmk Glove80)
- **Display:** Basic ZMK display (text-based widgets, no bongo cat)
- **RGB:** Per-key RGB layer colors (29 LEDs, individually controlled)
- **Build:** Custom workflow with Zephyr SDK installation

---

## Per-Key RGB Colors (per-key-rgb branch)

| Layer | Color |
|-------|-------|
| Base (0) | RED |
| Lower (1) | GREEN |
| Raise (2) | BLUE |
| Adjust (3) | PURPLE |

To activate: cycle RGB effects with `RGB_EFF` key until "layer indicators" mode.

---

## Configuration Files (per-key-rgb branch)

### `config/west.yml`
- Uses `darknao/zmk` (rgb-layer-25.08 branch) for per-key RGB
- Removed `zmk-nice-oled` module (incompatible with darknao fork)

### `config/sofle.conf`
- Added `CONFIG_EXPERIMENTAL_RGB_LAYER=y`
- Added `CONFIG_ZMK_BEHAVIORS_QUEUE_SIZE=256`
- Uses basic ZMK display (no custom status screen)

### `config/sofle.keymap`
- Added `#include <dt-bindings/zmk/rgb_colors.h>`
- Added `underglow-layer` devicetree node with 29 LEDs per side
- Colors: RED (base), GREEN (lower), BLUE (raise), PURPLE (adjust)

### `config/boards/nice_nano_v2.overlay`
- SPI3 on P0.08, WS2812, chain-length = 29, GRB color order

---

## Build Status

| Branch | Build Workflow | Status |
|--------|---------------|--------|
| `main` | zmkfirmware reusable workflow | ❌ Failing - needs investigation |
| `per-key-rgb` | Custom workflow with Zephyr SDK | ❌ Failing - needs investigation |

---

## Known Issues

1. **Build failures:** Both branches failing to build. Need to check error logs.
2. **nice_oled vs per-key RGB:** Cannot use both simultaneously (different ZMK base versions).
3. **LED count:** 29 per side, 58 total. Underglow-layer bindings match chain-length of 29.

---

## Flashing Instructions

1. Go to GitHub Releases: https://github.com/ihabau/ZMK_Sofle_RGB/releases
2. Download the .uf2 files for your chosen branch
3. Flash settings_reset.uf2 first (double-click reset on Nice!Nano, drag to mass storage)
4. Then flash sofle_left.uf2 or sofle_right.uf2 to each half

---

## Resources

- [ZMK Documentation](https://zmk.dev/docs)
- [darknao/zmk Per-Key RGB](https://github.com/darknao/zmk/tree/rgb-layer-25.08)
- [nice_oled Module](https://github.com/mctechnology17/zmk-nice-oled)
- [Sofle Keyboard Build Guide](https://josefadamcik.github.io/SofleKeyboard/build_guide_choc.html)
