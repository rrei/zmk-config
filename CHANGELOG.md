# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.2.0] - 2026-01-02

### Changed

- Keymap: replace NUBS combos with bracket/parenthesis combos for programming
  - `(` on X+C (22-23), `)` on M+, (26-27)
  - `[` on C+V (23-24), `]` on N+M (25-26)
- Keymap: move EXT layer-taps from C/, to V/M keys; add EXT access via left thumb GRAVE
- Keymap: redesign thumb row modifiers (SHIFT/CMD/ALT repositioned)
- Keymap: replace tri-layer system with dedicated SYS combo on middle thumbs (31-34)
- Keymap: simplify EXT/NAV thumb rows to transparent (brackets now on BASE combos)

## [3.1.1] - 2025-12-26

### Changed

- Keymap: redesign combo macro with configurable timing parameters - replace
  `COMBO`/`NAMED_COMBO` with single parametrised `COMBO(NAME, KEY, LAYERS, POSITIONS,
  REQUIRE_PRIOR_IDLE, TIMEOUT)` macro
- Keymap: add timing constants `COMBO_TIMING_DEFAULT` (70ms prior-idle, 35ms timeout)
  and `COMBO_TIMING_SPACE` (35ms prior-idle for faster word separation during typing)
- Keymap: reduce default `require-prior-idle` from 100ms to 70ms

## [3.1.0] - 2025-12-24

### Changed

- Keymap: reorganise layer-taps to middle finger column - NAV on D/K (home row), EXT on
  C/, (bottom row), Alt HRM on E/I (top row)
- Keymap: simplify combo system - replace `FAST_COMBO`/`SLOW_COMBO` with unified `COMBO`
  macro (35ms timeout); remove slow thumb-key alternatives and layer activation combos
- Keymap: add NUBS combos (positions 22-23, 26-27) and F11/F12 combos on EXT layer
- Keymap: reduce HRM tapping-term from 400ms to 200ms for faster modifier access
- Keymap: add volume mute to SYS layer left inner thumb

## [3.0.1] - 2025-12-24

### Changed

- Keymap: move layer-taps from top row (E/R, U/I) to bottom row (C/V, M/,) to reduce
  interference with common typing patterns

### Fixed

- Build: fix double semicolons in macro definitions causing devicetree parse errors

## [3.0.0] - 2025-12-24

### Changed

- Keymap: major refactoring of layers, home row mods, and combos
  - Remove PREC (precision) layer; now 4 layers total (BASE, EXT, NAV, SYS)
  - Implement positional "timeless" home row mods (`&hl`, `&hr`) with opposite-hand
    activation via `hold-trigger-key-positions`
  - Add tri-layer system: EXT + NAV activates SYS via conditional layer
  - Replace tap-dance Bluetooth clear with hold-tap + mod-morph (3s hold to clear,
    hold with Shift to clear all)
  - Add DRY macros for combos (`FAST_COMBO`, `SLOW_COMBO`) and behaviours (`HRM`,
    `HOLD_TAP_OVERRIDES`)
  - Add slow thumb-key combo alternatives (70ms) alongside fast adjacent-key combos
    (35ms)
  - Add layer activation combos (`mo_nav_l/r`, `mo_ext_l/r`) for thumb-based access
  - Move parentheses and brackets to thumb keys on EXT/NAV layers
  - Home row mods timing: 400ms tapping term (up from 250ms) for reduced misfires
- Build: update board identifier from `nice_nano_v2` to `nice_nano` for ZMK Zephyr 4.1
  compatibility

### Fixed

- Build: restore `zmk,physical-layout` chosen property for Corne shield builds
- CI: extend build workflow paths filter to include `build.yaml`
- Keymap: correct right-hand slow combo key positions (use key 34 instead of 35)

### Documentation

- Update `README.md` with current layer structure, positional HRM, tri-layer system,
  and combo types
- Update `CLAUDE.md` with current architecture, DRY macros, timing parameters, and
  key position reference diagram

## [2.0.0] - 2025-12-06

### Changed

- Convert from 6-column to 5-column layout (36 keys)
  - Physical layout set to `foostan_corne_5col_layout`
  - All layers reduced from 42 to 36 keys
  - Combo key positions remapped for new numbering scheme
  - Documentation updated with 5-column key position reference

## [1.1.3] - 2025-08-13

### Changed

- Changelog: add missing entry for 1.1.2 and prepare upcoming release.

## [1.1.2] - 2025-08-12

### Changed

- CI: GitHub Actions workflows refined
  - Build: trigger only on main changes under `config/`, ignore tag refs; job renamed to
    `build-firmware`
  - Draw: trigger only on main changes under `config/`; job renamed to `draw-keymap`
  - Release: switch to tag/manual triggers and bundle firmware artifacts in release

## [1.1.1] - 2025-08-12

### Changed

- Release workflow improvements
  - Now triggers on successful build completion instead of tag push
  - Enhanced changelog extraction script to properly parse version sections
  - Added semantic version tag validation

## [1.1.0] - 2025-08-11

### Added

- Automated keymap diagram generation workflow via GitHub Actions
  - Automatically generates SVG diagrams on keymap changes
  - Commits updated diagrams with `[skip-build]` prefix
- Comprehensive documentation
  - CLAUDE.md with detailed codebase guidance for AI assistance
  - Improved README with architecture and configuration details
- Visual keymap representation
  - SVG diagram of current keyboard layout
  - YAML configuration for keymap-drawer

### Changed

- Keymap improvements
  - Refactored tap dance behaviours for better consistency
  - Improved combo system with better positioning and timing
  - Added symmetric SYS layer access from both keyboard halves
  - Reduced hold-tap `tapping-term-ms` from 280ms to 250ms for more responsive modifiers
- Combo adjustments
  - Re-added TAB combo for better accessibility
  - Moved ESC combo one position to the right
  - Made DEL combo easier to trigger
  - Tweaked combo keys and positions for better ergonomics

### Fixed

- Build workflow configuration to properly skip builds on diagram updates
- Combo triggering issues that interfered with normal typing

## [1.0.0] - Initial Release

Initial release of the ZMK configuration for Corne keyboard with nice_nano_v2.
