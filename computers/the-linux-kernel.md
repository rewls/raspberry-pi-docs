# The Linux kernel

## Introduction

- The Raspberry Pi kernel is hosted on GitHub; updates lag behind the upstream Linux kernel.

- The upstream kernel updates continuously, whereas Raspberry Pi integrates **long-term releases** of the Linux kernel into the Raspberry Pi kernel.

- We generate a `next` branch in raspberrypi/firmware for each long-term Linux kernel release.

- After extensive testing and discussion, we merge each next branch into the main branch of our repository.

### Update

- The usual Raspberry Pi OS update process automatically updates your kernel to the latest stable release.

- If you want to try the latest unstable test kernel, you can manually update.

## Build the kernel

- The default compilers and linkers distributed with an OS are configured to build executables to run on that OS.

- **Native builds** use these default compilers and linkers.

- **Cross-compilation** is the process of building code for a target other than the one running the build process.

<br>

- Cross-compilation of the Raspberry Pi kernel allows you to build a 64-bit kernel from a 32-bit OS, and vice versa.

- Alternatively, you can cross-compile a 32-bit or 64-bit Raspberry Pi kernel from a device other than a Raspberry Pi.

<br>

- The instructions below are divided into native builds and cross-compilation.

- Choose the section appropriate for your situation; although the two processes share many steps, there are also some important differences.

### Download kernel source

- Before you can build for any target, you need the kernel source.

- To get the kernel source, you need Git.

- Next, download the source code for the latest Raspberry Pi kernel:

    ```sh
    $ git clone --depth=1 https://github.com/raspberrypi/linux
    ```

- This can take several minutes.

> ##### Tip
>
- The `git clone` command above downloads the current active branch, which we build Raspberry Pi OS images from, without any history.
>
> - Omit `--depth=1` to download the entire repository, including the full history of all branches.
>
> - This takes much longer and occupies much more storage.
>
> <br>
>
> - To download a different branch with no history, add the `--branch` option to the command above, replacing `<branch>` with the name of the branch you wish to download:
>
> ```sh
> $ git clone --depth=1 --branch <branch> https://github.com/raspberrypi/linux
> ```
>
> - For a full list of available branches, see the the Raspberry Pi kernel repository.

- Now that you have the kernel source, build a fresh kernel natively or via cross-compilation.

### Cross-compile the kernel

- First, you will need a suitable Linux cross-compilation host.

- We tend to use Ubuntu; since Raspberry Pi OS is also a Debian distribution, compilation commands are similar.

#### Install required dependencies and toolchain

- To build the sources for cross-compilation, install the required dependencies onto your device.

- Run the following command to install most dependencies:

    ```sh
    $ sudo apt install bc bison flex libssl-dev make libc6-dev libncurses5-dev
    ```

    > ##### Arch Linux
    >
    > ```sh
    > $ sudo pacman -S bc bison flex openssl make glibc ncurses
    > ```

- Then, install the proper toolchain for the kernel architecture you wish to build:

    - To install the 64-bit toolchain to build a 64-bit kernel, run the following command:

        ```sh
        $ sudo apt install crossbuild-essential-arm64
        ```

        > ##### Arch Linux
        >
        > ```sh
        > $ sudo pacman -S aarch64-linux-gnu-gcc aarch64-linux-gnu-binutils
        > ```

    - To install the 32-bit toolchain to build a 32-bit kernel, run the following command:

        ```sh
        sudo apt install crossbuild-essential-armhf
        ```

        > ##### Arch Linux
        >
        > ```sh
        > $ curl -LO https://developer.arm.com/-/media/Files/downloads/gnu/13.3.rel1/binrel/arm-gnu-toolchain-13.3.rel1-x86_64-arm-none-linux-gnueabihf.tar.xz
        > $ su
        > # mkdir /opt/toolchain
        > # cd /opt/toolchain
        > # tar xfJ /path/to/arm-gnu-toolchain-13.3.rel1-x86_64-arm-none-linux-gnueabihf.tar.xz
        > $ echo "export PATH=\"/opt/toolchain/arm-none-linux-gnueabihf/bin:$PATH\"" >> ~/.bashrc
        > ```

#### Build configuration

- This section describes how to apply the default configuration when you build a kernel.

- You can also configure your kernel in the following ways:

    - enable and disable kernel features

    - apply patches from another source

- Enter the following commands to build the sources and Device Tree files:

    |Target Architecture|Target Model|
    |-|-|
    |64-bit|Raspberry Pi Zero 2 W|

    ```sh
    $ cd linux
    $ KERNEL=kernel8
    $ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2711_defconfig
    ```

    |Target Architecture|Target Model|
    |-|-|
    |32-bit|Raspberry Pi Zero|
    |32-bit|Raspberry Pi Zero W|

    ```sh
    $ cd linux
    $ KERNEL=kernel
    $ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcmrpi_defconfig
    ```

    |Target Architecture|Target Model|
    |-|-|
    |32-bit|Raspberry Pi Zero 2 W|

    ```sh
    $ cd linux
    $ KERNEL=kernel7
    $ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
    ```
    
