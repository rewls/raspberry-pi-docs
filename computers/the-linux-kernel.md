# The Linux kernel

## Kernel

- The Raspberry Pi kernel is hosted on GitHub; updates lag behind the upstream Linux kernell.

### Updating your kernel

- If you use the standard Raspberry Pi OS update and upgrade process, this will automatically update the kernel to the latest stable version of our kernel

- You may wish to update to the latest "bleeding edge" or test kernel.

### Getting your code into the kernel

- Initially, you should fork the Linux repository and clone that on your build system; this can be either on your Raspberry Pi or on a Linux machine you're using for cross-compiling.

- You can then make your changes, test them, and commit them into your fork.

## Building the kernel

- The default compilers and linkers that come with an OS are configured to build executables to run on that OS.

- A cross-compiler is configured to build code for a target other than the one running the build process, and using it is called cross-compilation.

- The instructions below are divided into native builds and cross-compilation.

### Building the kernel locally

> ##### Important
>
> - Building the 64-bit kernel on the 32-bit distribution of Raspberry Pi OS is a cross-compilation exercise because it requires the installation of the cross-compiler (`gcc-aarch64-linux-gnu`).
>
> - If you're running the 32-bit distribution of Raspberry Pi OS on Pi 4B, Pi 400, CM4 or CM4S then you'll be running a 32-bit userland, and 64-bit kernel -- so if you want to explicitly build a 32-bit kernel you should set `ARCH=arm`, and to boot this kernel you'll need to set `arm_64bit=0` in `config.txt`.

- On a Raspberry Pi, first install the latest version of Raspberry Pi OS.

- Install Git and the build dependencies:

    ```shell
    $ sudo apt install git bc bison flex libssl-dev make
    ```

- Next get the sources, which will take some time:

```shell
$ git clone --depth=1 https://github.com/raspberrypi/linux
```

#### Choosing sources

- The `git clone` command above will download the current active branch without any history.

- Omitting the `--depth=1` will download the entire repository, including the full history of all branches, but this takes much longer and occupies much more storage.

#### Kernel configuration

##### Apply the default configuration

- First, prepare the default configuration depending on your Raspberry Pi model.

- For Raspberry Pi 3, 3+, 4, 400 and Zero 2 W, and Raspberry Pi Compute Modules 3, 3+ and 4 default 64-bit build configuration:

    ```shell
    $ cd linux
    $ KERNEL=kernel8
    $ make bcm2711_defconfig
    ```

- For Raspberry Pi 5 default 64-bit build configuration:

    ```shell
    $ cd linux
    $ KERNEL=kernel_2712
    $ make bcm2712_defconfig
    ```

##### Customising the kernel version using `LOCALVERSION`

- In addition to your kernel configuration changes, you may wish to adjust `LOCALVERSION` to ensure your new kernel does not receive the same version string as the upstream kernel.

- This clarifies that you are running your own kernel in the output of `uname`, and ensures the existing modules in `/lib/modules` are not overwritten.

- To adjust `LOCALVERSION`, change the following line in `.config`:

    ```
    CONFIF_LOCALVERSION="-v7l-MY_CUSTOM_KERNEL"
    ```

#### Building the kernel

- Build and install the kernel, modules, and Device Tree blobs.

- For the 64-bit kernel:

```shell
$ make -j4 Image.gz modules dtbs
$ sudo make modules_install
$ sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
$ sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/firmware/overlays/
$ sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/
$ sudo cp arch/arm64/boot/Image.gz /boot/firmware/$KERNEL.img
```

> ##### Note
>
> - On Raspberry Pi 2,3,4 and 5, the `-j4` flag splits the work between all four cores, speeding up compilation significantly.

- If you now reboot, your Raspberry Pi should be running your freshly-compiled kernel.

### Cross-compiling the kernel

- First, you will need a suitable Linux cross-compilation host.

- We tend to use Ubuntu.

#### Install required dependencies and toolchain

```shell
$ sudo apt install git bc bison flex libssl-dev make libc6-dev libncurses5-dev
```

##### Install the 64-bit toolchain for a 64-bit kernel

```shell
$ sudo apt install crossbuild-essential-arm64
```

#### Get the kernel sources

```shell
$ git clone --depth=1 https://github.com/raspberrypi/linux
```

#### Build sources

##### 64-bit configs

- For Raspberry Pi 3, 3+, 4, 400 and Zero 2 W, and Raspberry Pi Compute Modules 3, 3+ and 4:

    ```shell
    $ cd linux
    $ KERNEL=kernel8
    $ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2711_defconfig
    ```

- For Raspberry Pi 5:

    ```shell
    cd linux
    KERNEL=kernel_2712
    make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2712_defconfig
    ```

> ##### Note
>
> - The standard, `bcm2711_defconfig`-based kernel (`kernel8.img`) also runs on Raspberry Pi 5.
>
> - For best performance you should use `kernel_2712.img`, but for situation where a 4KB page size is required then `kernel8.img` should be used.

##### Build with configs

