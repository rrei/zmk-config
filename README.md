# Rei's Corne Keyboard ZMK Config

## Overview
This ZMK configuration is designed to maximize the use of home row keys for efficient typing and navigation. It employs a hybrid approach between pure home row mods and dedicated navigation layers.

## Key Features

### Home Row Mods
- Implemented in both base layer and extended layer
- Extended layer includes numbers and function keys

### Navigation Layer
- Easily triggered for frequent use
- Right hand: arrow keys, page up/down, home/end
- Left hand: mouse movement control
- Can be triggered with either hand
- Designed to allow right hand to activate layer while left hand controls mouse
- Full mouse control possible with left hand only

### Precision Layer
- Identical to navigation layer but with reduced speeds
- Mouse movement and scroll speeds reduced to 1/3
- Enables finer control for precise tasks

### System Layer
- Bluetooth profile management
  - Switch between profiles
  - Clear profiles
- Keyboard management
  - Reset each keyboard half
  - Enter soft off mode (needs to be pressed for 3s)
- Media controls
  - Volume adjustment
  - Brightness control
  - Music/video playback

### Idle Layer
- Accessed via combo through system layer
- Disables all keys
- Purpose: safely move keyboard without accidental keypresses
- Exit methods:
  - Same combo that enabled it
  - Power cycling the keyboard

## Power Management
- Deep sleep activates after 10-15 minutes of inactivity
- Keyboard wakes when any key is pressed
- Preserves battery life without manual intervention
