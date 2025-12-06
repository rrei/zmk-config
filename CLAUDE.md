# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration for a Corne (crkbd) 5-column split keyboard with nice_nano_v2 controllers. The configuration implements advanced features like mouse emulation, multi-layer typing with home row mods, and sophisticated power management.

## Build System

The project uses ZMK's standard build system with GitHub Actions. Building is typically done automatically via GitHub Actions, but local development is possible with the Zephyr west tool (though not commonly needed for keymap changes).

### GitHub Actions Build
- Firmware builds automatically on push/PR via `.github/workflows/build.yml`
- Uses `zmkfirmware/zmk` upstream workflow for building user configs
- Build targets defined in `build.yaml`: `nice_nano_v2` with `corne_left` and `corne_right` shields
- Firmware files (.uf2) are available as build artifacts
- Build workflow skips commits with `[skip-build]` prefix to avoid unnecessary builds

### Automated Diagram Generation
- Keymap diagrams automatically generated via `.github/workflows/draw-keymap.yml`
- Triggers on changes to `config/*.keymap` or `config/*.dtsi` files
- Uses `caksoylar/keymap-drawer` action to generate SVG layout diagrams
- Commits updated diagrams with `[skip-build]` prefix to prevent triggering firmware builds
- Maintains `layout.svg` file automatically in sync with keymap changes

## Architecture

### Configuration Files
- `config/corne.keymap`: Main keymap definition with layers, combos, behaviours, and key bindings
- `config/corne.conf`: ZMK configuration flags (mouse support, sleep settings, soft-off)
- `config/west.yml`: West workspace configuration pointing to ZMK main branch

### Layer Structure (6 layers total)
The keymap uses a sophisticated multi-layer system with consistent home row mods:

1. **BASE (0)**: Main typing layer with QWERTY layout and home row modifiers
2. **EXT (1)**: Extended layer with numbers, function keys, and symbols
3. **NAV (2)**: Navigation layer with arrow keys, page navigation, and mouse movement
4. **PREC (3)**: Precision layer (identical to NAV but with 1/3 speed mouse movement)
5. **SYS (4)**: System layer for Bluetooth management, media controls, and keyboard resets
6. **IDLE (5)**: Safety layer that disables all keys to prevent accidental input

### Key Behaviours
- **Home Row Mods**: Aggressive use of hold-tap behaviours on home row for modifiers
- **Combos**: Extensive combo system for common keys (TAB, SPACE, ENTER, BACKSPACE, etc.)
- **Tap Dance**: Multi-tap behaviours (e.g., Bluetooth clear sequences)
- **Layer Taps**: Keys that act as normal keys on tap, layer switches on hold

### Advanced Features
- **Mouse Emulation**: Full mouse control via pointing device support with configurable sensitivity
- **Precision Mode**: Reduced sensitivity mouse movement for fine control
- **Power Management**: Deep sleep after 10-15 minutes, soft-off mode for transport
- **Bluetooth Management**: Multi-profile support with profile switching and clearing

### Configuration Parameters
Key timing parameters are defined at the top of `corne.keymap`:
- `HOLD_TAP_TAPPING_TERM_MS`: 250ms for hold-tap recognition
- `COMBO_*_TIMEOUT_MS`: Fast (25ms) and slow (75ms) combo timeouts
- `SOFT_OFF_HOLD_TIME_MS`: 3 seconds to activate soft-off mode

## Development Workflow

### Making Keymap Changes
1. Edit `config/corne.keymap` for layout modifications
2. Edit `config/corne.conf` for feature flags or timing adjustments
3. Commit and push changes to trigger automatic build and diagram generation
4. Download firmware from GitHub Actions artifacts
5. Flash `.uf2` files to respective keyboard halves
6. Updated `layout.svg` diagram will be automatically committed if keymap changed

### Testing Changes
- Use `layout.svg` for visual reference of current keymap
- Test combos and layer switching thoroughly after firmware updates
- Verify Bluetooth connectivity and profile switching functionality

### Key Position Reference
The keymap uses 0-based indexing for key positions in a 3x5+3 layout:
```
 0  1  2  3  4       5  6  7  8  9
10 11 12 13 14      15 16 17 18 19
20 21 22 23 24      25 26 27 28 29
      30 31 32      33 34 35
```

## Important Implementation Details

### Mouse Movement Configuration
- Mouse movement speed controlled via `ZMK_POINTING_DEFAULT_MOVE_VAL` and `ZMK_POINTING_DEFAULT_SCRL_VAL`
- Precision layer uses `zip_xy_scaler` and `zip_scroll_scaler` with 1:3 ratio for fine control
- Movement values are defined before including ZMK headers

### Power Management
- Idle timeout: 5 minutes before entering idle state
- Sleep timeout: 10 minutes before deep sleep
- Soft-off requires 3-second hold and affects both halves simultaneously
- Keyboard wakes on any key press from deep sleep

### Combo System Design
- Combos use `require-prior-idle-ms` to prevent interference with normal typing
- Different timeout values for different combo types (fast vs slow)
- Combos are active on both BASE and EXT layers for consistency

This configuration represents a highly optimised setup for productive typing with minimal finger movement and maximum functionality in a compact 36-key layout.