# Raspberry Pi OS

## Introduction

- Raspberry Pi OS is a free, Debian-based operating system optimised for the Raspberry Pi hardware.

- Raspberry Pi OS supports over 35,000 Debian packages.

- We recommend Raspberry Pi OS for most Raspberry Pi use cases.

- Because Raspberry Pi OS is derived from Debian, it follows a staggered version of the Debian release cycle.

- Releases happen roughly every 2 years.

- The latest version of Raspberry Pi OS is based on Debian Bookworm.

- The previous version was based on Debian Bullseye.

## Update software

### Manage software packages with APT

#### Install updates

- `apt` stores a list of software sources in a file at `/etc/apt/sources.list`.

- Before installing software, run the following command to update your local list of packages using `/etc/apt/sources.list`:

```shell
$ sudo apt update
```

- Run the following command to upgrade all your installed packages to their latest versions:

```shell
$ sudo apt full-upgrade
```

> ##### Tip
>
> - Unlike Debian, Raspberry Pi OS is under continual development.
>
> - As a result, package dependencies sometimes change, so you should always use `full-upgrade` instead of the standard `upgrade`.

- Using `apt` to keep Raspberry Pi OS up to date also keeps your Linux kernel and firmware up to date, since Raspberry Pi distributes them as Debian packages.

- When Raspberry Pi releases a new major version of Raspberry Pi OS, the above commands won’t upgrade your operating system to that new major version.

- To upgrade to a new major version, follow our OS upgrade instructions.

#### Search for software

- To search the archives for a package, pass a search keyword to `apt-cache search`:

```shell
$ apt-cache search <keyword>
```

- Use the following command to view detailed information about a package:

```shell
$ apt-cache show <package-name>
```

- Use this command to verify that the maintainer, version, and size match your expectations for a package.

#### Install a package

```shell
$ sudo apt install <package-name>
```

- `apt` will display the amount of disk space the package will consume.

- Enter **Y** and press **Enter** to confirm installation of the package.

- You can skip this confirmation step by adding the -y flag to the command above.

#### Uninstall a package

```shell
sudo apt remove <package-name>
```

> ##### TIP
>
> - To completely remove all traces of the package, including configuration files, use `purge` instead of `remove`.

- `apt` will display the amount of disk space removing the package will free up.

- Enter **Y** and press **Enter** to confirm installation of the package.
You can skip this confirmation step by adding the -y flag to the command above.

#### Manage `apt` disk usage

- Before running, `sudo apt full-upgrade` shows the amount of data you’ll need to download and store on disk to complete an upgrade.

- To check that you have enough free disk space, run the following command:

```shell
$ df -h
```

- `apt` stores downloaded package (.deb) files in `/var/cache/apt/archives`.

- During installation, `apt` downloads these packages, then copies files from the packages to the correct installation locations.

- To delete any lingering package files, run the following command:

```shell
$ sudo apt clean
```

### Upgrade your operating system to a new major version

> ##### Warning
>
> - Before attempting a major version upgrade, make a backup.

- To update the operating system to a new major release on your Raspberry Pi, image a second SD card with the new release.

- Use a USB SD card reader or network storage to copy files and configuration from your current installation to the new SD card.

- Then, swap the new SD card into the slot on your Raspberry Pi, and boot.

### Upgrade your firmware

> ##### Warning
>
> - Before attempting a firmware upgrade, make a backup.

> ##### Warning
>
> - Pre-release versions of software are not guaranteed to work.
>
> - Do not use `rpi-update` on any system unless recommended to do so by a Raspberry Pi engineer.
>
> - It could leave your system unreliable or broken. Do not use `rpi-update` as part of any regular update process.

- To update the firmware on your Raspberry Pi to the latest version, use `rpi-update`.

- `rpi-update` downloads the latest pre-release version of the Linux kernel, its matching modules, device tree files, and the latest versions of the VideoCore firmware.

- It then installs these files into an existing Raspberry Pi OS install.

- All the source data used by `rpi-update` comes from the `rpi-firmware` repository.

- This repository contains a subset of the data from the official firmware repository.

- Run `rpi-update` as root to initiate the update.

- Once the update is complete, reboot your Raspberry Pi for these changes to take effect:

```shell
$ sudo rpi-update
$ sudo reboot
```

### Downgrade firmware to the last stable release

- If you update your firmware to the latest release and experience an issue, use the following command to return to the last stable firmware release:

```shell
$ sudo apt-get update
$ sudo apt install --reinstall raspi-firmware
```

> ##### Note
>
> - If you still run Raspberry Pi OS Bullseye, you must instead reinstall raspberrypi-kernel using the following command:

## Play audio and video

- Raspberry Pi OS comes with VLC media player pre-installed.

- You can use VLC to play video and audio files. 

- VLC uses hardware acceleration in Raspberry Pi OS, and supports many popular audio and video file formats.

### VLC media player

#### `vlc` CLI

```shell
$ wget --trust-server-names http://rptl.io/big-buck-bunny
$ vlc big-buck-bunny-1080p.mp4
```

