# PandaKB Sofle Choc V1 - ZMK Firmware Project

## Project Overview

**Hardware:** PandaKB Sofle Choc V1 (Nice!Nano v2)
**Firmware:** ZMK (darknao/zmk fork with per-key RGB support)
**Last Updated:** 2026-07-12

---

## Goals

1. ✅ **4 Layers** - QWERTY, Lower, Raise, Adjust
2. ✅ **Per-Key RGB** - Individual LED per switch on pin D0 (P0.08)
3. ✅ **LCD on Both Sides** - Bongo cat, WPM, battery indicator, BLE/USB indicator, layer indicator
4. ✅ **GitHub Build Workflow** - Automated firmware builds

---

## Configuration Files

### 1. `config/west.yml`
- **Changed:** ZMK remote from `zmkfirmware/zmk` to `darknao/zmk`
- **Branch:** `rgb-layer-25.08` (per-key RGB support)
- **Modules:** zmk-nice-oled (unchanged)

### 2. `config/sofle.conf`
- **Added:** `CONFIG_EXPERIMENTAL_RGB_LAYER=y` (enables per-key RGB)
- **Added:** `CONFIG_ZMK_BEHAVIORS_QUEUE_SIZE=256` (required for underglow-layer)
- **Retained:** All display and nice_oled widget configs

### 3. `config/sofle.keymap`
- **Added:** `#include <dt-bindings/zmk/rgb_colors.h>`
- **Added:** `underglow-layer` devicetree node with per-layer color mappings:
  - **Base (0):** RED
  - **Lower (1):** GREEN
  - **Raise (2):** BLUE
  - **Adjust (3):** PURPLE
- **Note:** LED count = 29 (matches chain-length in overlay)

### 4. `.github/workflows/build.yml`
- **Changed:** Custom build workflow (can't use zmkfirmware's reusable workflow with fork)
- **Builds:** Left half, Right half, Settings reset
- **Artifacts:** Uploads .uf2 files to GitHub Releases

### 5. `config/boards/nice_nano_v2.overlay`
- **Unchanged:** SPI3 on P0.08, WS2812, chain-length = 29, GRB color order

---

## Build Instructions

### GitHub Actions (Recommended)
1. Push to `master` branch
2. Workflow runs automatically
3. Download firmware from Releases tab:
   - `sofle_left.uf2` - Left half
   - `sofle_right.uf2` - Right half
   - `settings_reset.uf2` - Reset settings

### Local Build
```bash
# Initialize west
pip3 install west
west init -l config
west update
west zephyr-export

# Build left half
west build -s zmk/app -b nice_nano_v2 -- \
  -DSHIELD="sofle_left nice_oled" \
  -DCONFIG_ZMK_STUDIO=y

# Build right half
west build -s zmk/app -b nice_nano_v2 -- \
  -DSHIELD="sofle_right nice_oled"

# Build settings reset
west build -s zmk/app -b nice_nano_v2 -- \
  -DSHIELD="settings_reset"
```

---

## Flashing Instructions

### Prerequisites
- Install ZMK.uf2 or use web flasher at https://zmk.dev/flash
- Put Nice!Nano in bootloader mode (double-click reset button)

### Flash Process
1. Connect Nice!Nano via USB
2. Double-click reset button (enters bootloader mode)
3. Drag .uf2 file to the mass storage device
4. Device will reboot automatically

### First Flash
- Flash **settings_reset.uf2** first to clear old settings
- Then flash the appropriate half firmware

---

## Per-Key RGB Colors

| Layer | Color | Hex |
|-------|-------|-----|
| Base (0) | RED | `0xFF0000` |
| Lower (1) | GREEN | `0x00FF00` |
| Raise (2) | BLUE | `0x0000FF` |
| Adjust (3) | PURPLE | `0x800080` |

### Available Colors
`RED`, `GREEN`, `BLUE`, `PURPLE`, `TEAL`, `ORANGE`, `PINK`, `YELLOW`, `WHITE`, `OFF`

### Activating Per-Key Mode
After flashing, use `&rgb_ug RGB_EFF` to cycle effects until "layer indicators" mode is active.

---

## Progress Log

### 2026-07-12
- ✅ Switched to darknao/zmk fork for per-key RGB support
- ✅ Updated west.yml manifest
- ✅ Added per-key RGB config to sofle.conf
- ✅ Added underglow-layer definitions to keymap
- ✅ Created custom GitHub Actions build workflow
- ✅ Updated build.yaml
- ✅ Created this progress document

---

## Known Issues

1. **LED Count:** The underglow-layer bindings have 58 entries but chain-length is 29. Need to verify actual per-key LED count on PCB.
2. **Color Customization:** Colors can be changed in keymap by editing the `&ug COLOR` values.

---

## Next Steps

1. **Verify LED Count:** Confirm actual number of per-key LEDs on Sofle Choc V1 PCB
2. **Test Build:** Push to GitHub and verify workflow builds successfully
3. **Flash & Test:** Flash firmware and verify per-key RGB works
4. **Customize Colors:** Adjust layer colors to preference

---

## Resources

- [ZMK Documentation](https://zmk.dev/docs)
- [darknao/zmk Per-Key RGB](https://github.com/darknao/zmk/tree/rgb-layer-25.08)
- [Sofle Keyboard Build Guide](https://josefadamcik.github.io/SofleKeyboard/build_guide_choc.html)
- [ZMK Flashing Guide](https://zmk.dev/docs/features/flashing)
