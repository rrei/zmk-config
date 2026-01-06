# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in
this repository.

## Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration for a Corne (crkbd)
5-column split keyboard with nice!nano v2 controllers. The configuration implements
advanced features like mouse emulation, positional "timeless" home row mods, combo-based
system layer access, and power management.

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
- Uses semantic versioning (e.g., `3.0.1`)

### Release Process

To create a release:

1. Ensure `CHANGELOG.md` has an entry for the version (e.g., `## [3.0.1] - 2025-12-24`)
2. Create and push a git tag matching the version:
   ```bash
   git tag 3.0.1
   git push origin 3.0.1
   ```
3. The release workflow (`.github/workflows/release.yml`) automatically:
   - Builds firmware using ZMK's upstream workflow
   - Extracts release notes from `CHANGELOG.md` for the tagged version
   - Creates a GitHub release with firmware `.uf2` files attached

The workflow can also be triggered manually via `workflow_dispatch` if needed.

## Architecture

### Configuration Files

- `config/corne.keymap`: Main keymap definition with layers, combos, behaviours, and key
  bindings
- `config/corne.conf`: ZMK configuration flags (mouse support, sleep settings, soft-off)
- `config/west.yml`: West workspace configuration pointing to ZMK main branch
- `build.yaml`: GitHub Actions build matrix (board and shield combinations)

### Layer Structure (4 layers)

The keymap uses positional home row mods with combo-based system layer access:

1. **BASE (0)**: Main typing layer with QWERTY layout and positional home row modifiers
2. **EXT (1)**: Extended layer with numbers (home row), function keys (top row), and
   symbols
3. **NAV (2)**: Navigation layer with arrow keys, page navigation, and mouse movement
4. **SYS (3)**: System layer for Bluetooth management, media controls, and brightness
   (accessed via thumb combo: middle thumbs together)

### Key Behaviours

- **Positional Home Row Mods (`&hl`, `&hr`)**: "Timeless" hold-tap behaviours with
  `hold-trigger-key-positions` set to opposite-hand keys only. This prevents accidental
  modifier activation when typing quickly.
- **Combos**: Combo system with configurable timing (35ms timeout, 70ms prior-idle by
  default; 35ms prior-idle for SPACE) for common keys like Tab, Backspace, Enter,
  Escape, Space, brackets/parentheses, F11/F12, and SYS layer access
- **Hold-Tap + Mod-Morph**: Bluetooth clear uses a 3-second hold-tap combined with
  mod-morph (shift for clear-all)
- **Layer Taps**: Keys that act as normal keys on tap, layer switches on hold (D/K for
  NAV, V/M and MINUS for EXT)

### DRY Macros

The keymap uses preprocessor macros to reduce repetition:

- `COMBO(NAME, KEY, LAYERS, POSITIONS, REQUIRE_PRIOR_IDLE, TIMEOUT)`: Base combo macro
- `COMBO_DEFAULT(NAME, KEY, LAYERS, POSITIONS)`: Standard timing (70ms prior-idle, 35ms
  timeout)
- `COMBO_FAST(NAME, KEY, LAYERS, POSITIONS)`: Fast timing for SPACE (35ms prior-idle,
  35ms timeout)
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

- Home row mods (`&hl`, `&hr`): 200ms tapping term, 100ms prior idle, 300ms quick-tap
- Standard hold-taps (`&mt`, `&lt`): 200ms tapping term, 100ms prior idle, 300ms
  quick-tap
- Combos: 35ms timeout, 70ms prior idle (35ms for SPACE)
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
7. Updated `diagrams/corne.svg` diagram will be automatically committed if keymap
   changed

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
cpp -E config/corne.keymap 2>/dev/null | grep "kp_tab"
```

This is useful for debugging devicetree parse errors, as ZMK's error column numbers
refer to the **post-expansion** output, not the original source file.

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

- All combos use 35ms timeout for responsive activation
- Default `require-prior-idle-ms = 70` prevents interference with normal typing
- SPACE combo uses `require-prior-idle-ms = 35` for faster word separation during typing
- Combos active on BASE and EXT layers (some EXT-only for F11/F12)
- `slow-release` enabled for better modifier interaction
- Bracket/parenthesis combos on bottom row for programming:
  - `(` on X+C (22-23), `)` on M+, (26-27)
  - `[` on C+V (23-24), `]` on N+M (25-26)
- SYS layer combo on middle thumbs (31-34) with 100ms timing

### SYS Layer Access

The SYS layer is accessed via a dedicated combo on the middle thumb keys (positions 31
and 34):

```
mo_sys {
    key-positions = <31 34>;
    layers = <BASE>;
    timeout-ms = <100>;
    require-prior-idle-ms = <100>;
};
```

This provides direct access to the system layer without requiring simultaneous layer key
holds, simplifying the mental model for layer navigation.

This configuration represents a highly optimised setup for productive typing with
minimal finger movement and maximum functionality in a compact 36-key layout.
