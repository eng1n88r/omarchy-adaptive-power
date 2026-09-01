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
2. Optionally, the [adaptive-charge](https://github.com/eng1n88r/adaptive-charge)
   daemon for full adaptive mode (no Rust toolchain needed - installs the
   prebuilt release as a pacman package):

   ```sh
   curl -fsSL https://raw.githubusercontent.com/eng1n88r/adaptive-charge/master/install.sh | bash
   ```

The plugin is useful at every level of setup:

- **No kernel charge thresholds** (hardware unsupported): the adaptive section
  hides itself; you get the stock battery panel.
- **Supported hardware, no daemon**: standalone mode — the presets set a
  static charge limit by writing the kernel threshold via `pkexec` (Omarchy's
  polkit agent shows an auth dialog per change).
- **Daemon installed**: full adaptive mode — schedule learning, pre-unplug
  top-ups, on/off switch, and no password prompts (scoped sudoers grant).

## Install

If another clone of `omarchy.power` is on the bar, disable or remove it first.

```sh
omarchy plugin add https://github.com/eng1n88r/omarchy-adaptive-power.git --enable
```

The widget is placed in the bar's right section; reposition with
`omarchy bar move adaptive.power --section right` or by editing
`~/.config/omarchy/shell.json` if the insert position isn't where your
battery widget used to sit.

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

## License

MIT (see LICENSE). Panel code is derived from the stock `omarchy.power`
plugin (declared via `omarchy.clonedFrom`); the only external dependency is
the optional [adaptive-charge](https://github.com/eng1n88r/adaptive-charge)
daemon, also MIT.
