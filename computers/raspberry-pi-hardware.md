# Raspberry Pi hardware

## Introduction

- Raspberry Pi makes computers in several different **series**:

    - The **flagship** series, often referred to by the shorthand "Raspberry Pi", offers high-performance hardware, a full Linux operating system, and a variety of common ports in a form factor roughly the size of a credit card.

    - The **Zero** series offers a full Linux operating system and essential ports at an affordable price point in a minimal form factor with low power consumption.

    - The **Compute Module** series, often referred to by the shorthand "CM", offers high-performance hardware and a full Linux operating system in a minimal form factor suitable for industrial and embedded applications.

        - Compute Module models feature hardware equivalent to the corresponding flagship models, but with fewer ports and no on-board GPIO pins. Instead, users should connect Compute Modules to a separate baseboard that provides the ports and pins required for a given application.

- Additionally, Raspberry Pi makes the Pico series of tiny, versatile microcontroller boards.

- Pico models do not run Linux or allow for removable storage, but instead allow programming by flashing a binary onto on-board flash storage.

### Zero series

- Models with the **H** suffix have header pins pre-soldered to the GPIO header.

- Models that lack the H suffix do not come with header pins attached to the GPIO header; the user must solder pins manually or attach a third-party pin kit.

<br>

- All Zero models have the following connectivity:

    - a microSD card slot

    - a CSI camera port (version 1.3 of the original Zero introduced this port)

    - a mini HDMI port

    - 2 × micro USB ports (one for input power, one for external devices)

|Model|SoC|Memoy|GPIO|Wireless Connectivity|
|-|-|-|-|-|
|Raspberry Pi Zero WH|BCM2835|514MB|40-pin GPIO header|- 2.4GHz single-band 802.11n Wi-Fi (35Mb/s)<br>- Bluetooth 4.0, Bluetooth Low Energy (BLE)|
|Raspberry Pi Zero 2 WH|RP3A0|512MB|40-pin GPIO header|- 2.4GHz single-band 802.11n Wi-Fi (35Mb/s)<br>- Bluetooth 4.0, Bluetooth Low Energy (BLE)|

## GPIO and the 40-pin header

- You can find a 40-pin GPIO (general-purpose input/output) header on all current Raspberry Pi boards.

- The GPIO headers on all boards have a 0.1in (2.54mm) pin pitch.

> ##### Note
>
> - The header is unpopulated (has no headers) on Zero and Pico devices that lack the "H" suffix.

![GPIO-Pinout-Diagram-2](/images/GPIO-Pinout-Diagram-2.png)

- General Purpose I/O (GPIO) pins can be configured as either general-purpose input, general-purpose output, or as one of up to six special alternate settings, the functions of which are pin-dependent.

![GPIO](/images/GPIO.png)

> ##### Note
>
> - The GPIO pin numbering scheme is not in numerical order.
>
> - GPIO pins 0 and 1 are present on the board (physical pins 27 and 28), but are reserved for advanced use.

### Outputs

- A GPIO pin designated as an output pin can be set to high (3.3V) or low (0V).

### Inputs

- A GPIO pin designated as an input pin can be read as high (3.3V) or low (0V).

- This is made easier with the use of internal pull-up or pull-down resistors.

- Pins GPIO2 and GPIO3 have fixed pull-up resistors, but for other pins this can be configured in software.

### View a GPIO pinout for your Raspberry Pi

- A GPIO reference can be accessed on your Raspberry Pi by opening a terminal window and running the command `pinout`.

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

- In order to use the GPIO ports, your user must be a member of the `gpio` group.

- The default user account is a member by default, but you must add other users manually using the following command:

    ```sh
    $ sudo usermod -a -G gpio <username>
    ```

### GPIO pads

- The GPIO connections on the BCM2835 package are sometimes referred to in the peripherals data sheet as "pads" -- a semiconductor design term meaning "chip connection to outside world".

<br>

- The pads are configurable CMOS push-pull output drivers/input buffers.

