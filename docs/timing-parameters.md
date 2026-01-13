# ZMK Timing Parameters Reference

This document covers timing parameters for **hold-tap behaviours** (including `&mt`,
`&lt`, and custom "timerless" HRM) and **combo behaviours** in ZMK firmware.

## Introduction: Two Schools of Thought

The "Home Row Modifier" (HRM) layout places modifier keys (Shift, Ctrl, Alt, GUI) on the
home row (A, S, D, F, J, K, L, ;), activating them when held and sending characters when
tapped. While ergonomically superior, this creates ambiguity for the firmware: is a
quick "roll" of two keys a typing burst or a modifier chord?

To solve this, ZMK users have developed two distinct architectural strategies:

1. **Positional Logic (Spatial):** Strictly enforces that a modifier on the left hand
   can _only_ modify a key on the right hand. This is the safest method, virtually
   eliminating errors, but prevents one-handed shortcuts.
2. **Non-Positional Logic (Temporal):** Relies on typing rhythm (idle time) to
   distinguish between fast typing (taps) and deliberate actions (mods). This is more
   flexible, allowing one-handed usage (e.g., mouse + keyboard shortcuts), but requires
   tuning to your specific typing speed.

**Choose Positional** if you are a disciplined touch typist and want a "bulletproof"
setup that rarely misfires.

**Choose Non-Positional** if you need one-handed freedom, use the mouse with keyboard
modifiers frequently, or do not strictly alternate hands.

Both strategies rely heavily on `require-prior-idle-ms` to distinguish "typing mode"
from "command mode," making it the most important modern setting for ergonomic firmware.

## Hold-Tap Parameters

### `tapping-term-ms`

**Default:** 200ms (for `&mt` and `&lt`)

| Value     | Use Case                                                  |
| --------- | --------------------------------------------------------- |
| 150-180ms | Aggressive — responsive but more prone to misfires        |
| 200ms     | ZMK default — balanced starting point                     |
| 250-280ms | "Timerless" approach — timing becomes less critical       |
| 5000ms+   | Extreme timerless — relies entirely on `balanced` flavour |

**How it works:** The hold behaviour activates if:

1. You hold longer than `tapping-term-ms`, OR
2. (With `balanced` flavour) another key is pressed AND released while holding

**Recommendation:** For standard `&mt` without positional filtering, **200-220ms** is a
good balance. For "timerless" setups with positional hold-tap, **280ms** makes timing
irrelevant.

### `require-prior-idle-ms`

**Default:** Disabled (-1)

| Value     | Use Case                            |
| --------- | ----------------------------------- |
| 50-75ms   | Very aggressive — fast typists only |
| 100-125ms | Responsive with moderate protection |
| 150ms     | Standard recommendation (~70 WPM)   |
| 175ms+    | Maximum misfire protection          |

**Formula:** `10500 / WPM`

- 70 WPM → 150ms
- 85 WPM → 123ms
- 100 WPM → 105ms
- 115 WPM → 91ms

**How it works:** If any non-modifier key was pressed within this duration before the
hold-tap, it immediately resolves as a tap. This:

- Eliminates typing delay during fast typing
- Prevents most accidental modifier activations
- Makes hold-tap behaviour effectively "disabled" during typing bursts

**Trade-off:** Higher values = better misfire protection but harder to use HRM for
capitalising letters mid-sentence.

**Recommendation:** **125-150ms** for most users. Use the formula if you know your WPM.

### `quick-tap-ms`

**Default:** Disabled (-1)

| Value     | Use Case                                            |
| --------- | --------------------------------------------------- |
| 0ms       | Disabled — double-tap triggers hold on second press |
| 150-175ms | Standard — enables rapid repeated taps              |
| 200ms+    | Very permissive for repeated keys                   |

**How it works:** If you tap the same key again within this window, it immediately
registers as a tap (hold not possible). Useful for:

- Rapid repeated keys like `ssss` or `backspace`
- Preventing accidental holds when double-tapping

