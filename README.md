# Keychron Battery (Noctalia)

Bar widget, panel and low-battery notifications for Keychron mice.

The plugin does no hardware access itself: it reads Keychron mice from UPower
(`upower --dump`). The widget uses the bar's own text and icon colors and turns
`error` red at the notification threshold.

| Mouse connection | Source | Battery |
| --- | --- | --- |
| Receiver (Ultra-Link 8K `3434:d028`) or cable (M5 `3434:d048`) | [keychron-battery-dkms](https://github.com/csutcliff/keychron-battery-dkms) kernel module → `/sys/class/power_supply/keychron_mouse` → UPower | yes, updated every 5 min |
| Bluetooth | BLE battery service → kernel → UPower | yes |

Other Keychron mice on a receiver only work if their USB ID is in the module's
device table. The module always reports "discharging" and the model name
"Keychron M5"; set *Mouse name* in the plugin settings to change the label.

## Requirements

Install the kernel module (DKMS, rebuilt on kernel updates):

```sh
git clone https://github.com/csutcliff/keychron-battery-dkms.git
sudo cp -r keychron-battery-dkms /usr/src/keychron-battery-0.1.2
sudo dkms install keychron-battery/0.1.2
sudo modprobe keychron_battery
cat /sys/class/power_supply/keychron_mouse/capacity
```

With Secure Boot the module has to be signed with your enrolled MOK key.

## Install

```sh
noctalia msg plugins source add keychron-local path ~/coding-projects/keychron-niri
noctalia msg plugins enable dquolke/keychron-battery
```

Then add a widget instance to a bar in `~/.local/state/noctalia/settings.toml`:

```toml
end = [ ..., "keychron", ... ]

[widget.keychron]
type = "dquolke/keychron-battery:keychron-battery"
```

`noctalia msg config-reload` applies it; `.luau` edits hot-reload.

## Settings

- *Notify at (%)*: notification and red bar entry at this level (default 20).
- *Critical threshold (%)*: second, urgent notification (default 10).
- *Notifications*, *Poll interval*, *Mouse name*.
