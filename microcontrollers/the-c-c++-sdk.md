# The C/C++ SDK

## SDK Setup

- For a full walk-through of how to get going with the C/C++ SDK, you should read our 'getting started' documentation.

- However, if you are intending to develop for Pico on a Raspberry Pi, then you can set up the C/C++ toolchain quickly by running our setup script from the command line.

> ##### Note
>
> - You should make sure the OS on your Raspberry Pi is up to date before running the setup script.

## Raspberry Pi Pico C/C++ SDK

- Our official C SDK can be used from the command line.

- To get started, download our C/C++ SDK and Examples, and take a look at our 'getting started' documentation to get going.

- Or for a quick setup see the next section.

- The SDK Github repository

- The Examples Github repository

- You can find documentation around the C/C++ SDK at;

    - Getting started with Raspberry Pi Pico

    - Connecting to the Internet with Raspberry Pi Pico W

    - Raspberry Pi Pico C/C++ SDK

    - API level documentation

> ##### Note
>
> - Documentation introducing working with Wi-Fi and Bluetooth on Raspberry Pi Pico W with c/C++ or MicroPython is presented in the Connecting to the Internet with Raspberry Pi Pico W book.

> ##### Note
>
> - If you are building applications with the C/C++ SDK for Raspberry Pi Pico W and, to connect to a network you will need to pass `-DPICO_BOARD=pico_w -DWIFI_SSID="Your Network" -DWIFI_PASSWORD="Your Password"` to CMake.
>
> - If you only need to enable Bluetooth support then you do not need to pass a SSID and password, but still need to pass the `-DPICO_BOARD=pico_w` string to CMake.

## Your First Binaries

### Blink an LED

- The Raspberry Pi Pico comes with a single LED on-board.

- The LED is connected to `WL_GPIO0` on the Infineon 43439 wireless chip for Pico W.

- You can blink this on and off by,

1. Download the Blink UF2 for Raspberry Pi Pico, or for Pico W.

2. Push and hold the BOOTSEL button and plug your Pico into the USB port of your Raspberry Pi or other computer.

3. It will mount as a Mass Storage Device called RPI-RP2.

4. Drag and drop the Blink UF2 binary onto the RPI-RP2 volume.

    - Pico will reboot.

- You should see the on-board LED blinking.

- You can see the code on Github for Raspberry Pi Pico W version.

### Say "Hello World"

- The next program anyone writes is to say 'Hello World' over a USB serial connection.

1. Download the 'Hello World' UF2

2. Push and hold the BOOTSEL button and plug your Pico into the USB port of your Raspberry Pi or other computer.

3. It will mount as a Mass Storage Device called RPI-RP2.

4. Drag and drop the 'Hello World' UF2 binary onto the RPI-RP2 volume.

    - Pico will reboot.

5. Open a Terminal window and type:

    ```sh
    # pacman -S minicom
    $ minicom -b 115200 -o -D /dev/ttyACM0
    ```

- You should see 'Hello, world!' printed to the Terminal.

- You can see the code on Github.

## Quick-start your own project

> ##### Note
>
> - The following instructions are terse, and Linux-based only.
>
> - For detailed steps, instructions for other platforms, and just in general, we recommend you see the Getting started with Raspberry Pi Pico and Raspberry Pi Pico C/C++ SDK books.

- Install CMake (at least version 3.13), and GCC cross compiler

    ```sh
    # apt install cmake arm-none-eabi-gcc arm-none-eabi-newlib libstdc++-arm-none-eabi-newlib
    ```

- Set up your project to point to use the Raspberry Pi Pico SDK by cloning the SDK locally:

    ```sh
    $ git clone https://github.com/raspberrypi/pico-sdk.git
    ```

- Copy `external/pico_sdk_import.cmake` from the SDK into your project directory.

- Set up your project to point to use the Raspberry Pi Pico SDK by cloning the SDK locally:

    ```sh
    $ git clone https://github.com/raspberrypi/pico-sdk.git
    ```

- Copy `external/pico_sdk_import.cmake` from the SDK into your project directory.

- Set `PICO_SDK_PATH` to the SDK location in your environment, or pass it (`-DPICO_SDK_PATH=`) to `cmake` later.

- Setup a `CMakeLists.txt` like:

    ```txt
    cmake_minimum_required(VERSION 3.13)

    # initialize the SDK based on PICO_SDK
    # note: this must happen before project()
    include(pico_sdk_import.cmake)

    project(my_project)

    # initialize the Raspberry Pi Pico SDK
    pico_sdk_init()

    # rest of your project
    ```

- Go ahead and write your code, see pico-examples or the Raspberry Pi Pico C/C++ SDK book for more information on how to go about that.

- About the simplest you can do is a single source file

    ```c
    #include <stdio.h>
    #include "pico/stdlib.h"

    int main() {
        setup_default_uart();
        printf("Hello, world!\n");
        return 0;
    }
    ```

- and add the following to your CMakeLists.txt

    ```cmake
    add_executable(hello_world
        hello_world.c
    )

    # Add pico_stdlib library which aggregates commonly used features
    target_link_libraries(hello_world pico_stdlib)

    # create map/bin/hex/uf2 file in addition to ELF.
    pico_add_extra_outputs(hello_world)
    ```

> ##### Note
>
> This example uses the default UART for stdout; if you want to use the default USB see the hello-usb example.

- Setup a CMake build directory.

- For example, if not using an IDE:

    ```sh
    $ mkdir build
    $ cd build
    $ cmake ..
    ```

- When building for a board other than the Raspberry Pi Pico, you should pass `-DPICO_BOARD=board_name` to the cmake command above, e.g. cmake `-DPICO_BOARD=pico_w ..` to configure the SDK and build options accordingly for that particular board.

- Doing so sets up various compiler defines (e.g. default pin numbers for UART and other hardware) and in certain cases also enables the use of additional libraries (e.g. wireless support when building for `PICO_BOARD=pico_w`) which cannot be built with out a board which provides the requisite functionality.

- For a list of boards defined in the SDK itself, look in this directory which has a header for each named board.

- Make your target from the build directory you created.

    ```sh
    $ make hello_world
    ```

- You now have `hello_world.elf` to load via a debugger, or `hello_world.uf2` that can be installed and run on your Raspberry Pi Pico via drag and drop.