- Register-based control settings are available for:

    - Internal pull-up / pull-down enable/disable

    - Output drive strength

    - Input Schmitt-trigger filtering

#### Power-on states

- All GPIO pins revert to general-purpose inputs on power-on reset.

- The default pull states are also applied, which are detailed in the alternate function table in the Arm peripherals datasheet.

- Most GPIOs have a default pull applied.

### Interrupts

- Each GPIO pin, when configured as a general-purpose input, can be configured as an interrupt source to the Arm.

- Several interrupt generation sources are configurable:

    - Level-sensitive (high/low)

    - Rising/falling edge

    - Asynchronous rising/falling edge

- Level interrupts maintain the interrupt status until the level has been cleared by system software (e.g. by servicing the attached peripheral generating the interrupt).

<br>

- The normal rising/falling edge detection has a small amount of synchronisation built into the detection.

- At the system clock frequency, the pin is sampled with the criteria for generation of an interrupt being a stable transition within a three-cycle window, i.e. a record of 1 0 0 or 0 1 1.

- Asynchronous detection bypasses this synchronisation to enable the detection of very narrow events.

### Alternative functions

- Almost all of the GPIO pins have alternative functions.

- Peripheral blocks internal to the SoC can be selected to appear on one or more of a set of GPIO pins, for example the I2C buses can be configured to at least three separate locations.

- Pad control, such as drive strength or Schmitt filtering, still applies when the pin is configured as an alternate function.

<br>

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

### Voltage specifications

- Two 5V pins and two 3.3V pins are present on the board, as well as a number of ground pins (GND), which can not be reconfigured.

- The remaining pins are all general-purpose 3.3V pins, meaning outputs are set to 3.3V and inputs are 3.3V-tolerant.

<br>

- The table below gives the various voltage specifications for the GPIO pins for BCM2835, BCM2836, BCM2837 and RP3A0-based products (e.g. Raspberry Pi Zero or Raspberry Pi 3+).

- For information about Compute Modules you should see the relevant datasheets.

|Symbol|Parameter|Conditions|Min|Typical|Max|Unit|
|-|-|-|-|-|-|-|
|V<sub>IL</sub>|Input Low Voltage|-|-|-|0.9|V|
|V<sub>IH</sub>|Input high voltage<sup>a</sup>|-|1.6|-|-|V|
|I<sub>IL</sub>|Input leakage current|TA = +85°C|-|-|5|µA|
|C<sub>IN</sub>|Input capacitance|-|-|5|-|pF|
|V<sub>OL</sub>|Output low voltage<sup>b</sup>|IOL = -2mA|-|-|0.14|V|
|V<sub>OH</sub>|Output high voltage<sup>b</sup>|IOL = -2mA|3.0|-|-|V|
|I<sub>OL</sub>|Output low voltage<sup>c</sup>|V0 = 0.4V|18|-|-|mA|
|I<sub>OH</sub>|Output high current<sup>c</sup>|VO = 2.3V|17|-|-|mA|
|R<sub>PU</sub>|Pullup resistor|-|50|-|65|kΩ|
|R<sub>PD</sub>|Pulldown resistor|-|50|-|65|kΩ|

- <sup>a</sup> Hysteresis enabled

- <sup>b</sup> Default drive strength (8mA)

- <sup>c</sup> Maximum drive strength (16mA)

## GPIO pads control

- GPIO drive strengths do not indicate a maximum current, but a maximum current under which the pad will still meet the specification.

- You should set the GPIO drive strengths to match the device being attached in order for the device to work correctly.

### Control drive strength

- Inside the pad are a number of drivers in parallel.

- If the drive strength is set low (`0b000`), most of these are tri-stated so they do not add anything to the output current.

- If the drive strength is increased, more and more drivers are put in parallel.

- The diagram shows that behaviour.

> ##### Warning
>
> - For Raspberry Pi 4, Raspberry Pi 400 and Compute Module 4 the current level is half the value shown in the diagram.