**Trade-off:** Higher values make repeated taps easier but can cause accidental taps
when you intended to hold.

**Recommendation:** **175ms** (urob's value) or **0ms** if you never need rapid repeats
on HRM keys.

### `flavor`

**Default:** `hold-preferred` (for `&mt`), `tap-preferred` (for `&lt`)

| Flavour                  | Hold Triggers When                                       | Best For                         |
| ------------------------ | -------------------------------------------------------- | -------------------------------- |
| `hold-preferred`         | Tapping-term expires OR another key pressed              | Aggressive mod activation        |
| `balanced`               | Tapping-term expires OR another key pressed AND released | **Home row mods** (most popular) |
| `tap-preferred`          | Only when tapping-term expires                           | Layer-taps, tap-priority keys    |
| `tap-unless-interrupted` | Another key pressed before tapping-term                  | Inverted logic                   |

**Why `balanced` for HRM:** It matches natural modifier usage (hold shift → tap letter →
release shift). The "press and release" requirement prevents false activations from key
rolling.

### `hold-trigger-key-positions` (Positional Hold-Tap)

**Default:** Not set (all positions trigger hold)

**How it works:** If set, pressing a key NOT in this list forces a tap. Typically
configured with opposite-hand keys only:

- Left HRM: trigger on right-hand keys
- Right HRM: trigger on left-hand keys

**Benefit:** Prevents same-hand key rolls from triggering modifiers.

### `hold-trigger-on-release`

**Default:** false

**How it works:** Delays the positional check until the interrupting key is RELEASED
(not pressed).

**Why use it:** Allows combining multiple modifiers on the same hand while maintaining
positional protection for tapped keys.

**Example:** Without this, pressing Ctrl+Shift (both left hand) would trigger a tap.
With this enabled, holding both works correctly.

**Requirement:** Works best with `balanced` or `tap-preferred` flavour. NOT compatible
with `hold-preferred`.

### `retro-tap`

**Default:** false

**How it works:** If you hold past tapping-term but DON'T press another key, release
triggers the tap behaviour.

**Use case:** Lets you "change your mind" — start holding for a modifier, then release
without using it to get the tap.

**Downside:** You can't use the modifier alone (e.g., holding Cmd to click something
with mouse).

## Combo Parameters

### `timeout-ms`

**Default:** 50ms

| Value    | Use Case                                                    |
| -------- | ----------------------------------------------------------- |
| 18-25ms  | Very tight — fewer false positives, requires precise timing |
| 30-40ms  | Balanced — good for adjacent keys                           |
| 50ms     | ZMK default                                                 |
| 70-100ms | Permissive — easier to trigger, more false positives        |

**How it works:** All keys in the combo must be pressed within this duration.

**Recommendation:** **25-35ms** for horizontal adjacent combos. Lower values = fewer
misfires with no downside if you can still trigger reliably.

### `require-prior-idle-ms` (Combos)

**Default:** Disabled (-1)

| Value     | Use Case                                     |
| --------- | -------------------------------------------- |
| 50-70ms   | Responsive — combos work during fast typing  |
| 100-125ms | Moderate protection                          |
| 150-175ms | Maximum protection — must pause before combo |

**How it works:** Same as hold-tap — if any key was pressed within this duration, the
combo won't trigger.

**Recommendation:** **70-100ms** for frequently-used combos. **150ms+** for combos that
shouldn't trigger during typing (like layer access).

### `slow-release`

**Default:** false

**How it works:** Normally, combo releases when ANY constituent key is released. With
`slow-release`, it waits until ALL keys are released.

**Use case:** Better for modifier combos where you want to hold the result.

## Thumb Key Parameters (Layer Taps)

Thumbs operate differently from fingers; they don't "roll" as much as they "chord."
Layer taps (`&lt`) on thumb keys benefit from different tuning than home row mods.

### Recommended Settings

