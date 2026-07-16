# PandaKB Sofle Choc V1 - ZMK Firmware Project

## Project Overview

**Hardware:** PandaKB Sofle Choc V1 (Nice!Nano v2)
**Firmware:** ZMK v0.3.0 (zmkfirmware/zmk)
**Git User:** ihab Al Ubaidi / www.ihabau@gmail.com
**Repository:** https://github.com/ihabau/ZMK_Sofle_RGB
**Last Updated:** 2026-07-16

---

## Goals

1. ✅ **4 Layers** - BASE, LOWER, RAISE, ADJUST (conditional layer)
2. ✅ **RGB Underglow** - WS2812 on pin D0 (P0.08), 29 LEDs per side, GRB color order
3. ✅ **OLED Display** - nice_oled on both halves (bongo cat, WPM, battery, BLE/USB, layer)
4. ✅ **GitHub Actions CI/CD** - Automated firmware builds with release artifacts
5. ✅ **Mouse Emulation** - Right encoder scroll via `&msc` behavior
6. ✅ **Encoder Controls** - Left encoder: volume, Right encoder: mouse scroll, Right click: RGB toggle

---

## Configuration Files

### 1. `config/west.yml`
- **ZMK remote:** `zmkfirmware/zmk` at tag `v0.3.0`
- **Module:** `mctechnology17/zmk-nice-oled` (main branch)
- **Note:** NOT darknao fork (incompatible with Sofle shield)

### 2. `config/sofle.conf`
- `CONFIG_EC11=y` + `CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y` (encoder)
- `CONFIG_ZMK_RGB_UNDERGLOW=y` (WS2812 underglow)
- `CONFIG_ZMK_DISPLAY=y` + `CONFIG_ZMK_DISPLAY_STATUS_SCREEN_CUSTOM=y` (OLED)
- `CONFIG_NICE_OLED_WIDGET_HID_INDICATORS=y` + `CONFIG_ZMK_HID_INDICATORS=y` (CapsLock on OLED, USB only)
- `CONFIG_ZMK_POINTING=y` (mouse emulation for encoder scroll)
- `CONFIG_ZMK_STUDIO_LOCKING=n`

### 3. `config/sofle.keymap`
- Custom `scroll_encoder` behavior: `zmk,behavior-sensor-rotate` wrapping `&msc SCRL_DOWN`/`SCRL_UP`
- `tap-ms = <250>` on scroll encoder (prevents too-short triggers)
- `#define ZMK_POINTING_DEFAULT_SCRL_VAL 10` placed BEFORE `#include pointing.h` (scroll speed)
- Conditional layer: ADJUST activated when LOWER + RAISE held simultaneously
- Left encoder: volume (`&inc_dec_kp C_VOL_DN C_VOL_UP`)
- Right encoder: mouse scroll (`&scroll_encoder`)
- Left center thumb: Mute (`&kp C_MUTE`)
- Right center thumb: RGB Toggle (`&rgb_ug RGB_TOG`)
- LOWER key: tap=Backspace, hold=LOWER layer (`&lt LOWER BSPC`)

### 4. `.github/workflows/build.yml`
- Triggers on push to `main` branch
- Uses `zmkfirmware/zmk/.github/workflows/build-user-config.yml@v0.3`
- Creates GitHub Release with `.uf2` firmware files

### 5. `build.yaml`
- Left half: `nice_nano_v2` + `sofle_left nice_oled` + Studio
- Right half: `nice_nano_v2` + `sofle_right nice_oled`
- Settings reset: `nice_nano_v2` + `settings_reset`

### 6. `config/boards/nice_nano_v2.overlay`
- SPI3 on P0.08 (WS2812 data pin)
- chain-length = 29, GRB color mapping

---

## Keymap Layout (4 Layers)

### BASE (Layer 0)
| Row 0 | GRAVE | 1 | 2 | 3 | 4 | 5 | | 6 | 7 | 8 | 9 | 0 | DELETE |
| Row 1 | ESC | Q | W | E | R | T | | Y | U | I | O | P | BSPC |
| Row 2 | TAB | A | S | D | F | G | | H | J | K | L | SEMI | SQT |
| Row 3 | SHIFT | Z | X | C | V | B | **MUTE** | **RGB TOG** | N | M | COMMA | DOT | FSLH | SHIFT |
| Row 4 | | GUI | ALT | CTRL | LOWER/BSPC | ENTER | | SPACE | RAISE | CTRL | ALT | GUI |

