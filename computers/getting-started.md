# Getting started

- To get started with your Raspberry Pi, you'll need the following:

    - a power supply

    - boot media (e.g. a microSD card with ample storage and speed)

- You can set up your Raspberry Pi as an interactive computer with a desktop, or as a *headless* computer accessible only over the network.

- To set your Raspberry Pi up headless, you don't need any additional peripherals: you can preconfigure a hostname, user account network connection, and SSH when install an operating system.

- If you want to use your Raspberry Pi directly, you'll need the following additional accessories:

    - a display

    - a cable to connect your Raspberry Pi to your display

    - a keyboard

    - a mouse

## Getting started with your Raspberry Pi

### Power supply

- The following table shows the USB-PD power mode required to power various Raspberry Pi models.

|Model|Recommended power supply (voltage/current)|Raspberry Pi power supply|
|-|-|-|
|Raspberry Pi 5|5V/5A, 5V/3A limits peripherals to 600mA|27W USB-C power supply|
|Raspberry Pi 4 Model B|5V/3A|15W USB-C power supply|
|Raspberry Pi Zero (all models)|5V/2.5A|12.5W Micro USB power supply|

- Plug your power supply into the port marked "POWER IN","PWR IN", or "PWR".

### Boot Media

- Raspberry Pi models lack onboard storage, so you have to supply it.

- You can boot your Raspberry Pi from an operating system image installed on any supported media: microSD cards are used commonly, but USB storage, network storage, and storage connected via a PCIe HAT are also available.

- However, only recent Raspberry Pi models support all of these media types.

- All Raspberry Pi consumer models since the Raspberry Pi 1 Model A+ feature a MicroSD slot.

- Your Raspberry Pi automatically boots from the microSD slot when the slot contains a card.

#### Recommended SD cards

- We recommend using an SD card with at least 32GB of storage for Raspberry Pi OS installations.

- For Raspberry Pi OS Lite, we recommend at least 16GB.

- You can use any SD card with a capacity of less than 2TB.

- Capacities above 2TB are currently not supported due  to limitations in the MBR.

- Because of a hardward limitation, the following devices will only boot from a boot partition of 256GB or less:

    - Raspberry Pi Zero

### Keyboard

- You can use any of the USB ports on your Raspberry Pi to connect a wired keyboard or USB Bluetooth receiver.

### Mouse

- You can use any of the USB ports on your Raspberry Pi to connect a wired mouse or USB Bluetooth receiver.

### Display

- Raspberry Pi models have the following display connectivity:

|Model|Display outputs|
|-|-|
|Raspberry Pi 5|2x micro HDMI|
|Raspberry Pi 4 (all models)|2x micro HDMI, audio and composite out via 3.5mm TRRS jack|
|Raspberry Pi Zero (all models)|mini HDMI|

- If your Raspberry Pi has more then one HDMI port, plug your primary monitor into the port marked `HDMI0`.

### Audio

- All Raspberry Pi models with HDMI, micro HDMI, or mini HDMI support audio output over HDMI.

- All Raspberry Pi models support audio over USB.

- All Raspberry Pi models equipped with Bluetooth support Bluetooth audio.

- All variants of the Raspberry Pi 1, 2, 3, and 4 include a 3.5mm auxiliary TRRS jack, which may require amplification for sufficient output volume.

### Networking

- The following Raspberry Pi models come with Wi-Fi and Bluetooth connectivity:

    - Raspberry Pi 5

    - Raspberry Pi 4

    - Raspberry Pi Zero W

- The "Model B" suffix indicates variants with an Ethernet port; "Model A" indicates no Ethernet port.

- If your Raspberry Pi doesn't have an Ethernet port, you can still connect to a wired internet connection using a USB-to-Ethernet adapter.

## Install an operating system

- To use your Raspberry Pi, you'll need an operating system.

- By default, Raspberry Pis check for an operating system on any SD card inserted in the SD card slot.

- Depending on your Raspberry Pi model, you can also boot an operating system from other storage devices, including USB drives, storage connected via a HAT, and network storage.

- To install an operating system on a storage device for your Raspberry Pi, you’ll need:

    - a computer you can use to image the storage device into a boot device

    - a way to plug your storage device into that computer

- We recommend installing an operating system using Raspberry Pi Imager.

- Raspberry Pi Imager is a tool that helps you download and write images on macOS, Windows, and Linux. 

- You can use Imager to preconfigure credentials and remote access settings for your Raspberry Pi.

### Install using Imager

- You can install Imager in the following ways:

    - Download the latest version from raspberrypi.com/software and run the installer.

    - Install it from a terminal using your package manager.

- Once you’ve installed Imager, launch the application by running rpi-imager.

- Click **Choose device** and select your Raspberry Pi model from the list.

- Next, click **Choose OS** and select an operating system to install.

- Connect your preferred storage device to your computer.

- Then, click **Choose storage** and select your storage device.

- Next, click **Next**.

- In a popup, imager will ask you to apply OS curstomisation.

- We strongly recommend configuring your Raspberry Pi via the OS customisation settings.