- **flavour**: `hold-preferred` — You generally want layers to activate _immediately_
  when pressed, even if you tap the next key very quickly.
- **tapping-term-ms**: 200ms — Standard timing works well for thumbs.
- **quick-tap-ms**: 200ms — Allows for "tap-hold" to repeat the key (e.g., repeating
  Backspace or Space).

### Code Example (Thumb Layer Tap)

```dts
/ {
    behaviors {
        lt_thumb: layer_tap_thumb {
            compatible = "zmk,behavior-hold-tap";
            flavor = "hold-preferred";
            tapping-term-ms = <200>;
            quick-tap-ms = <200>;
            bindings = <&mo>, <&kp>;
        };
    };
};
```

## Configuration Profiles

### Profile 1: Standard `&mt`/`&lt`

For standard hold-taps WITHOUT positional filtering:

```dts
tapping-term-ms = <200>;
require-prior-idle-ms = <125>;
quick-tap-ms = <175>;
flavor = "balanced";
```

**Trade-offs:**

- ✅ Simple configuration
- ✅ Works well for moderate typists
- ⚠️ Same-hand key rolls can trigger false holds
- ⚠️ Requires good typing discipline

### Profile 2: "Timerless" Positional HRM (urob's Approach)

For custom hold-taps WITH positional filtering:

```dts
tapping-term-ms = <280>;
require-prior-idle-ms = <150>;
quick-tap-ms = <175>;
flavor = "balanced";
hold-trigger-key-positions = <OPPOSITE_HAND_KEYS>;
hold-trigger-on-release;
```

**Trade-offs:**

- ✅ Virtually eliminates misfires
- ✅ Timing becomes irrelevant
- ✅ Same-hand rolls protected by positional filtering
- ⚠️ More complex configuration
- ⚠️ Shifting letters requires proper nesting or separate shift key

### Profile 3: Aggressive Fast Typist

For 100+ WPM typists who prioritise responsiveness:

```dts
tapping-term-ms = <180>;
require-prior-idle-ms = <100>;
quick-tap-ms = <150>;
flavor = "balanced";
```

**Trade-offs:**

- ✅ Very responsive
- ⚠️ More prone to misfires
- ⚠️ Requires consistent typing technique

### Combo Timing Recommendations

**Standard combos (Tab, Backspace, Enter, etc.):**

```dts
timeout-ms = <35>;
require-prior-idle-ms = <70>;
slow-release;
```

**Layer/System combos (less time-critical):**

```dts
timeout-ms = <50>;
require-prior-idle-ms = <100>;
slow-release;
```

**Space combo (needs to work during fast typing):**

```dts
timeout-ms = <35>;
require-prior-idle-ms = <35>;
slow-release;
```

### Summary: Positional vs Non-Positional HRM

| Parameter                      | Positional HRM (Safe) | Non-Positional HRM (Flexible) | Effect                                              |
| :----------------------------- | :-------------------- | :---------------------------- | :-------------------------------------------------- |
| **flavour**                    | balanced              | balanced                      | Allows nested key presses to trigger mods instantly |
| **tapping-term-ms**            | 280–300ms             | 200–250ms                     | How long before a hold is forced                    |
| **require-prior-idle-ms**      | 150ms                 | 125–150ms                     | Forces "tap" during fast typing bursts              |
| **quick-tap-ms**               | 175ms                 | 175ms                         | Allows double-tap-hold to repeat key                |
| **hold-trigger-on-release**    | true                  | true                          | Waits for key release; essential for rolling        |
| **hold-trigger-key-positions** | **Opposite hand**     | **None**                      | The core difference — positional filters by hand    |

### Template: Positional HRM

