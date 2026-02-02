# Rei's Corne Keyboard ZMK Config

## Overview

This ZMK configuration is designed to maximise the use of home row keys for efficient
typing and navigation. It employs home row mods with a combo-based system for accessing
extended functionality.

- **Hardware:** Corne (5 columns, 36 keys) with nice!nano v2 controllers
- **Features:** Mouse emulation, home row mods, extensive combos, power management

## Layout

![Layout](./diagrams/corne.svg)

_Diagram automatically updates when keymap changes_

## Key Features

### Layer Structure

| Layer | Index | Access                         | Purpose                            |
| ----- | ----- | ------------------------------ | ---------------------------------- |
| BASE  | 0     | Default                        | QWERTY typing with home row mods   |
| EXT   | 1     | Hold V, M, or ` / thumb combos | Numbers, function keys, symbols    |
| NAV   | 2     | Hold D or K / thumb combos     | Arrow keys, mouse, page navigation |
| SYS   | 3     | Combo (middle thumbs)          | Bluetooth, media, brightness       |

### Home Row Mods

Standard hold-tap modifiers on the home row:

- **Left hand**: A=Shift, S=Ctrl, E=Alt, F=Cmd
- **Right hand**: J=Cmd, I=Alt, L=Ctrl, ;=Shift
- **Timing**: 200ms tapping term, 125ms prior idle, 175ms quick-tap

### Navigation Layer

- **Access**: Hold `D` or `K` keys / thumb combos (S+thumb, K+thumb)
- **Right hand**: Arrow keys, page up/down, home/end
- **Left hand**: Mouse movement and scroll control
- **Mouse buttons**: Left/right click on bottom row

### Extended Layer

- **Access**: Hold `V`, `M`, or `` ` `` keys / thumb combos (D+thumb, J+thumb)
- **Top row**: Function keys F1-F10
- **Home row**: Numbers 1-0 with home row mods
- **Thumb keys**: Parentheses `()` and brackets `[]`

### System Layer

- **Access**: Middle thumb combo (positions 31-34)
- **Bluetooth**: Profile selection (0-4), clear current, clear all (with Shift)
- **Media**: Previous/play-pause/next, volume up/down
- **Brightness**: Screen brightness controls

### Combo System

- **Timeout**: 35ms for responsive activation (70ms for layer combos via `COMBO_SLOW`)
- **Common keys**: TAB, BSPC, DEL, RET, ESC, SPACE — use `COMBO_FAST` (35ms prior-idle)
- **Brackets**: `()`, `[]` — use `COMBO` (70ms prior-idle)
- **Layer access**: EXT (D/J+thumb), NAV (S/K+thumb), SYS (middle thumbs) — use
  `COMBO_SLOW` (70ms timeout, 70ms prior-idle)
- **F11/F12**: On EXT layer only — use `COMBO_SLOW`
- **Active on**: BASE and EXT layers (F11/F12 on EXT only)

### Bluetooth Clear

Safe bluetooth clearing via hold-tap with mod-morph:

- **Hold 3 seconds**: Clear current profile
- **Hold 3 seconds + Shift**: Clear all profiles

## Power Management

- **Idle timeout**: 5 minutes before entering idle state
- **Deep sleep**: 10 minutes before entering deep sleep mode
- **Wake**: Any key press wakes keyboard from deep sleep

## Building & Flashing

### Automatic Build (Recommended)

1. Fork this repository
2. Enable GitHub Actions in your fork
3. Edit `config/corne.keymap`, `config/corne.conf`, or `build.yaml`
4. Commit and push changes
5. Download firmware `.uf2` files from Actions artifacts

### Flashing Firmware

1. Put keyboard half into bootloader mode (double-tap reset button)
2. Drag appropriate `.uf2` file to the mounted drive
3. Repeat for second half
4. Keyboards will restart with new firmware

### Creating a Release

1. Update `CHANGELOG.md` with version entry (e.g., `## [3.0.1] - 2025-12-24`)
2. Create and push a git tag:
   ```bash
   git tag 3.0.1
   git push origin 3.0.1
   ```
3. GitHub Actions will build firmware and create a release with `.uf2` files attached
