# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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