```dts
/* Define Left and Right hand key indices for your board */
#define KEYS_L 0 1 2 3 4 10 11 12 13 14 20 21 22 23 24
#define KEYS_R 5 6 7 8 9 15 16 17 18 19 25 26 27 28 29
#define THUMBS 30 31 32 33

/ {
    behaviors {
        hm_l: homerow_mods_left {
            compatible = "zmk,behavior-hold-tap";
            flavor = "balanced";
            tapping-term-ms = <TAPPING_TERM>;
            quick-tap-ms = <QUICK_TAP>;
            require-prior-idle-ms = <PRIOR_IDLE>;
            hold-trigger-key-positions = <KEYS_R THUMBS>;
            hold-trigger-on-release;
            bindings = <&kp>, <&kp>;
        };

        hm_r: homerow_mods_right {
            compatible = "zmk,behavior-hold-tap";
            flavor = "balanced";
            tapping-term-ms = <TAPPING_TERM>;
            quick-tap-ms = <QUICK_TAP>;
            require-prior-idle-ms = <PRIOR_IDLE>;
            hold-trigger-key-positions = <KEYS_L THUMBS>;
            hold-trigger-on-release;
            bindings = <&kp>, <&kp>;
        };
    };
};
```

### Template: Non-Positional HRM

```dts
/ {
    behaviors {
        hm: homerow_mods {
            compatible = "zmk,behavior-hold-tap";
            flavor = "balanced";
            tapping-term-ms = <TAPPING_TERM>;
            quick-tap-ms = <QUICK_TAP>;
            require-prior-idle-ms = <PRIOR_IDLE>;
            hold-trigger-on-release;
            bindings = <&kp>, <&kp>;
        };
    };
};
```

## Parameter Interactions

### `require-prior-idle-ms` vs `tapping-term-ms`

- `require-prior-idle-ms` handles the **typing delay** problem (immediate tap during
  fast typing)
- `tapping-term-ms` handles the **hold detection** problem (how long to wait for hold)
- With `balanced` flavour and good `require-prior-idle-ms`, a high `tapping-term-ms` has
  minimal downside

### Positional Hold-Tap vs `require-prior-idle-ms`

- Positional filtering prevents **same-hand** misfires
- `require-prior-idle-ms` prevents **typing burst** misfires
- Together, they provide comprehensive protection

### Combo `timeout-ms` vs `require-prior-idle-ms`

- Lower `timeout-ms` = fewer false positives (accidental combos)
- Higher `require-prior-idle-ms` = fewer misfires during typing
- Both can be tuned independently

## Troubleshooting Guide

| Problem                                   | Solution                                                                             |
| ----------------------------------------- | ------------------------------------------------------------------------------------ |
| **False positives (hold instead of tap)** | Increase `require-prior-idle-ms`, increase `tapping-term-ms`, use `balanced` flavour |
| **False negatives (tap instead of hold)** | Decrease `require-prior-idle-ms`, use `hold-preferred` flavour                       |
| **Same-hand misfires**                    | Add positional `hold-trigger-key-positions`                                          |
| **Can't combine modifiers**               | Enable `hold-trigger-on-release`                                                     |
| **Typing delay**                          | Increase `require-prior-idle-ms` (counterintuitive but correct)                      |
| **Accidental combos**                     | Decrease `timeout-ms`, increase combo `require-prior-idle-ms`                        |
| **Can't trigger combos**                  | Increase `timeout-ms`, decrease `require-prior-idle-ms`                              |

## Sources

- [ZMK Hold-Tap Documentation](https://zmk.dev/docs/keymaps/behaviors/hold-tap)
- [ZMK Combo Documentation](https://zmk.dev/docs/keymaps/combos)
- [ZMK Behaviour Configuration](https://zmk.dev/docs/config/behaviors)
- [urob/zmk-config](https://github.com/urob/zmk-config)
- [Taming Home Row Mods](https://sunaku.github.io/home-row-mods.html)
- [urob/zmk-config Discussion #71](https://github.com/urob/zmk-config/discussions/71)
- [urob/zmk-config Discussion #79](https://github.com/urob/zmk-config/discussions/79)
- [Home Row Mods Guide](https://precondition.github.io/home-row-mods.html)