#### Customise the kernel version using `LOCALVERSION`

- To prevent the kernel from overwriting existing modules in `/lib/modules` and to clarify that you run your own kernel in `uname` output, adjust `LOCALVERSION`.

<br>

- To adjust `LOCALVERSION`, change the following line in `.config`:

    ```sh
    CONFIG_LOCALVERSION="-v7l-MY_CUSTOM_KERNEL"
    ```

> ##### Tip
>
> - You can also change this setting graphically with `menuconfig` at **General setup** > **Local version - append to kernel release**.
>
> - For more information about `menuconfig`, see the kernel configuration instructions.

#### Build

- Run the following command to build a 64-bit kernel:

    ```sh
    $ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules dtbs
    ```

- Run the following command to build a 32-bit kernel:

    ```sh
    $ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs
    ```

#### Install the kernel

- Having built the kernel, you need to copy it onto your Raspberry Pi boot media (likely an SD card or SSD) and install the modules.

##### Find your boot media

- First, run `lsblk`.

- Then, connect your boot media.

- Run `lsblk` again; the new device represents your boot media.

- You should see output similar to the following:

    ```
    sdb
       sdb1
       sdb2
    ```

- If sdb represents your boot media, `sdb1` represents the the FAT32-formatted boot partition and sdb2 represents the (likely ext4-formatted) root partition.

<br>

- First, mount these partitions as `mnt/boot` and `mnt/root`, adjusting the partition letter to match the location of your boot media:

```sh
$ mkdir mnt
$ mkdir mnt/boot
$ mkdir mnt/root
$ sudo mount /dev/sdb1 mnt/boot
$ sudo mount /dev/sdb2 mnt/root
```

##### Install

- Next, install the kernel modules onto the boot media:

    - For 64-bit kernels:

        ```sh
        $ sudo env PATH=$PATH make -j12 ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=mnt/root modules_install
        ```

    - For 32-bit kernels:

        ```sh
        $ sudo env PATH=$PATH make -j12 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=mnt/root modules_install
        ```

> ##### Tip
>
> - On multi-core devices, the `make -j<n>` option distributes work between cores.
>
> - This can speed up compilation significantly.
>
> - Run `nproc` to see how many processors you have; we recommend passing a number 1.5x your number of processors.

- Next, install the kernel and Device Tree blobs into the boot partition, backing up your original kernel.

<br>

- To install the 64-bit kernel:

    - Run the following commands to create a backup image of the current kernel, install the fresh kernel image, overlays, README, and unmount the partitions:

        ```sh
        $ sudo cp mnt/boot/$KERNEL.img mnt/boot/$KERNEL-backup.img
        $ sudo cp arch/arm64/boot/Image mnt/boot/$KERNEL.img
        $ sudo cp arch/arm64/boot/dts/broadcom/*.dtb mnt/boot/
        $ sudo cp arch/arm64/boot/dts/overlays/*.dtb* mnt/boot/overlays/
        $ sudo cp arch/arm64/boot/dts/overlays/README mnt/boot/overlays/
        $ sudo umount mnt/boot
        $ sudo umount mnt/root
        ```

- To install the 32-bit kernel:

    1. Run the following commands to create a backup image of the current kernel and install the fresh kernel image:

        ```sh
        $ sudo cp mnt/boot/$KERNEL.img mnt/boot/$KERNEL-backup.img
        $ sudo cp arch/arm/boot/zImage mnt/boot/$KERNEL.img
        ```

    2. Depending on your kernel version, run the following command to install Device Tree blobs:

        - For kernels up to version 6.4:

            ```sh
            $ sudo cp arch/arm/boot/dts/*.dtb mnt/boot/
            ```

        - For kernels version 6.5 and above:

            ```sh
            $ sudo cp arch/arm/boot/dts/broadcom/*.dtb mnt/boot/
            ```

    3. Finally, install the overlays and README, and unmount the partitions:

        ```sh
        $ sudo cp arch/arm/boot/dts/overlays/*.dtb* mnt/boot/overlays/
        $ sudo cp arch/arm/boot/dts/overlays/README mnt/boot/overlays/
        $ sudo umount mnt/boot
        $ sudo umount mnt/root
        ```

- Finally, connect the boot media to your Raspberry Pi and connect it to power to run your freshly-compiled kernel.

> ##### Tip
>
> - Alternatively, copy the kernel with a different filename (e.g. `kernel-myconfig.img`) instead of overwriting the `kernel.img` file.
>
> - Then, edit `config.txt` in the boot partition to select your kernel:
>
>   ```
>   kernel=kernel-myconfig.img
>   ```
>
> - Combine this approach with a custom LOCALVERSION to keep your custom kernel separate from the stock kernel image managed by the system.
>
> - With this arrangement, you can quickly revert to a stock kernel in the event that your kernel cannot boot.
