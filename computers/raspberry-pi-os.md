# Raspberry Pi OS

## Introduction

- Raspberry Pi OS is a free, Debian-based operating system optimised for the Raspberry Pi hardware.

- Raspberry Pi OS supports over 35,000 Debian packages.

- We recommend Raspberry Pi OS for most Raspberry Pi use cases.

<br>

- Because Raspberry Pi OS is derived from Debian, it follows a staggered version of the Debian release cycle.

- Releases happen roughly every 2 years.

<br>

- The latest version of Raspberry Pi OS is based on Debian Bookworm.

- The previous version was based on Debian Bullseye.

<br>

- You can find images of Raspberry Pi OS at raspberrypi.com/software/operating-systems/

## Update software

- Always keep the software running on your Raspberry Pi updated to the latest version.

- This keeps your device secure from vulnerabilities and ensures that you get the latest bug fixes.

### Manage software packages with APT

- Advanced Package Tool (APT) is the recommended way to install, update, and remove software in Raspberry Pi OS.

- You can access APT through the `apt` CLI.

#### Install updates

- `apt` stores a list of software sources in a file at `/etc/apt/sources.list`.

- Before installing software, run the following command to **update** your local list of packages using `/etc/apt/sources.list`:

    ```shell
    $ sudo apt update
    ```

- Run the following command to **upgrade** all your installed packages to their latest versions:

    ```shell
    $ sudo apt full-upgrade
    ```

> ##### Tip
>
> - Unlike Debian, Raspberry Pi OS is under continual development.
>
> - As a result, package dependencies sometimes change, so you should always use `full-upgrade` instead of the standard `upgrade`.

- Run these commands regularly to keep your software up-to-date.

- Using `apt` to keep Raspberry Pi OS up to date also keeps your Linux kernel and firmware up to date, since Raspberry Pi distributes them as Debian packages.

<br>

- When Raspberry Pi releases a new major version of Raspberry Pi OS, the above commands won’t upgrade your operating system to that new major version.

- To upgrade to a new major version, follow our OS upgrade instructions.

#### Search for software

- To search the archives for a package, pass a search keyword to `apt-cache search`:

    ```shell
    $ apt-cache search <keyword>
    ```

- The search returns multiple packages with names or descriptions that included the keyword.

<br>

- Use the following command to view detailed information about a package:

    ```shell
    $ apt-cache show <package-name>
    ```

- Use this command to verify that the maintainer, version, and size match your expectations for a package.

#### Install a package

- To install a package on your Raspberry Pi, pass the name of the package to the following command:

    ```shell
    $ sudo apt install <package-name>
    ```

- `apt` will display the amount of disk space the package will consume.

- Enter **Y** and press **Enter** to confirm installation of the package.

- You can skip this confirmation step by adding the `-y` flag to the command above.

#### Uninstall a package

- To uninstall a package from your Raspberry Pi, pass the name of the package to the following command:


    ```shell
    sudo apt remove <package-name>
    ```

    > ##### TIP
    >
    > - To completely remove all traces of the package, including configuration files, use `purge` instead of `remove`.

- `apt` will display the amount of disk space removing the package will free up.

- Enter **Y** and press **Enter** to confirm installation of the package.

- You can skip this confirmation step by adding the `-y` flag to the command above.

#### Manage `apt` disk usage

- Before running, `sudo apt full-upgrade` shows the amount of data you’ll need to download and store on disk to complete an upgrade.

- To check that you have enough free disk space, run the following command:

    ```sh
    $ df -h
    ```

- `apt` stores downloaded package (`.deb`) files in `/var/cache/apt/archives`.

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
> - It could leave your system unreliable or broken.
>
> - Do not use `rpi-update` as part of any regular update process.

- To update the firmware on your Raspberry Pi to the latest version, use `rpi-update`.

<br>

- `rpi-update` downloads the latest pre-release version of the Linux kernel, its matching modules, device tree files, and the latest versions of the VideoCore firmware.

