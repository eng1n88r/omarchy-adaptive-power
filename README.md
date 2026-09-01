# Adaptive Power

Replaces the Omarchy battery widget with the same panel plus adaptive
charging controls, driven by the
[adaptive-charge](https://github.com/eng1n88r/adaptive-charge) daemon.

While plugged in, charging stops at a ceiling you pick (70–100%) to reduce
battery wear. The daemon learns at what time you usually unplug and finishes
charging to 100% shortly before it. The panel section shows what it is doing
("charging to 80%", "holding at 80%", "charging to 100% before you unplug")
with an on/off switch and one-click ceiling presets — no password prompts,
via a scoped sudoers grant that the daemon's installer sets up.

## Requirements

1. A battery exposing `/sys/class/power_supply/BAT*/charge_control_end_threshold`
   (ThinkPad, ASUS, Framework, …). Without it the daemon has nothing to write.
2. The [adaptive-charge](https://github.com/eng1n88r/adaptive-charge) daemon:

   ```sh
   git clone https://github.com/eng1n88r/adaptive-charge
   cd adaptive-charge
   make build
   sudo make install
   ```

Without the daemon the panel still works as a normal battery widget; the
adaptive section shows "stopped" and clicks report an error.

## Install

If another clone of `omarchy.power` is on the bar, disable or remove it first.

```sh
omarchy plugin add https://github.com/eng1n88r/omarchy-adaptive-power.git --enable
```

## Remove

```sh
omarchy plugin remove adaptive.power --yes
```

Removal puts `omarchy.power` back in the same bar slot.

## Notes

- The ceiling is really a band: picking 80% sets stop-at-80 / restart-below-75,
  so a 1% dip does not trigger charge micro-cycles. The battery rests anywhere
  inside the band.
- Tracks the stock `omarchy.power` panel (this is a clone, like other
  replacement power panels); expect occasional rebases after Omarchy updates.
