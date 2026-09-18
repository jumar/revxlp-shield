# RevXLP ZMK shield

ZMK shield support for the RevXLP keyboard.

The shield is designed for the Seeed XIAO nRF52840 (`xiao_ble`) and uses a
74HC595-style SPI GPIO expander for the keyboard columns. The matrix is six
columns by seven rows, with support for the RevXLP's 10u and 12u physical
layouts.

## Features

- 10u and 12u physical-layout definitions
- 6x7 GPIO matrix scan
- SPI GPIO expander support
- Backlight control through a GPIO-controlled LED MOSFET
- ZMK pointing support
- Bundled default keymap

The 12u layout is selected by default. It exposes 42 physical positions: three
rows of twelve keys plus six thumb keys. The 10u layout exposes the alternate
10-key-per-row arrangement.

## Installation

Add the module to the `config/west.yml` manifest of your ZMK configuration:

```yaml
manifest:
  remotes:
    - name: jumar
      url-base: https://github.com/jumar

  projects:
    - name: revxlp-shield
      remote: jumar
      path: modules/shields/revxlp
      revision: <commit>
```

Use a commit hash for `revision` so builds remain reproducible. Then initialize
or update the workspace:

```sh
west update
```

## Build

Build the shield with the `xiao_ble` board:

```sh
west build -s zmk/app -d build/revxlp \
  -b xiao_ble -- -DSHIELD=revxlp
```

A typical `build.yaml` entry is:

```yaml
include:
  - board: xiao_ble//zmk
    shield: revxlp
```

## Selecting the 10u layout

The shield selects `twelve_u_layout` by default. To use the 10u layout, add a
chosen-node override in the keymap or an overlay that is included after the
shield overlay:

```dts
/ {
    chosen {
        zmk,physical-layout = &ten_u_layout;
    };
};
```

The shield also provides a physical-layout position map for both layouts so
keymap tooling can map logical positions to the corresponding physical keys.

## Configuration

The shield enables pointing support in its default configuration. Backlight
support is enabled through `CONFIG_ZMK_BACKLIGHT` and uses the GPIO assigned to
the backlight MOSFET. Adjust or override these settings in the application
configuration when needed.

The shield's default keymap is in
`boards/shields/revxlp/revxlp.keymap`. An application can provide its own
keymap for the shield in the usual ZMK configuration directory.

## Repository layout

```text
boards/shields/revxlp/
├── Kconfig.defconfig
├── Kconfig.shield
├── revxlp.conf
├── revxlp.keymap
├── revxlp.overlay
└── revxlp.zmk.yml
zephyr/module.yml
```

## License

The shield overlay includes the MIT license identifier used by ZMK.