- It then installs these files into an existing Raspberry Pi OS install.

- All the source data used by `rpi-update` comes from the `rpi-firmware` repository.

- This repository contains a subset of the data from the official firmware repository.

- Run `rpi-update` as root to initiate the update.

- Once the update is complete, reboot your Raspberry Pi for these changes to take effect:

    ```sh
    $ sudo rpi-update
    $ sudo reboot
    ```

### Downgrade firmware to the last stable release

- If you update your firmware to the latest release and experience an issue, use the following command to return to the last stable firmware release:

    ```sh
    $ sudo apt-get update
    $ sudo apt install --reinstall raspi-firmware
    ```

> ##### Note
>
> - If you still run Raspberry Pi OS Bullseye, you must instead reinstall `raspberrypi-kernel` using the following command:
>
> ```sh
> $ sudo apt install --reinstall libraspberrypi0 libraspberrypi-{bin,dev,doc} raspberrypi-{kernel,bootloader}
> ```
>
> - Reboot your Raspberry Pi with `sudo reboot` to put these changes into effect.

## Play audio and video

- Raspberry Pi OS comes with VLC media player pre-installed.

- You can use VLC to play video and audio files. 

- VLC uses hardware acceleration in Raspberry Pi OS, and supports many popular audio and video file formats.

### VLC media player

#### `vlc` CLI

- You can also launch VLC from the command line.

- For the examples below, we used a short clip from Big Buck Bunny.

- To download this clip from Raspberry Pi, run the following command:

    ```sh
    $ wget --trust-server-names http://rptl.io/big-buck-bunny
    ```

- To play the clip in VLC from the command line, run the following command:

    ```sh
    $ vlc big-buck-bunny-1080p.mp4
    ```

- To prevent the VLC GUI staying open after your file has finished playing, add the `--play-and-exit` flag:

    ```sh
    $ vlc --play-and-exit big-buck-bunny-1080p.mp4
    ```

- To play a video in fullscreen mode (which can result in smoother playback in some circumstances), add the `--fullscreen` flag:

    ```sh
    $ vlc --play-and-exit --fullscreen big-buck-bunny-1080p.mp4
    ```

#### Use `cvlc` to play media without a GUI

- If you use `cvlc` instead of `vlc` with any of these commands, then the VLC GUI won’t be shown:

    ```shell
    $ cvlc --play-and-exit big-buck-bunny-1080p.mp4
    ```

### Specify an audio output device

- To force audio output to a particular device, pass the `alsa` value to the `-A` option to use ALSA audio output, and the `--alsa-audio-device` option to specify an audio output device:

    ```sh
    $ cvlc --play-and-exit -A alsa --alsa-audio-device <alsa-device> computer-startup-music.mp3
    ```

- Replace the `<alsa-device>` placeholder with one of the following options:

    |ALSA device|Description|
    |-|-|
    |`sysdefault:CARD=Headphones`|The headphone jack|
    |`sysdefault:CARD=vc4hdmi`|The HDMI output on a Raspberry Pi Zero, or Raspberry Pi Model 1, 2 or 3|
    |`sysdefault:CARD=vc4hdmi0`|The HDMI0 output on a Raspberry Pi 4, 5, 400, or Compute Module 4|
    |`sysdefault:CARD=vc4hdmi1`|The HDMI1 output on a Raspberry Pi 4, 5, 400, or Compute Module 4|

> ##### TIP
>
> - Use the following command to get a list of all ALSA devices on your Raspberry Pi:
>
> ```shell
> $ aplay -L | grep sysdefault
> ```

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

- Displays the current voltages used by the specific block.

