# config.txt

## What is `config.txt`?

- Instead of the BIOS found on a conventional PC, Raspberry Pi devices use a configuration file called `config.txt`.

- The GPU reads `config.txt` before the Arm CPU and Linux initialise.

- Raspberry Pi OS looks for this file in the **boot partition**, located at `/boot/firmware/`.

> ##### Note
>
> - Prior to Raspberry Pi OS *Bookworm*, Raspberry Pi OS stored the boot partition at `/boot/`.

- You can edit `config.txt` directly from your Raspberry Pi OS installation.

- You can also remove the storage device and edit files in the boot partition, including `config.txt`, from a separate computer.

- Changes to `config.txt` only take effect after a reboot.

- You can view the current active settings using the following commands:

    - `vcgencmd get_config <config>`

        - displays a specific config value.

    - `vcgencmd get_config int`

        - lists all non-zero integer config options

    - `vcgencmd get_config str`

        - lists all non-null string config options

> ##### Note
>
> - Not all config settings can be retrieved using `vcgencmd`.

## Common options

### Common hardware configuration options

#### `dtoverlay`

- The `dtoverlay` option requests the firmware to load a named Device Tree overlay - a configuration file that can enable kernel support for built-in and external hardware.

- For more details, see DTBs, overlays and config.txt.

## Boot Options

### `otg_mod` (Raspberry Pi 4 only)

- USB On-The-Go (often abbreviated to OTG) is a feature that allows supporting USB devices with an appropriate OTG cable to configure themselves as USB hosts.

- On older Raspberry Pis, a single USB 2 controller was used in both USB host and  device mode.

- Raspberry Pi 4B adds a high performance USB 3 controller, attached via PCIe, to drive the main USB ports.

- The legacy USB 2 controller still available on the USB-C power connector use as a device (`otg_mode=0`, the default).

- `otg_mode=1` requests that a more capable XHCI USB 2 controller is used as another host controller on that USB-C connector.
