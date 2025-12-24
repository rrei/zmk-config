# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in
this repository.

## Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration for a Corne (crkbd)
5-column split keyboard with nice!nano v2 controllers. The configuration implements
advanced features like mouse emulation, positional "timeless" home row mods, a tri-layer
system, and power management.

## Build System

The project uses ZMK's standard build system with GitHub Actions. Building is typically
done automatically via GitHub Actions, but local development is possible with the Zephyr
west tool (though not commonly needed for keymap changes).

### GitHub Actions Build

- Firmware builds automatically on push/PR via `.github/workflows/build.yml`
- Uses `zmkfirmware/zmk` upstream workflow for building user configs
- Build targets defined in `build.yaml`: `nice_nano` board with `corne_left` and
  `corne_right` shields
- Firmware files (.uf2) are available as build artifacts
- Build workflow triggers on changes to `config/*` or `build.yaml`
- Build workflow skips commits with `[skip-build]` prefix to avoid unnecessary builds

### Automated Diagram Generation

- Keymap diagrams automatically generated via `.github/workflows/draw.yml`
- Triggers on changes to `config/*.keymap` or `config/*.dtsi` files
- Uses `caksoylar/keymap-drawer` action to generate SVG layout diagrams
- Skips running on commits containing `[skip-draw]`
- Commits updated diagrams with `[skip-build]` prefix to prevent triggering firmware
  builds
- Maintains `diagrams/corne.svg` file automatically in sync with keymap changes

### Versioning

- Version tracked in `CHANGELOG.md` and via git tags only
- No separate version file (no `pyproject.toml`, `package.json`, or similar)
- Release workflow extracts version from git tags and parses changelog for release notes

## Architecture

### Configuration Files

- `config/corne.keymap`: Main keymap definition with layers, combos, behaviours, and key
  bindings
- `config/corne.conf`: ZMK configuration flags (mouse support, sleep settings, soft-off)
- `config/west.yml`: West workspace configuration pointing to ZMK main branch
- `build.yaml`: GitHub Actions build matrix (board and shield combinations)

### Layer Structure (4 layers)

The keymap uses a tri-layer system with positional home row mods:

1. **BASE (0)**: Main typing layer with QWERTY layout and positional home row modifiers
2. **EXT (1)**: Extended layer with numbers (home row), function keys (top row), and
   symbols
3. **NAV (2)**: Navigation layer with arrow keys, page navigation, and mouse movement
4. **SYS (3)**: System layer for Bluetooth management, media controls, and brightness
   (accessed via tri-layer: EXT + NAV)

### Key Behaviours

- **Positional Home Row Mods (`&hl`, `&hr`)**: "Timeless" hold-tap behaviours with
  `hold-trigger-key-positions` set to opposite-hand keys only. This prevents accidental
  modifier activation when typing quickly.
- **Combos**: Extensive combo system with fast (35ms) and slow (70ms) variants for common
  keys and layer activation
- **Hold-Tap + Mod-Morph**: Bluetooth clear uses a 3-second hold-tap combined with
  mod-morph (shift for clear-all)
- **Layer Taps**: Keys that act as normal keys on tap, layer switches on hold (E/I for
  NAV, R/U for EXT)
- **Conditional Layer**: Tri-layer system where activating both EXT and NAV enables SYS

### DRY Macros

The keymap uses preprocessor macros to reduce repetition:

- `COMBO(NAME, BINDINGS, POSITIONS, TIMEOUT)`: Parametrised combo definition
- `FAST_COMBO(N, B, P)`: 35ms timeout combo for adjacent keys
- `SLOW_COMBO(N, B, P)`: 70ms timeout combo for thumb key combinations
- `HRM(NAME, HOLD_TRIGGER_KEYS)`: Positional home row mod behaviour definition
- `HOLD_TAP_OVERRIDES(NODE)`: Consistent timing overrides for `&mt` and `&lt`

### Advanced Features

- **Mouse Emulation**: Full mouse control via pointing device support with configurable
  sensitivity
- **Power Management**: Deep sleep after 10 minutes idle
- **Bluetooth Management**: Multi-profile support with profile switching and safe
  clearing

### Configuration Parameters

Key timing parameters defined in `corne.keymap`:

- Home row mods (`&hl`, `&hr`): 400ms tapping term, 100ms prior idle, 300ms quick-tap
- Standard hold-taps (`&mt`, `&lt`): 200ms tapping term, 100ms prior idle, 300ms quick-tap
- Fast combos: 35ms timeout, 100ms prior idle
- Slow combos: 70ms timeout, 100ms prior idle
- Mouse movement: `ZMK_POINTING_DEFAULT_MOVE_VAL = 1800`
- Scroll speed: `ZMK_POINTING_DEFAULT_SCRL_VAL = 22`

## Development Workflow

### Making Keymap Changes

1. Edit `config/corne.keymap` for layout modifications
2. Edit `config/corne.conf` for feature flags or timing adjustments
3. Edit `build.yaml` if changing board or shield configuration
4. Commit and push changes to trigger automatic build and diagram generation
5. Download firmware from GitHub Actions artifacts
6. Flash `.uf2` files to respective keyboard halves
7. Updated `diagrams/corne.svg` diagram will be automatically committed if keymap changed

### Testing Changes

- Use `diagrams/corne.svg` for visual reference of current keymap
- Test combos and layer switching thoroughly after firmware updates
- Verify Bluetooth connectivity and profile switching functionality

### Debugging Devicetree Errors

Use `cpp -E` to preview how C preprocessor macros expand before devicetree compilation:

```bash
# Show full preprocessed output
cpp -E config/corne.keymap

# Filter to specific section (e.g., combos)
cpp -E config/corne.keymap 2>/dev/null | sed -n '/combos {/,/^    };$/p'

# Check specific macro expansion
cpp -E config/corne.keymap 2>/dev/null | grep "kp_tab_fast"
```

This is useful for debugging devicetree parse errors, as ZMK's error column numbers refer
to the **post-expansion** output, not the original source file.

**Note**: Local `cpp` won't have ZMK's include paths, so ZMK-specific headers (like
`dt-bindings/zmk/keys.h`) won't expand. But it's still useful for checking custom macro
definitions like COMBO, HRM, etc.

### Key Position Reference

The keymap uses 0-based indexing for key positions in a 3x5+3 layout. Note that keys 30
and 35 (outer thumb keys) are physically covered and mapped to `&none`:

```
┌────┬────┬────┬────┬────┐                 ┌────┬────┬────┬────┬────┐
│  0 │  1 │  2 │  3 │  4 │                 │  5 │  6 │  7 │  8 │  9 │
├────┼────┼────┼────┼────┤                 ├────┼────┼────┼────┼────┤
│ 10 │ 11 │ 12 │ 13 │ 14 │                 │ 15 │ 16 │ 17 │ 18 │ 19 │
├────┼────┼────┼────┼────┤                 ├────┼────┼────┼────┼────┤
│ 20 │ 21 │ 22 │ 23 │ 24 │                 │ 25 │ 26 │ 27 │ 28 │ 29 │
└────┴────┴────┼────┼────┼────┐       ┌────┼────┼────┼────┴────┴────┘
               │ 30 │ 31 │ 32 │       │ 33 │ 34 │ 35 │
               └────┴────┴────┘       └────┴────┴────┘
```

Key position groups for positional hold-tap:

- `LEFT_HALF_KEYS`: 0-4, 10-14, 20-24, 30-32
- `RIGHT_HALF_KEYS`: 5-9, 15-19, 25-29, 33-35

## Important Implementation Details

### Mouse Movement Configuration

- Mouse movement speed controlled via `ZMK_POINTING_DEFAULT_MOVE_VAL` (1800) and
  `ZMK_POINTING_DEFAULT_SCRL_VAL` (22)
- Movement values are defined before including ZMK headers

### Power Management

- Idle timeout: 5 minutes before entering idle state
- Sleep timeout: 10 minutes before deep sleep
- Keyboard wakes on any key press from deep sleep

### Combo System Design

- Fast combos (35ms) for adjacent finger keys to avoid typing interference
- Slow combos (70ms) for thumb key combinations allowing more relaxed timing
- All combos use `require-prior-idle-ms = 100` to prevent interference with normal typing
- Combos are active on both BASE and EXT layers for consistency
- `slow-release` enabled for better modifier interaction

### Tri-Layer System

The SYS layer is accessed via conditional layer:

```
if-layers = <EXT NAV>;
then-layer = <SYS>;
```

This means pressing both layer keys (e.g., R+E or holding both thumb combos) activates
the system layer automatically.

This configuration represents a highly optimised setup for productive typing with
minimal finger movement and maximum functionality in a compact 36-key layout.
