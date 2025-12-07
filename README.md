# Rei's Corne Keyboard ZMK Config

## Overview

This ZMK configuration is designed to maximise the use of home row keys for efficient
typing and navigation. It employs a hybrid approach between pure home row mods and
dedicated navigation layers.

- **Hardware:** Corne (5 columns, 36 keys) with nice!nano v2 controllers
- **Features:** Mouse emulation, home row mods, extensive combos, power management

## Layout

![Layout](./diagrams/corne.svg)

_Diagram automatically updates when keymap changes_

## Key Features

### Home Row Mods (tweaked)

- **Modifiers**: `ASEF` and `;LIJ` -> `Shift/Control/Option/Command`
- **Navigation Layer**: heavily used functions accessible from `D/K`, "pushing" `Option`
  to the top row (`E/I`)
- **Timing**: 250ms hold-tap timing with 100ms prior idle requirement to prevent false
  positives during normal typing

### Navigation Layer

- **Access**: Hold `D` or `K` keys from the base layer
- **Right Hand**: Arrow keys, page up/down, home/end
- **Left Hand**: Mouse movement and scroll control
- **Design**: Both hands can activate layer for versatile usage patterns
- **Mouse Control**: Full mouse functionality available from left hand only

### Precision Layer

- **Access**: Hold `Z` or `,` keys from base layer
- **Function**: Identical to navigation layer with 1/3 speed mouse movement
- **Purpose**: Fine-grained control for precise cursor positioning tasks

### System Layer

- **Access**: Hold `X` or `.` keys from base layer
- **Bluetooth**: Profile switching and clearing (tap-dance sequences)
- **Keyboard**: Reset halves, soft-off mode (3-second hold)
- **Media**: Volume, brightness, and playback controls

### Idle Layer

- **Access**: Combo through system layer (`G` + `H`)
- **Purpose**: Disables all keys for safe keyboard transport
- **Exit**: Same combo or power cycle keyboard

### Combos

- **Commonly used keys**: tab, space, enter, backspace, delete, esc and parentheses
- **Convenient access**: all combos use index and middle fingers, and are at most one
  key away from resting position
- **Timing**: 100ms prior idle and short 25ms timeout to prevent typing interference

## Power Management

- **Idle timeout**: 5 minutes before entering idle state
- **Deep sleep**: 10 minutes before entering deep sleep mode
- **Wake**: Any key press wakes keyboard from deep sleep
- **Soft-off**: Manual transport mode via 3-second system layer hold

## Building & Flashing

### Automatic Build (Recommended)

1. Fork this repository
2. Enable GitHub Actions in your fork
3. Edit `config/corne.keymap` or `config/corne.conf`
4. Commit and push changes
5. Download firmware `.uf2` files from Actions artifacts

### Flashing Firmware

1. Put keyboard half into bootloader mode (double-tap reset button)
2. Drag appropriate `.uf2` file to the mounted drive
3. Repeat for second half
4. Keyboards will restart with new firmware