- Click the **Edit Settings** button to open OS customisation.

- If you don't configure your Raspberry Pi via OS customisation settins, Raspberry Pi OS will ask you for the same information at first boot during the configuration wizard. You can click the **No** button to skip OS customisation.

#### OS customisation

- You can preconfigure:

    - a username and password

    - Wi-Fi credentials

    - the device hostname

    - the time zone

    - your keyboard layout

    - remote connectivity

- When you first open the OS customisation menu, you might see a prompt asking for permission to load Wi-Fi credentials from your host computer.

- If you respond "yes", Imager will prefill Wi-Fi credentials from the network you're currently connected to.

- The **hostname** option defines the hostname your Raspberry Pi broadcasts to the network using mDNS.

    - When you connect your Raspberry Pi to your network, other devices on the network can communicate with your computer using `<hostname>.local` or `<hostname>.lan`.

- The **username and password** option defines the username and password of the admin user account on your Raspberry Pi.

- The **wireless LAN** option allows you to enter an SSID (name) and password for your wireless network.

    - By default, Imager uses the country you're currently in as the "Wireless LAN country".

    - This setting controls the Wi-Fi broadcast frequencies used by your Raspberry Pi.

    - Enter credentials for the wireless LAN option if you plan to run a headless Raspberry Pi.

- The **locale settings** option allows you to define the time zone and default keyboard layout for your Pi.

- The **Services** tab includes settings to help you connect your Raspberry Pi remotely.

- If you plan to use your Raspberry Pi remotely over your network, check the box next to **Enable SSH**.

    - Choose the **password authentication** option to SSH into your Raspberry Pi over the network using the username and password you provided in the general tab of OS customisation.

    - Choose **Allow public-kkey authentication only** to preconfigure your Raspberry Pi for passwordless public-key SSH authentication using a private key from the computer you're currently using.

    - If already have an RSA key in your SSH configuration, Imager uses that public key.

    - If you don't, you can click **Run SSH-keygen** to generate a public/private key pair.

    - Imager will use the newly-generated public key.

- OS customisation also includes an **Options** menu that allows you to configure the behaviour of Imager during a write.

####  Write

- Click **Save** to save your customisation.

### Install over the network

- Network Install enables a Raspberry Pi to install an operating system on a storage device using a version of Raspberry Pi Imager downloaded over the network.

- Network

- Network Install only runs on Raspberry Pi 4, 400, and 5.

- If your Raspberry Pi runs an older bootloader, you may need to update the bootloader to use Network Install.

- Network Install requires the following:

    - a compatible Raspberry Pi model running firmware that supports Network Install

    - a monitor

    - a keyboard

    - a wired internet connection

- To launch Network Install, power on your Raspberry Pi while pressing and holding the SHIFT key in the following configuration:

    - no bootable storage device

    - attached keyboard

    - attached compatible storage device, such as an SD card or USB storage

- Once you’re connected to the internet, your Raspberry Pi will download Raspberry Pi installer.

- If the download fails, you can repeat the process to try again.

- Once you finish downloading Raspberry Pi Installer, your Raspberry Pi will automatically start Raspberry Pi Imager.

- For more information about Network Install configuration, see HTTP boot.

## Set up your Raspberry Pi

- After installing an operating system image, connect your storage device to your Raspberry Pi.

- First, unplug your Raspberry Pi’s power supply to ensure that the Raspberry Pi is powered down while you connect peripherals.

-  If you installed the operating system on a microSD card, you can plug it into your Raspberry Pi’s card slot now.

- If you installed the operating system on any other storage device, you can connect it to your Raspberry Pi now.

- Then, plug in any other peripherals, such as your mouse, keyboard, and monitor.

- Finally, connect the power supply to your Raspberry Pi.

## Configuration on first boot

- If your Raspberry Pi does not boot within 5 minutes, check the status LED.

- If it’s flashing, see the LED warning flash codes for more information.

- If your Pi refuses to boot, try the following mitigation steps:

    - if you used a boot device other than an SD card, try booting from an SD card

    - re-image your SD card; be sure to complete the entire verify step in Imager

    - update the bootloader on your Raspberry Pi, then re-image your SD card

- You need a monitor and keyboard to navigate through the wizard; a mouse is optional.

- Bluetooth

    - This process works with built-in or external USB Bluetooth adapters. If you use a USB adapter, plug it in before booting your Raspberry Pi.

- Locale

- User

- Wi-Fi

- Browser

- Software updates

- Finish

## Next steps

- Recommended software

    - Raspberry Pi OS comes with many essential applications pre-installed so you can start using them straight away.

    - If you’d like to take advantage of other applications we find useful, click the raspberry icon in the top left corner of the screen.

    - Select Preferences > Recommended Software from the drop-down menu, and you’ll find the package manager.

- Tutorials

- Support

    - For support with official Raspberry Pi products, or to connect with other Raspberry Pi users, visit the Raspberry Pi forums.

- Further reading 

    - You can find more information on how get started with your Raspberry Pi in the latest edition of Official Raspberry Pi Beginners Guide by Gareth Halfacree.