- To prevent the VLC GUI staying open after your file has finished playing, add the --play-and-exit flag:

- To prevent the VLC GUI staying open after your file has finished playing, add the --play-and-exit flag:

```shell
$ vlc --play-and-exit big-buck-bunny-1080p.mp4
```

- To play a video in fullscreen mode (which can result in smoother playback in some circumstances), add the --fullscreen flag:

```shell
$ vlc --play-and-exit --fullscreen big-buck-bunny-1080p.mp4
```

#### Use cvlc to play media without a GUI

- If you use cvlc instead of vlc with any of these commands, then the VLC GUI won’t be shown:

```shell
$ cvlc --play-and-exit big-buck-bunny-1080p.mp4
```

### Specify an audio output device

- To force audio output to a particular device, pass the alsa value to the -A option to use ALSA audio output, and the --alsa-audio-device option to specify an audio output device:

```shell
$ cvlc --play-and-exit -A alsa --alsa-audio-device <alsa-device> computer-startup-music.mp3
```

- Replace the <alsa-device> placeholder with one of the following options:

|ALSA device|Description|
|-|-|
|`sysdefault:CARD=Headphones`|The headphone jack|
|`sysdefault:CARD=vc4hdmi0`|The HDMI0 output on a Raspberry Pi 4, 5, 400, or Compute Module 4|
|`sysdefault:CARD=vc4hdmi1`|The HDMI1 output on a Raspberry Pi 4, 5, 400, or Compute Module 4|

> ##### TIP
>
> - Use the following command to get a list of all ALSA devices on your Raspberry Pi:
>
> ```shell
> $ aplay -L | grep sysdefault
> ```

### Improve stream playback performance

- If you have a raw H.264 stream, like those captured from a Raspberry Pi Camera Module, you can improve playback performance in VLC by wrapping the stream inside a container format such as MP4.

- You can use `ffmpeg` to convert stream content into a container file.

## Utilities

- There are several useful command-line utilities pre-installed in Raspberry Pi OS.

### `kmsprint`

- The `kmsprint` tool can be used to list the display-modes supported by the monitors attached to the Raspberry Pi.

- Use `kmsprint` to see details of the monitors connected to the Raspberry Pi, and `kmsprint -m` to see a list of all the display modes supported by each monitor.

### `vclog`

- `vclog` displays log messages from the VideoCore GPU from Linux running on the Arm.

    - It needs to be run as root.

- `sudo vclog --msg` prints out the message log, whilst `sudo vclog --assert` prints out the assertion log.

### `vcgencmd`

- The `vcgencmd` tool is used to output information from the VideoCore GPU on the Raspberry Pi.

- To get a list of all commands supported by `vcgencmd`, use `vcgencmd commands`.

#### `vcos`

- The `vcos` command has two useful sub-commands:

    - `version` displays the build date and version of the firmware on the VideoCore

    - `log` status displays the error log status of the various VideoCore firmware areas

#### `version`

- Displays the build date and version of the VideoCore firmware.

#### `get_throttled`

- Returns the throttled state of the system.

- This is a bit pattern.

- A bit being set indicates the following meanings:

|Bit|Hexadecimal value|Meaning|
|-|-|-|
|0|`0x1`|Undervoltage detected|
|1|`0x2`|Arm frequency capped|
|2|`0x4`|Currently throtlled|
|3|`0x8`|Soft temperature limit active|
|16|`0x10000`|Undervoltage has occurred|
|17|`0x20000`|Arm frequency capping has ocurred|
|18|`0x40000`|Throttling has occurred|
|19|`0x80000`|Soft temperature limit has occurred|

#### `measure_temp`

- Returns the temperature of the SoC as measured by its internal temperature sensor.

- On Raspberry Pi 4, `measure_temp pmic` returns the temperature of the PMIC.

#### `measure_clock [clock]`

- This returns the current frequency of the specified clock.

- Accepts the following clock values:

|clock|Description|
|-|-|
|`arm`|ARM core(s)|
|`core`|GPU core|
|`h264`|H.264 block|
|`isp`|Image Sensor Pipeline|
|`v3d`|3D block|
|`uart`|UART|
|`pwm`|PWM block (analogue audio output)|
|`emmc`|SD card interface
|`pixel`|Pixel valves|
|`vec`|Analogue video encodier|
|`hdmi`|HDMI|
|`dpi`|Display Parallel Interface|

#### `measure_volts [block]`

- Displays the current voltages used by the specific block. Accepts the following block values:

|block|Description|
|-|-|
|`core`|VC4 core voltage|
|`sdram_c`|SDRAM Core Voltage|
|`sdram_i`|SDRAM I/O Voltage|
|`sdram_p`|SDRAM Phy Voltage|

#### `otp_dump`

- Displays the content of the OTP (one-time programmable) memory inside the SoC.

- These are 32-bit values, indexed from 8 to 64. See the OTP bits page for more details.

#### `get_config [configuration item|int|str]`

- Displays the value of the configuration setting specified: alternatively, specify either int (integer) or str (string) to see all configuration items of the given type.