- Accepts the following block values:

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

    ```sh
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

<br>

- On Linux, you can install python dependencies in two ways:

    - use `apt` to install pre-configured system packages

    - use `pip` to install libraries using Python’s dependency manager in a virtual environment

> ##### Important
>
> - Starting in Raspberry Pi OS Bookworm, you can only use `pip` to install into a Python Virtual Environment (`venv`).
>
> - This change was introduced by the Python community, not by Raspberry Pi: for more information, see PEP 668.

### Install Python packages using apt

- Packages installed via apt are packaged specifically for Raspberry Pi OS.

- These packages usually come pre-compiled, so they install faster.

- Because `apt` manages dependencies for all packages, installing with this method includes all of the sub-dependencies needed to run the package.

- And `apt` ensures that you don’t break other packages if you uninstall.

### Install Python libraries using pip

#### Bookworm changes to `pip` installation

- In older versions of Raspberry Pi OS, you could install libraries directly into the system version of Python using `pip`.

- Since Raspberry Pi OS Bookworm, users cannot install libraries directly into the system version of Python.

<br>

- Instead, install libraries into a virtual environment (`venv`).

- To install a library at the system level for all users, install it with `apt`.

<br>

- Attempting to install a Python package system-wide outputs an error similar to the following:

```sh
$ pip install buildhat
error: externally-managed-environment

× This environment is externally managed
To install Python packages system-wide, try apt install
  python3-xyz, where xyz is the package you are trying to
  install.

  If you wish to install a non-Debian-packaged Python package,
  create a virtual environment using python3 -m venv path/to/venv.
  Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
  sure you have python3-full installed.

  For more information visit http://rptl.io/venv

note: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.
hint: See PEP 668 for the detailed specification.
```

- Python users have long dealt with conflicts between OS package managers like `apt` and Python-specific package management tools like `pip`.

- These conflicts include both Python-level API incompatibilities and conflicts over file ownership.

<br>

- Starting in Raspberry Pi OS Bookworm, packages installed via `pip` *must be installed into a Python virtual environment* (`venv`).

- A virtual environment is a container where you can safely install third-party modules so they won’t interfere with your system Python.

#### Use `pip` with virtual environments

- To use a virtual environment, create a container to store the environment.

- There are several ways you can do this depending on how you want to work with Python:

##### per-project environments

- Many users create separate virtual environments for each Python project.

- Locate the virtual environment in the root folder of each project, typically with a shared name like `env`.

- Run the following command from the root folder of each project to create a virtual environment configuration folder:

    ```sh
    $ python -m venv env
    ```

- Before you work on a project, run the following command from the root of the project to start using the virtual environment:

    ```sh
    $ source env/bin/activate
    ```

- You should then see a prompt similar to the following:

    ```sh
    (env) $
    ```

- When you finish working on a project, run the following command from any directory to leave the virtual environment:

    ```sh
    (env) $ deactivate
    ```

##### Create a virtual environment

- Run the following command to create a virtual environment configuration folder, replacing `<env-name>` with the name you would like to use for the virtual environment (e.g. `env`):

    ```sh
    $ python -m venv <env-name>
    ```

> ##### Tip
>
> - Pass the `--system-site-packages` flag before the folder name to preload all of the currently installed packages in your system Python installation into the virtual environment.

##### Enter a virtual environment

- Then, execute the `bin/activate` script in the virtual environment configuration folder to enter the virtual environment:

    ```sh
    $ source <env-name>/bin/activate
    ```

- You should then see a prompt similar to the following:

    ```sh
    (<env-name>) $
    ```

- The `(<env-name>)` command prompt prefix indicates that the current terminal session is in a virtual environment named `<env-name>`.

<br>

- To check that you’re in a virtual environment, use `pip list` to view the list of installed packages:

    ```sh
    (<env-name>) $ pip list
    Package    Version
    ---------- -------
    pip        23.0.1
    setuptools 66.1.1
    ```

- The list should be much shorter than the list of packages installed in your system Python.

- You can now safely install packages with `pip`.

- Any packages you install with `pip` while in a virtual environment only install to that virtual environment.

- In a virtual environment, the `python` or `python3` commands automatically use the virtual environment’s version of Python and installed packages instead of the system Python.

##### Exit a virtual environment

- To leave a virtual environment, run the following command:

    ```sh
    (<env-name>) $ deactivate
    ```
