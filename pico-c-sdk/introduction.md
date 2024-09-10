# Introduction

## Raspberry Pi Pico SDK

- The Raspberry Pi Pico SDK (Software Development Kit), henceforth SDK, provides the headers, libraries and build system necessary to write programs for RP-series microcontroller devices such as the Rasberry Pi Pico in C, C++ or assembly language.

- The SDK is designed to provide an API (Application Programming Interface) and programming environment that is familiar both to non-embedded C developers and embedded C developers alike.

- A single program runs on the device at a time with a conventional `main()` method.

- Standard C/C++ libraries are supported along with APIs for accessing the microcontroller's hardware including DMA, IRQs, and the wide variety of fixed-function peripherals and PIO (Programmable IO).

- Additionally the SDK provides higher-level libraries for dealing with timers, USB, synchronization and multi-core programming, along with additional high-level functionality built using PIO, such as audio.

- The SDK can be used to build anything from simple applications, or full-fledged runtime environments such as MicroPython, to low-level software such as the microcontroller's on-chip bootrom itself.

- This documentation is generated from the SDK source tree using Doxygen.

- It provides basic information on the APIs used for each library, but does not provide usage information.

- Please refer to  the Databooks for usage and more technical information.

### SDK Design

- The intention within the SDK has been for features to just work out of the box, with sensible defaults, but also to give the developer as much control and power as possible to fine-tune every aspect of the application they are building and the libraries used.

### The Build System

- The SDK uses CMake to manage the build.

- The SDK builds an executable which is bare-metal, i.e. it includes the entirety of the code needed to run on the device.

### Examples

- This SDK documentation contains a number of example code fragments.

#### Examples Index

- This page links to the various example code fragments in this documentation.

- For more complete examples, please see the pico-examples repository, which contains complete buildable projects.

    - RTC example

    - UART example

    - ADC example

    - I2C example

    - Clock example

    - Timer example

    - Flash programming example

    - Watchdog example

    - Divider example

    - PWM example

    - Multicore example

    - Reset example