- For example, the following command returns the total memory on the device in megabytes:

```shell
$ vcgencmd get_config total_mem
```

#### `get_mem type`

- Reports on the amount of memory addressable by the Arm and the GPU.

- To show the amount of Arm-addressable memory, use `vcgencmd get_mem arm`; to show the amount of GPU-addressable memory, use `vcgencmd get_mem gpu`.

- On devices with more than 1GB of memory, the `arm` parameter will always return 1GB minus the `gpu` memory value, since the GPU firmware is only aware of the first 1GB of memory.

- To get an accurate report of the total memory on the device, see the `total_mem` configuration item and the `get_config` section above.

##### `codec_enabled [type]`

- Reports whether the specified codec type is enabled.

- Possible options for type are AGIF, FLAC, H263, H264, MJPA, MJPB, MJPG, MPG2, MPG4, MVC0, PCM, THRA, VORB, VP6, VP8, WMV9, WVC1.

- Note that because the H.265 HW block on the Raspberry Pi 4 and 400 is not part of the VideoCore GPU, its status is not accessed via this command.

##### `mem_oom`

- Displays statistics on any OOM (out of memory) events occurring in the VideoCore memory space.

##### `mem_reloc_stats`

- Displays statistics from the relocatable memory allocator on the VideoCore.

##### `read_ring_osc`

- Returns the current speed, voltage and temperature of the ring oscillator.

## Use Python on a Raspberry Pi

- Raspberry Pi OS comes with Python 3 pre-installed.

- Interfering with the system Python installation can cause problems for your operating system.

- When you install third-party Python libraries, always use the correct package-management tools.

- On Linux, you can install python dependencies in two ways:

    - use apt to install pre-configured system packages

    - use pip to install libraries using Python’s dependency manager in a virtual environment

### Install Python packages using apt

- Packages installed via apt are packaged specifically for Raspberry Pi OS.

- These packages usually come pre-compiled, so they install faster.

- Because `apt` manages dependencies for all packages, installing with this method includes all of the sub-dependencies needed to run the package.

- And `apt` ensures that you don’t break other packages if you uninstall.

## GPIO and the 40-pin header

- A 40-pin GPIO header is found on all current Raspberry Pi boards, although it is unpopulated on Raspberry Pi Zero, Raspberry Pi Zero W, and Raspberry Pi Zero 2 W.

- The GPIO headers on all boards have a 0.1in (2.54mm) pin pitch.

![GPIO-Pinout-Diagram-2](/images/GPIO-Pinout-Diagram-2.png)

![GPIO](/images/GPIO.png)

> ##### Note
>
> - The GPIO pin numbering scheme is not in numerical order.
>
> - GPIO pins 0 and 1 are present on the board (physical pins 27 and 28), but are reserved for advanced use.

### Voltages

- Two 5V pins and two 3.3V pins are present on the board, as well as a number of ground pins (GND), which can not be reconfigured. The remaining pins are all general-purpose 3.3V pins, meaning outputs are set to 3.3V and inputs are 3.3V-tolerant.

### Outputs

- A GPIO pin designated as an output pin can be set to high (3.3V) or low (0V).

### Inputs

- A GPIO pin designated as an input pin can be read as high (3.3V) or low (0V).

- This is made easier with the use of internal pull-up or pull-down resistors.

- Pins GPIO2 and GPIO3 have fixed pull-up resistors, but for other pins this can be configured in software.

### Other GPIO functions

- Some functions are available on all pins, others on specific pins:

    - PWM (pulse-width modulation)

        - Software PWM available on all pins

        - Hardware PWM available on GPIO12, GPIO13, GPIO18, GPIO19

    - SPI

        - SPI0: MOSI (GPIO10); MISO (GPIO9); SCLK (GPIO11); CE0 (GPIO8), CE1 (GPIO7)

        - SPI1: MOSI (GPIO20); MISO (GPIO19); SCLK (GPIO21); CE0 (GPIO18); CE1 (GPIO17); CE2 (GPIO16)

    - I2C

        - Data: (GPIO2); Clock (GPIO3)

        - EEPROM Data: (GPIO0); EEPROM Clock (GPIO1)

    - Serial

        - TX (GPIO14); RX (GPIO15)

### View a GPIO pinout for your Raspberry Pi

- A GPIO reference can be accessed on your Raspberry Pi by opening a terminal window and running the command pinout.

- This tool is provided by the GPIO Zero Python library, which is installed by default in Raspberry Pi OS.

> ##### Warning
>
> - While connecting simple components to GPIO pins is safe, be careful how you wire things up.
>
> - LEDs should have resistors to limit the current passing through them.
>
> - Do not use 5V for 3.3V components.
>
> - Do not connect motors directly to the GPIO pins, instead use an H-bridge circuit or a motor controller board.

### Permissions

- In order to use the GPIO ports, your user must be a member of the gpio group.

- The default user account is a member by default, but you must add other users manually using the following command:

    ```shell
    $ sudo usermmod -a -G gpio <username>
    ```