![GPIO drive strength diagram](/images/pi_gpio_drive_strength_diagram.png)

### Current value

- The current value specifies the maximum current under which the pad will still meet the specification.

<br>

- Current value is not the current that the pad will deliver, and is not a current limit.

<br>

- The pad output is a voltage source:

    - If set high, the pad will try to drive the output to the rail voltage (3.3V)

    - If set low, the pad will try to drive the output to ground (0V)

- The pad will try to drive the output high or low.

- Success will depend on the requirements of what is connected.

- If the pad is shorted to ground, it will not be able to drive high.

- It will try to deliver as much current as it can, and the current is only limited by the internal resistance.

<br>

- If the pad is driven high and it is shorted to ground, in due time it will fail.

- The same holds true if you connect it to 3.3V and drive it low.

<br>

- Meeting the specification is determined by the guaranteed voltage levels.

- Because the pads are digital, there are two voltage levels, high and low.

- The I/O ports have two parameters which deal with the output level:

    - V<sub>OL</sub>, the maximum low-level voltage (0.14V at 3.3V VDD IO)

    - V<sub>OH</sub>, the minimum high-level voltage (3.0V at 3.3V VDD IO)

- V<sub>OL</sub> = 0.14V means that if the output is Low, it will be <= 0.14V.

- V<sub>OH</sub> = 3.0V means that if the output is High, it will be >= 3.0V.

<br>

- As an example, a drive strength of 16mA means that if you set the pad high, you can draw up to 16mA, and the output voltage is guaranteed to be >= V<sub>OH</sub>.

- This also means that if you set a drive strength of 2mA and you draw 16mA, the voltage will not be V<sub>OH</sub> but lower.

- In fact, it may not be high enough to be seen as high by an external device.

<br>

- There is more information on the physical characteristics of the GPIO pins.

> ##### Note
>
> - On the Compute Module devices, it is possible to change the VDD IO from the standard 3.3V.
>
> - In this case, V<sub>OL</sub> and V<sub>OH</sub> will change according to the table in the GPIO section.

- The Raspberry Pi 3.3V supply was designed with a maximum current of ~3mA per GPIO pin.

- If you load each pin with 16mA, the total current is 272mA.

- The 3.3V supply will collapse under that level of load.

- Big current spikes will happen, especially if you have a capacitive load.

- Spikes will bounce around all the other pins near them.

- This is likely to cause interference with the SD card, or even the SDRAM behaviour.

### Safe current

- All the electronics of the pads are designed for 16mA.

- This is a safe value under which you will not damage the device.

- Even if you set the drive strength to 2mA and then load it so 16mA comes out, this will not damage the device.

- Other than that, there is no guaranteed maximum safe current.

### GPIO addresses

- 0x 7e10 002c PADS (GPIO 0-27)

- 0x 7e10 0030 PADS (GPIO 28-45)

- 0x 7e10 0034 PADS (GPIO 46-53)

|Bits|Field name|Description|Type|Reset|
|-|-|-|-|-|
|31:24|PASSWRD|Must be 0x5A when writing; accidental write protect password|W|0|
|23:5||**Reserved** - Write as 0, read as don’t care|||
|4|SLEW|Slew rate; 0 = slew rate limited; 1 = slew rate not limited|RW|0x1|
|3|HYST|Enable input hysteresis; 0 = disabled; 1 = enabled|RW|0x1|
|2:0|DRIVE|Drive strength, see breakdown list below|RW|0x3|

- Beware of Simultaneous Switching Outputs (SSO) limitations which are device-dependent as well as dependent on the quality and layout of the PCB, the amount and quality of the decoupling capacitors, the type of load on the pads (resistance, capacitance), and other factors beyond the control of Raspberry Pi.

### Drive strength list

- 0 = 2mA

- 1 = 4mA

- 2 = 6mA

- 3 = 8mA

- 4 = 10mA

- 5 = 12mA

- 6 = 14mA

- 7 = 16mA