### LOWER (Layer 1) - Numbers, F-keys, symbols
- Row 0: F1-F11
- Row 1: GRAVE, 1-0, F12
- Row 2: shifted symbols (!, @, #, $, %, ^, &, *, (, ), PIPE)
- Row 3: =, -, +, [, ], ;, :, \

### RAISE (Layer 2) - BT, navigation, clipboard
- Row 0: BT_CLR, BT1-BT5
- Row 1: INS, PSCRN, GUI, PG_UP
- Row 2: ALT, CTRL, SHIFT, PG_DN, arrows, DEL, BSPC
- Row 3: UNDO, CUT, COPY, PASTE

### ADJUST (Layer 3) - RGB controls (LOWER + RAISE)
- Row 0: EFF, EFR, SPI, SPD, TOG, EP_TOG
- Row 1: HUI, SAI, BRI (increase hue/sat/brightness)
- Row 2: HUD, SAD, BRD (decrease hue/sat/brightness)

---

## Build Instructions

### GitHub Actions (Recommended)
1. Push to `main` branch
2. Workflow runs automatically
3. Download firmware from Releases tab:
   - `sofle_left.uf2` - Left half
   - `sofle_right.uf2` - Right half
   - `settings_reset.uf2` - Reset settings

### Flashing
1. Double-click Nice!Nano reset button (enters bootloader mode)
2. Drag `.uf2` file to mass storage device
3. Device reboots automatically
4. **After enabling `CONFIG_ZMK_POINTING=y`:** Forget + re-pair Bluetooth (HID descriptor changed)

---

## Git Remote Configuration

```
origin  -> https://github.com/ergomechstore/sofle-v2-zmk.git (upstream, rarely used)
myrepo  -> https://github.com/ihabau/ZMK_Sofle_RGB.git (primary, push target)
```

---

## Progress Log

### 2026-07-12
- ✅ Created repository from ergomechstore/sofle-v2-zmk template
- ✅ Configured 4-layer keymap (BASE, LOWER, RAISE, ADJUST)
- ✅ Set up nice_oled module for OLED displays
- ✅ Enabled HID indicators (CapsLock on OLED, USB only)
- ✅ Created GitHub Actions build workflow
- ✅ Reversed volume encoder direction
- ✅ Redesigned ADJUST layer with RGB controls

### 2026-07-13
- ✅ Moved RGB toggle from key to ADJUST layer
- ✅ Changed LOWER key to layer-tap (tap=BSPC, hold=LOWER)
- ✅ Left center thumb = Mute, Right center thumb = RGB Toggle
- ✅ Regenerated keymap SVG via keymap-drawer
- ✅ Updated README with 4-layer layout, OLED info, flashing instructions

### 2026-07-14
- ✅ Enabled mouse emulation (`CONFIG_ZMK_POINTING=y`)
- ✅ Added custom `scroll_encoder` behavior with `&msc` (mouse scroll)
- ✅ Added `tap-ms = <250>` to prevent too-short encoder triggers
- ✅ Added `#define ZMK_POINTING_DEFAULT_SCRL_VAL 140` for scroll speed
- ✅ **Fixed critical bug:** Moved `#define` BEFORE `#include pointing.h` (macros were using default=10)
- ✅ Changed right encoder click to Enter, then back to RGB Toggle
- ✅ Committed and pushed: `1f648bf`, `81aab7b`, `7772667`

### 2026-07-16
- ✅ Fixed right encoder hardware (ground and diode weren't connected)
- ✅ Confirmed both scroll and click work after hardware fix
- ✅ Tested scroll speed values: 140 (too fast), 2 (too weak/didn't register), 10 (ZMK default, working)
- ✅ Final scroll speed set to 10 (ZMK default)
- ✅ Right encoder click confirmed working as RGB Toggle

---

## Known Issues & Limitations

1. **Per-key RGB:** Abandoned — darknao/zmk fork incompatible with Sofle shield, ZMK v0.3.0 has no per-key RGB support
2. **Static color presets:** ZMK has no behavior to set a specific RGB color (only incremental HUI/HUD, SAI/SAD, BRI/BRD)
3. **HID indicators (CapsLock):** Only work over USB, not BLE — ZMK firmware limitation
4. **BLE profiles 2/3 switching:** May require re-pairing (forgets and re-connects fix it)
5. **Mouse emulation BLE:** After enabling `CONFIG_ZMK_POINTING=y`, must re-pair Bluetooth (HID descriptor changes)

---

## Resources

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK v0.3.0](https://github.com/zmkfirmware/zmk/tree/v0.3.0)
- [mctechnology17/zmk-nice-oled](https://github.com/mctechnology17/zmk-nice-oled)
- [ZMK Pointing/Mouse](https://zmk.dev/docs/features/pointing)
- [ZMK Sensor Bindings](https://zmk.dev/docs/config/keymap#sensor-bindings)
- [GitHub Issue #72: Sensor rotate tap-ms](https://github.com/zmkfirmware/zmk/issues/72)