> ##### Note
>
> - To speed up compilation on multiprocessor systems, and get some improvement on single-processor device, use `-j n`, where n is the number of processors × 1.5.
>
> - You can use the `nproc` command to see how many processors you have.

###### For all 64-bit builds

```shell
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules dtbs
```

#### Install directly onto the SD card

- Having built the kernel, you need to copy it onto your Raspberry Pi and install the modules.

- First, use `lsblk` before and after plugging in your SD card to identify it.

- You should end up with something a lot like this:

    ```
    sdb
       sdb1
       sdb2
    ```

- with `sdb1` being the `FAT` filesystem (boot) partition, and `sdb2` being the `ext4` filesystem (root) partition.

- Mount these first, adjusting the partition letter as necessary:

    ```shell
    $ mkdir mnt
    $ mkdir mnt/fat32
    $ mkdir mnt/ext4
    $ sudo mount /dev/sdb1 mnt/fat32
    $ sudo mount /dev/sdb2 mnt/ext4
    ```

- Next, install the kernel modules onto the SD card:

##### For 64-bit

```shell
sudo env PATH=$PATH make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=mnt/ext4 modules_install
```
- Finally, copy the kernel and Device Tree blobs onto the SD card, making sure to back up your old kernel:

##### For 64-bit

```shell
$ sudo cp mnt/fat32/$KERNEL.img mnt/fat32/$KERNEL-backup.img
$ sudo cp arch/arm64/boot/Image mnt/fat32/$KERNEL.img
$ sudo cp arch/arm64/boot/dts/broadcom/*.dtb mnt/fat32/
$ sudo cp arch/arm64/boot/dts/overlays/*.dtb* mnt/fat32/overlays/
$ sudo cp arch/arm64/boot/dts/overlays/README mnt/fat32/overlays/
$ sudo umount mnt/fat32
$ sudo umount mnt/ext4
```

- You can then edit the config.txt file to select the kernel that the Raspberry Pi will boot:

    ```
    kernel=kernel-myconfig.img
    ```

- This has the advantage of keeping your custom kernel separate from the stock kernel image managed by the system and any automatic update tools, and allowing you to easily revert to a stock kernel in the event that your kernel cannot boot.

- Finally, plug the card into the Raspberry Pi and boot it.

## Configuring the kernel

- Configuration is most commonly done through the make menuconfig interface.

- Alternatively, you can modify your .config file manually.

### Preparing to configure

- The menuconfig tool requires the ncurses development headers to compile properly.

```shell
$ sudo apt install libncurses5-dev
```

### Using `menuconfig`

```shell
$ make menuconfig
```

- if you are cross-compiling a 64-bit kernel:

```shell
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
```

- Use the arrow keys to navigate, the Enter key to enter a submenu (indicated by `--->`), Escape twice to go up a level or exit, and the space bar to cycle the state of an option.

- Some options have multiple choices, in which case they’ll appear as a submenu and the Enter key will select an option.

- You can press h on most entries to get help about that specific option or menu.

## Patching the kernel

- Patchsets are often provided with newer hardware as a temporary measure, before the patches are applied to the upstream (mainline) Linux kernel and then propagated down to the Raspberry Pi kernel sources.

- However, patchsets for other purposes exist, for instance to enable a fully preemptible kernel for real-time usage.

### Version identification

- In a kernel source directory, running `head Makefile -n 3` will show you the version the sources relate to:

    ```
    VERSION = 6
    PATCHLEVEL = 1
    SUBLEVEL = 38
    ```

- In this instance, the sources are for a 6.1.38 kernel.

- You can see what version you’re running on your system with the `uname -r` command.

### Applying patches

- How you apply patches depends on the format in which the patches are made available.

- Most patches are a single file, and are applied with the `patch` utility.

- To download and patch our example kernel version with the real-time kernel patches:

    ```shell
    $ wget https://www.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.38-rt13-rc1.patch.gz
    $ gunzip patch-6.1.38-rt13-rc1.patch.gz
    $ cat patch-6.1.38-rt13-rc1.patch | patch -p1
    ```

- Some patchsets come as mailbox-format patchsets, arranged as a folder of patch files.

```shell
$ git am -3 /path/to/patches/*
```

- Some patchsets will require a specific commit to patch against; follow the details provided by the patch distributor.

## Kernel headers

- If you are compiling a kernel module or similar, you will need the Linux kernel headers.

- These provide the various function and structure definitions required when compiling code that interfaces with the kernel.

- If you have cloned the entire kernel from GitHub, the headers are already included in the source tree.

- If you don’t need all the extra files, it is possible to install only the kernel headers from the Raspberry Pi OS repo.

- If you are using the 64-bit version of Raspberry Pi OS, run:

    ```shell
    $ sudo apt install linux-headers-rpi-v8
    ```

- When a new kernel release is made, you will need the headers that match that kernel version.

- It can take several weeks for the repo to be updated to reflect the latest kernel version.

- If this happens, the best approach is to clone the kernel.
