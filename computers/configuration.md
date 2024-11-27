# Configuration

## Kernel command line (`cmdline.txt`)

- The Linux kernel accepts a collection of command line parameters during boot.

- On the Raspberry Pi, this command line is defined in a file in the boot partition, called `cmdline.txt`.

- You can edit this text file with any text editor.

```sh
$ sudo nano /boot/firmware/cmdline.txt
```

> ##### Important
>
> Put all parameters in `cmdline.txt` on the same line.
>
> - Do not use newlines.

- To view the command line passed to the kernel at boot time, run the following command:

    ```sh
    $ cat /proc/cmdline
    ```

- Because Raspberry Pi firmware makes changes to the command line before launching the kernel, the output of this command will not exactly match the contents of `cmdline.txt`.

### Command line options

- There are many kernel command line parameters, some of which are defined by the kernel itself.

- Others are defined by code that the kernel may be using, such as the Plymouth splash screen system.

#### Standard entries

- `console`

    - defines the serial console.

    - There are usually two entries:

        - `console=serial0,115200`

        - `console=tty1`

- `root`

    - defines the location of the root filesystem.

    - e.g. `root=/dev/mmcblk0p2` means multimedia card block 0 partition 2.

- `rootfstype`

    - defines what type of filesystem the rootfs uses, e.g. `rootfstype=ext4`.

- `quiet`

    - sets the default kernel log level to `KERN_WARNING`, which suppresses all but very serious log messages during boot.

## Set up a headless Raspberry Pi

- A **headless** Raspberry Pi runs without a monitor, keyboard, or mouse.

- To run a Raspberry Pi headless, you need a way to access it from another computer.

- To access your Raspberry Pi remotely, you'll need to connect your Raspberry Pi to a network, and a way to access the Raspberry Pi over that network.

<br>

- To connect your Raspberry Pi to network, you can either plug your device into a wired connection via Ethernet or configure wireless networking.

<br> 

- To access your Raspberry Pi over that network, use SSH.

<br>

- If you're setting up your Raspberry Pi from scratch, set up wireless networking and SSH during the imaging process.

- If you've already got a Raspberry Pi set up, you can configure SSH using `raspi-config`.

> ##### Warning
>
> - Depending on the model of Raspberry Pi and type of SD card you use, your Raspberry Pi may require up to five minutes to boot and connect to your wireless network the first time it boots.

### Connect to a wired network

- To connect to a wired network at first boot, plug your headless Raspberry Pi in via Ethernet, or use an Ethernet adaptor if your Raspberry Pi model does not include an Ethernet port.

- Your Raspberry Pi will automatically connect to the network.

### Connect to a wireless network

- To configure wireless network access at first boot in a headless Raspberry Pi, use the advanced settings menu in Raspberry Pi Imager.

- Enter the SSID and password of your preferred wireless network.

- Your Raspberry Pi will use these credentials to connect to the network on first boot.

- Some wireless adapters and some Raspberry Pi boards do not support 5GHz networks; check the documentation for your wireless module to ensure compatibility with your preferred network.

## `boot` folder contents

- Raspberry Pi OS stores boot files on the first partition of the SD card, formatted with the FAT file system.

<br>

- On startup, each Raspberry Pi loads various files from the boot partition in order to start up the various processors before the Linux kernel boots.

<br>

- On boot, Linux mounts the boot partition as `/boot/firmware/`.

> ##### Note
>
> - Prior to *Bookworm*, Raspberry Pi OS stored the boot partition at `/boot/`.
>
> - Since *Bookworm*, the boot partition is located at `/boot/firmware/`.

### `cmdline.txt`

- The kernel command line passed into the kernel at boot.

### `config.txt`

- Contains many configuration parameters for setting up the Raspberry Pi.

- For more information, see the `config.txt` documentation.

> ##### Important
>
> - Raspberry Pi 5 requires a non-empty `config.txt` file in the boot partition.

### Device Tree blob files (`*.dtb`)

- Device tree blob files contain the hardware definitions of the various models of Raspberry Pi.

- These files set up the kernel at boot based on the detected Raspberry Pi model.

### Kernel files (`*.img`)

- Various kernel image files that correspond to Raspberry Pi models:

    |Filename|Processor|Raspberry Pi model|Notes|
    |-|-|-|-|
    |`kernel.img`|BCM2835|Pi Zero, Pi 1||

> ##### Note
>
> - `lscpu` reports a CPU architecture of `armv7l` for systems running a 32-bit kernel, and `aarch64` for systems running a 64-bit kernel. The `l` in the `armv7l` case refers to little-endian CPU architecture, not `LPAE` as is indicated by the `l` in the `kernel7l.img` filename.

## LED warning flash codes

- If a Raspberry Pi fails to boot for some reason, or has to shut down, in many cases an LED will flash a specific number of times to indicate what happened.

- The LED will blink for a number of long flashes (0 or more), then produce short flashes, to indicate the exact status.

- In most cases, the pattern will repeat after a two-second gap.

|Long flashes|Short flashes|Status|
|-|-|-|
|4|6|Power failure type A|
|4|7|Power failure type B|

## Configure UARTs

- There are two types of UART available on the Raspberry Pi - PL011 and mini UART.

- The PL011 is a capable, broadly 16550-compatible UART, while the mini UART has a reduced feature set.

<br>

- All UARTs on the Raspberry Pi are 3.3V only - damage will occur if they are connected to 5V systems.

- An adapter can be used to connect to 5V systems.

- Alternatively, low-cost USB to 3.3V serial adapters are available from various third parties.

### Raspberry Pi Zero, 1, 2 and 3

- The Raspberry Pi Zero, 1, 2, and 3 each contain two UARTs as follows:

    |Name|Type|
    |-|-|
    |UART0|PL011|
    |UART1|mini UART|

### Primary UART

- On the Raspberry Pi, one UART is selected to be present on GPIO 14 (transmit) and 15 (receive) - this is the primary UART.

- By default, this will also be the UART on which a Linux console may be present.

- Note that GPIO 14 is pin 8 on the GPIO header, while GPIO 15 is pin 10.

### Secondary UART

- The secondary UART is not normally present on the GPIO connector.

- By default, the secondary UART is connected to the Bluetooth side of the combined wireless LAN/Bluetooth controller, on models which contain this controller.

### Primary and Secondary UART

- The following table summarises the assignment of UARTs on various Raspberry Pi devices:

    |Model|Primary/console|Secondary/Bluetooth|
    |-|-|-|
    |Raspberry Pi Zero W|UART1|UART0|

- Linux devices on Raspberry Pi OS:

    |Linux device|Description|
    |-|-|
    |`/dev/ttyS0`|mini UART|
    |`/dev/ttyAMA0`|first PL011 (UART0)|
    |`/dev/serial0`|primary UART|
    |`/dev/serial1`|secondary UART|

- `/dev/serial0` and `/dev/serial1` are symbolic links which point to either `/dev/ttyS0` or `/dev/ttyAMA0`.

- Due to changes in Bookworm, `/dev/serial1` does not exist by default.

- You can re-enable `serial1` by setting the following values in `config.txt`:

    ```
    dtparam=krnbt=off
    ```

> ##### Tip
>
> - This option may not work on all models in the future.
>
> - Only use this option if there is no other alternative for your use case.

### Enabling early console for Linux

- Although the Linux kernel starts the UARTs relatively early in the boot process, it is still long after some critical bits of infrastructure have been set up.

- A failure in those early stages can be hard to diagnose without access to the kernel log messages from that time.

- To enable `earlycon` support for one of the UARTs, add one of the following options to `cmdline.txt`, depending on which UART is the primary:

    - For Raspberry Pi 1, Pi Zero and Compute Module 1:

        ```
        earlycon=uart8250,mmio32,0x20215040
        earlycon=pl011,mmio32,0x20201000
        ```

- The baudrate defaults to 115200bps.

> ##### Note
>
> - Selecting the wrong early console can prevent the Raspberry Pi from booting.

## Device Trees, overlays, and parameters

- Raspberry Pi kernels and firmware use a Device Tree (DT) to describe hardware.

- These Device Tree may include DT parameters that to control onboard features.

- DT overlays allow optional external hardware to be described and configured, and they also support parameters for more control.

<br>

- The firmware loader (`start.elf` and its variants) is responsible for loading the DTB (Device Tree Blob - a machine-readable DT file).

- It chooses which one to load based on the board revision number, and makes modifications to further tailor it.

- This runtime customisation avoids the need for many DTBs with only minor differences.

<br>

- User-provided parameters in `config.txt` are scanned, along with any overlays and their parameters, which are then applied.

- The loader examines the result to learn which UART, if any, is to be used for the console.

- Finally it launches the kernel, passing a pointer to the merged DTB.

### Device Trees

- A Device Tree (DT) is a description of the hardware in a system.

- It should include the name of the base CPU, its memory configuration, and any peripherals (internal and external).

- A DT should not be used to describe the software, although by listing the hardware modules it does usually cause driver modules to be loaded.

> ##### Note
>
> - It helps to remember that DTs are supposed to be OS-neutral, so anything which is Linux-specific shouldn't be there.

- A Device Tree represents the hardware configuration as a hierarchy of nodes.

- Each node may contain properties and subnodes.

- Properties are named arrays of bytes, which may contain strings, numbers (big-endian), arbitrary sequences of bytes, and any combination thereof.

- By analogy to a filesystem, nodes are directories and properties are files.

- The locations of nodes and properties within the tree can be described using a path, with slashes as separators and a single slash to indicate the root.

#### Basic DTS syntax

- Device Trees are usually written in a textual form known as Device Tree Source (DTS), and are stored in files with a `.dts` suffix.

- DTS syntax is C-like, with braces for grouping and semicolons at the end of each line.

- Note that DTS requires semicolons after closing braces: think of C `struct`s rather than functions.

- The compiled binary format is referred to a sFlattened Device Tree (FDT) or Device Tree Blob (DTB), and is stored in `.dtb` files.

<br>

- The following is a simple tree in the `.dts` format:

```dts
/dts-v1/;
/include/ "common.dtsi";

/ {
    node1 {
        a-string-property = "A string";
        a-string-list-property = "first string", "second string";
        a-byte-data-property = [0x01 0x23 0x34 0x56];
        cousin: child-node1{
            first-child-property;
            second-child-property = <1>;
            a-string-property = "Hello, world";
        };
        child-node2 {
        };
    };
    node2 {
        an-empty-property;
        a-cell-property = <1 2 3 4>;
        child-node1 {
            my-cousin = <&cousin>;
        };
    };
};

/node2 {
    another-property-for-node2;
};
```

- This tree contains:

    - a required header: `/dts-v1/`

    - The inclusion of another DTS, file, conventionally named `*.dtsi` and 

    - a single root node: `/`

    - a couple of child nodes: `node1` and `node2`

    - some children for node1: `child-node1` and `child-node2`

    - a label (`cousin`) and a reference to thagt label (`&cousin`)

    - several properties scattered through the tree

    - a repeated node (`/node2`)

- Properties are simply key-value pairs where the value can either be empty or contain an arbitrary byte stream.

- While data types are not encoded in the data structure, there are a few fundamental data representations that can be expressed in a Device Tree source file.

- Text strings (NUL-terminated) are indicated with double quotes:

    ```dts
    string_property = "a string";
    ```

- Cells are 32-bit unsigned integers delimited by angle brackets:

    ```dts
    cell-property = <0xbeef 123 0xabcd1234>;
    ```

- Arbitrary byte data is delimited with square brackets, and entered in hex:

    ```dts
    binary-property = [01 23 45 67 89 ab cd ef];
    ```

- Data of differing representations can be concatenated using a comma:

    ```dts
    mixed-property = "a string", [01 23 45 67], <0x12345678>;
    ```

- Commas are also used to create lists of strings:

    ```dts
    string-list = "red fish", "blue fish";
    ```

#### An aside about `/include/`

- The `/include/` directive results in simple textual inclusion, much like C's `#include` directive, but a feature of the Device Tree compiler leads to different usage patterns.

- Given that nodes are named, potentially with absolute paths, it is possible for the same node to appear twice in a DTS file (and its inclusions).

- When this happens, the nodes and properties are combined, interleaving and overwriting properties as required (later values override earlier ones).

- In the example above, the second appearance of `/node2` causes a new property to be added to the original:

    ```dts
    /node2 {
        an-empty-property;
        a-cell-property = <1 2 3 4>;    /* each number (cell) is a uint32 */
        another-property-for-node2;
        child-node1 {
            my-cousin = <&cousin>;
        };
    };
    ```

- It is therefore possible for one `.dtsi` to overwrite, or provide defaults for, multiple places in a tree.

#### Labels and references

- It is often necessary for one part of the tree to refer to another, and there are four ways to do this:

##### Path strings

- Similar to filesystem paths, e.g. `/soc/i2s@7e203000` is the full path to the I2S device in BCM2835 and BCM2836.

- The standard APIs don't create paths to properties like `/soc/i2s@7e203000/status`: instead, you first find a node, then choose properties of that node.

##### Phandles

- A unique 32-bit integer assigned to a node in its `phandle` property.

- For historical reasons, you may also see a redundant, matching `linux,phandle`.

- Phandles are numbered sequentially, starting from 1; 0 is not a valid phandle.

- They are usually allocated by the DT compiler when it encounters a reference to a node in a integer context, usually in the form of a label.

- References to nodes using phandles are simply encoded as the corresponding integer (cell) values; there is no markup to indicate that they should be interpreted as phandles, as that is application-defined.

##### Labels

- Just as a label in C gives a name to a place in the code, a DT label assigns a name to a node in the hierarchy.

- The compiler takes references to labels and converts them into paths when used in string context (`&node`) and phandles in integer context (`<&node>`); the original labels do not appear in the compiled output. Note that labels contain no structure; they are just tokens in a flat, global namespace.

##### Aliases

- Similar to labels, except that they do appear in the FDT output as a form of index.

- They are stored as properties of the `/aliases` node, with each property mapping an alias name to a path string.

- Although the aliases node appears in the source, the path strings usually appears as references to labels (`&node`), tather then being written out in full.

- DT APIs that resolve a path string to a node typically look at the first character of the path, treating paths that do not start with a slash as aliases that must first be converted to a path using the `/aliases` table.

#### Device Tree semantics

- How to construct a Device Tree, and how best to sue it to capture the configuration of some hardware, is a large and complex subject.

- There are many resources available, some of which are listed below, but several points deserve highlighting:

    - `compatible` properties are the link between the hardware description and the driver software.

        - When an OS encounters a node with a `compatible` property, itlooks it up in its database of     device drivers to find the best match.

        - In Linux, this usually results in the driver module being automatically loaded, provided it has been appropriately labelled and not blacklisted.

    - The `status` property indicates whether a device is enabled or disabled.

        - If the `status` is `ok`, `okay` or obsent, then the device is enabled.

        - Otherwise, `status` should be `disabled`, so that the device is disabled.

        - It can be useful to place devices in a `.dtsi` file with the status set to `disabled`.

        - A derived configuration can then incldue that `.dtsi` and set the status for the devices which are needed to `okay`.

### Device Tree Overlays

- A modern System on a Chip (SoC) is a very complicated device; a complete Device Tree could be hundreds of lines long.

- Taking that one step further and placing the SoC on a board with other components only makes matters more complicated.

- To keep that manageable, particularly if there are related devices which share components, it makes sense to put the common elements in `.dtsi` files, to be included from possibly multiple `.dts` files.

<br>

- When a system like Raspberry Pi also supports optional plug-in accessories such as HATs, the problem grows.

- Ultimately, each possible configuration requires a Device Tree to describe it, but once you factor in all the different base models and the large number of available accessories, the number of combinations starts to multiply rapidly.

<br>

- What is needed is a way to describe these optional components using a partial Device Tree, and then to be able to build a complete tree by taking a base DT and adding a number of optional elements.

- You can do this, and these optional elements are called "overlays".

<br>

- Unless you want to learn how to write overlays for Raspberry Pis, you might prefer to skip on to Use Device Trees.

#### Fragments

- A DT overlay comprises a number of fragments, each of which targets one node and its subnodes.

- Although the concepts sounds simple enough, the syntax seems rather strange at first:

    ```dts
    // Enable the i2s interface
    /dts-v1/;
    /plugin/;

    / {
        compatible = "brcm,bcm2835";

        fragment@0 {
            target = <&i2s>;
            __overlay__ {
                status = "okay";
                test_ref = <&test_label>;
                test_label: test_subnode {
                    dummy;
                };
            };
        };
    };
    ```

- The `compatible` string identifies this as being for BCM2835, which is the base architecture for the RAspberry Pi SoCs; if the overlay makes use of features of a Raspberry Pi 4 then `brcm,bcm2711` is the correct value to use, otherwise `brcm,bcm2835` can be used for all Raspberry Pi overlays.

- Then comes the first fragment.

- Fragments should be numbered sequentially from zero.

- Failure to adhere to this may cause some or all of your framgnets to be missed.

<br>

- Each fragment consists of two parts: a `target` property, identifying the node to apply the overlay to; and the `__overlay__` itself, the body of which is added to the target node.

- The example above can be interpreted as if it were written like this:

```dts
/dts-v1/;
/plugin/;

/ {
    compatible = "brcm,bcm2835";
};

&i2s {
    status = "okay";
    test_ref = <&test_label>;
    test_label: test_subnode {
        dummy;
    };
};
```

- With a sufficiently new version of `dtc` you can write the example exactly as above and get identical output, but some homegrown tools don't understand this format yet.

- Any overlay that you might want to see included in the standard Raspberry Pi OS kernel should be written in the old format for now.

<br>

- The effect of merging that overlay with a standard Raspberry Pi base Device Tree (e.g. `bcm2708-rpi-b-plus.dtb`), provided the overlay is loaded afterwards, would be to enable the I2S interface by changing its status to `okay`.

- But if you try to compile this overlay using:

    ```sh
    $ dts -I dts -O dtb -o 2nd.dtbo 2nd-overlay.dts
    ```

- ...you will get an error:

    ```sh
    Label or path i2s not found
    ```

- This shouldn't be too unexpected, since there is no reference to the base `.dtb` or `.dts` file to allow the compiler to find the `i2s` label.

<br>

- Trying again, this time using the original example and adding the `-@` option to allow unresolved references (and `-Hepapr` to remove some clutter):

    ```sh
    $ dtc -@ -Hepapr -I dts -O dtb -o 1st.dtbo 1st-overlay.dts
    ```

- If `dtc` returns an error about the third line, it doesn't have the extensions required for overlay work.

- Run `sudo apt install device-tree-compiler` and try again - this time, compilation should complete successfully.

- Note that a suitable compiler is also available in the kernel tree as `scripts/dtc/dtc`, built when the `dtbs` make target is used:

    ```sh
    $ make ARCH=arm dtbs
    ```

- Dump the contents of the DTB files to see what the compiler has generated:

    ```sh
    $ fdtdump 1st.dtbo
    ```

- This should output something similar to the following:

```dts
/dts-v1/;
// magic:		0xd00dfeed
// totalsize:		0x207 (519)
// off_dt_struct:	0x38
// off_dt_strings:	0x1c8
// off_mem_rsvmap:	0x28
// version:		17
// last_comp_version:	16
// boot_cpuid_phys:	0x0
// size_dt_strings:	0x3f
// size_dt_struct:	0x190

/ {
    compatible = "brcm,bcm2835";
    fragment@0 {
        target = <0xffffffff>;
        __overlay__ {
            status = "okay";
            test_ref = <0x00000001>;
            test_subnode {
                dummy;
                phandle = <0x00000001>;
            };
        };
    };
    __symbols__  {
        test_label = "/fragment@0/__overlay__/test_subnode";
    }
    __fixups__ {
        i2s = "/fragment@0:target:0";
    };
    __local_fixups__ {
        fragment@0 {
            __overlay_ {
                test_ref = <0x00000000>
            };
        };
    };
};
```

- After the verbose description of the file structure there is our fragment.

- But look carefully - where we wrote `&i2s` it now says `0xffffffff`, a clue that something stranges has happened.

- The compiler has also added a `phandle` property containing a unique small integer to indicate that the node has a label, and replaced all references to the label with the same small integer.

<br>

- After the fragment there are three new nodes:

    - `__symbols__` lists the labels used in the overlay (`test_label` here), and the path to the labelled node.

        - This node is the key to how unresolved symbols are dealt with.

    - `__fixups__` contains a list of properties mapping the names of unresolved symbols to lists of paths to cells within the fragments that need patching with the phandle of the target node, once that target has been located.

        - In this case, the path is to the `0xffffffff` value of `target`, but fragments can contain other unresolved references which would require additional fixes.

    - `__local_fixups__` holds the locations of any references to labels that exist within the overlay - the `test_ref` property.

        - This is required because the program performing the merge will have to ensure that phandle numbers are sequential and unique.

- Back in Labels and references it says that "the original labels do not appear in the compiled output", but this isn't true when using the `-@` switch.

- Instead, every label results in a property in the `__symbols__` node, mapping a label to a path, exactly like the `aliases` node.

- In fact, the mechanism is so similar that when resolving symbols, the Raspberry Pi loader will search the "aliases" node in the absence of a `__symbols__` node.

- This was useful at one time because providing sufficient aliases allowed very old versions of `dtc` to be used to build the base DTB files, but fortunately that is ancient history now.

#### Device Tree parameters

- To avoid the need for lots of Device Tree overlays, and to reduce the need for users of peripherals to modify DTS files, the Raspberry Pi loader supports a new feature - Device Tree parameters.

- This permits small changes to the DT using named parameters, similar to the way kernelmodules receive parameters from `modprobe` and the kernel command line.

- Parameters can be exposed by the base DTBs and by overlays, including HAT overlays.

<br>

- Parameters are defined in the DTS by adding an `__overrides__` node to the root.

- It contains properties whose names are then chosen parameter names, and whose values are a sequence comprising a phandle for the target node, and a string indicating the target property; string, integer (cell) and boolean properties are supported.

##### String parameters

- String parameters are declared like this:

    ```dts
    name = <&label>,"property";
    ```

- where `label` and `property` are replaced by suitable values.

- String parameters can cause their target properties to grow,shrink, or be created.

<br>

- Note that properties called `status` are treated specially; non-zero/true/yes/on values are converted to the string`"okay"`, while zero/false/no/off becomes `"disabled"`.

##### Integer parameters

- Integer parameters are declared like this:

```dts
name = <&label>,"property.offset";  // 8-bit
name = <&label>,"property;offset";  // 16-bit
name = <&label>,"property:offset";  // 32-bit
name = <&label>,"property#offset";  // 64-bit
```

- Here, `label`, `property` and `offset` are replaced by suitable values; the offset is specified in bytes relative to the start of the property, and the preceding separator dictates the size of the parameter.

- In a change from earlier implementations, integer parameters may refer to non-existent properties or to offsets beyond the end of an existing property.

##### Boolean parameters

- Device Tree encodes boolean values as zero-length properties; if present then the property is true, otherwise it is false.

- They are defined like this:

    ```dts
    boolean_property;   // Set 'boolean_property' to true
    ```

- A property is assigned the value `false` by not defining it.

- Boolean parameters are declared like this, replacing the `label` and `property` placeholders with suitable values:

    ```dts
    name = <&label>,"property?";
    ```

- Inverted booleans invert the input value before applying it in the same way as a regular boolean; they are declared similarly, but use `!` to indicate the inversion:

    ```dts
    name = <&label>,"<property>!";
    ```

- Boolean parameters can cause properties to be created or deleted, but they can't delete a property that already exists in the base DTB.

##### Byte string parameters

- Byte string properties are arbitrary sequences of bytes, e.g. MAC addresses.

- They accept strings of hexadecimal bytes, with or without colons between the bytes.

    ```dts
    mac_address = <&ethernet0>,"local_mac_address[";
    ```

- The `[` was chosen to match the DT syntax for declaring a byte string.

##### Parameters with multiple targets

- There are some situations where it is convenient to be able to set the same value in multiple locations within the Device Tree.

- Rather than the ungainly approach of creating multiple parameters, it is possible to add multiple targets to a single parameter by concatenating them, like this:

```dts
__overrides__ {
    gpiopin = <&w1>,"gpios:4",
              <&w1_pins>,"brcm,pins:0";
    ...
};
```

> ##### Note
>
> - It is even possible to target properties of different types with a single parameter.
>
> - You could reasonably connect an "enable" parameter to a `status` string, cells containing zero or one, and a proper boolean property.

##### Literal assignments

- The DT parameter mechanism allows multiple targets to be patched from the same parameter, but the utility is limited by the fact that the same value has to be written to all locations (except for format conversion and the negation available from inverted booleans).

- The addition of embedded literal assignments allows a parameter to write arbitrary values, regardless of the parameter value supplied by user.

- Assignments appear at the end of a declaration, and are indicated by a `=`:

```dts
str_val  = <&target>,"strprop=value";              // 1
int_val  = <&target>,"intprop:0=42";               // 2
int_val2 = <&target>,"intprop:0=",<42>;            // 3
bytes    = <&target>,"bytestr[=b8:27:eb:01:23:45"; // 4
```

- Lines 1,2 and 4 are fairly obvious, but line 3 is more interesting because the value appears as an integer (cell) value.

- The DT compiler evaluates integer expressions at compile time, which might be convenient (particularly if macro values are used), but the cell can also contain a reference to a label:

    ```dts
    // Force an LED to use a GPIO on the internal GPIO controller.
    exp_led = <&led1>,"gpios:0=", <&gpio>,
              <&led1>,"gpios:4";
    ```

- When the overlay is applied, the label will be resolved against the base DTB in the usual way.

- It is good idea to split multi-part parameters over multiple lines like this to make them easier to read - something that becomes more necessary with the addition of cell value assignments.

<br>

- Bear in mind that parameters do nothing unless they are applied - a default value in a lookup table is ignored unless the parameter name is used without assigning a value.

##### Lookup tables

- Lookup tables allow parameter input values to be transformed before they are used.

- They act as associative arrays, rather like switch/case statements:

    ```dts
    phonetic = <&node>,"letter{a=alpha,b=bravo,c=charlie,d,e,='tango uniform'}";
    bus      = <&fragment>,"target:0{0=",<&i2c0>,"1=",<&i2c1>,"}";
    ```

- A key with no `=value` means to use the key as the value, an `=` with no key before it is the default value in the case of no match, and starting or ending the list with a comma (or an empty key=value pair anywhere) indicates that the unmatched input value should be used unaltered; otherwise, not finding a match is an error.

> ##### Note
>
> - The comma separator within the table string after a cell integer value is implicit - adding one explicitly creates an empty pair.

> ##### Note
>
> - As lookup tables operate on input values and literal assignments ignore them, it's not possible to combine the two - characters after the closing `}` in the lookup declaration are treated as an error.

##### Overlay/fragment parameters

- The DT parameter mechanism as described has a number of limitations, including the lack of an easy way to create arrays of integers, and the inability to create new nodes.

- One way to overcome some of these limitations is to conditionally include or exclude certain fragments.

- A fragment can be excluded from the final merge process by renaming the `__overlay__` node to ``_dormant__`.

- The parameter declaration syntax has been extended to allow the otherwise illegal zero target phandle to indicate that the following string contains operations at fragment or overlay scope.

- So far, four operations have been immplemented:

    ```dts
    +<n>    // Enable fragment <n>
    -<n>    // Disable frament <n>
    =<n>    // Enable fragment <n> if the assigned parameter value is true, otherwise disable it
    !<n>    // Enable fragment <n> if the assigned parameter value is false, otherwise disable it
    ```

- Examples:

    ```dts
    just_one    = <0>,"+1-2"; // Enable 1, disable 2
    conditional = <0>,"=3!4"; // Enable 3, disable 4 if value is true;
                              // otherwise disable 3, enable 4.
    ```

- The `i2c-rtc` overlay uses this technique.

##### Special properties

- A few property names, whne targeted by a parameter, get special handling.

- One you may have noticed already - `status` - will convert a boolean to either `okay` for true and `disabled` for false.

- Assigning to the `bootargs` property appends to it rather than overwriting it - this is how settings can be added to the kernel command line.

- The `reg` property is used to specify device addresses - the location of a memory-mapped hardwared block, the address on an I2C bus, etc.

- The names of child nodes should be qualified with their addresses in hexadecimal, using `@` as a separator:

    ```dts
    bmp280@76 {
        reg = <0x77>;
        ...
    };
    ```

- When assigning to the `reg` property, the address portion of the parent node name will be replacced with the assigned value..

- This can be used to prevent a node name clash when using the same overlay multiple times - a technique used by the `i2c-gpio` overlay. 

<br>

- The `name` property is a pseudo-property - it shouldn't appear in a DT, but assigning to it causes the name of its parent node to be changed to the assigned value.

- Like the `reg` property, this can be used to give nodes unique names.

##### Examples

- Here are some examples of different types of properties, with parameters to modify them:

```dts
/ {
    fragment@0 {
        target-path = "/";
        __overlay__ {

            test: test_node {
                string = "hello";
                status = "disabled";
                bytes = /bits/ 8 <0x67 0x89>;
                u16s = /bits/ 16 <0xabcd 0xef01>;
                u32s = /bits/ 32 <0xfedcba98 0x76543210>;
                u64s = /bits/ 64 <0xaaaaa5a55a5a5555 0x0000111122223333>;
                bool1; // Defaults to true
                       // bool2 defaults to false
                mac = [01 23 45 67 89 ab];
                spi = <&spi0>;
            };
        };
    };

    fragment@1 {
        target-path = "/";
        __overlay__ {
            frag1;
        };
    };

    fragment@2 {
        target-path = "/";
        __dormant__ {
            frag2;
        };
    };

    __overrides__ {
        string =        <&test>,"string";
        enable =        <&test>,"status";
        byte_0 =        <&test>,"bytes.0";
        byte_1 =        <&test>,"bytes.1";
        u16_0 =         <&test>,"u16s;0";
        u16_1 =         <&test>,"u16s;2";
        u32_0 =         <&test>,"u32s:0";
        u32_1 =         <&test>,"u32s:4";
        u64_0 =         <&test>,"u64s#0";
        u64_1 =         <&test>,"u64s#8";
        bool1 =         <&test>,"bool1!";
        bool2 =         <&test>,"bool2";
        entofr =        <&test>,"english",
                        <&test>,"french{hello=bonjour,goodby='au revoir',weekend}";
        pi_mac =        <&test>,"mac[{1=b8273bfedcba,2=b8273b987654}";
        spibus =        <&test>,"spi:0[0=",<&spi0>,"1=",<&spi1>,"2=",<<&spi2>;

        only1 =         <0>,"+1-2";
        only2 =         <0>,"-1+2";
        enable1 =       <0>,"=1";
        disable2 =      <0>,"!2";
    };
};
```

- For further examples, a large collection of overlay source files is hosted inthe Raspberry Pi Linux GitHub repository

### Using Device Trees on Raspberry Pi

#### DTBs, overlays and `config.txt`

- On a Raspberry Pi it is the job of the loader to combine overlays with an appropriate base device tree, and then to pass a fully resolved Device Tree to the kernel.

- The base Device Trees are located alongside `start.elf` in the FAT partition (`/boot/firmware/` from Linux), named `bcm2711-rpi-4-b.dtb`, etc.

- The loader now supports builds using bcm2835_defconfig, which selects the uptreamed BCM2835 support.

- This configuration will cause `bcm2835-rpi-b.dtb` to be built.

- If these files are copied with the kernel, then the loader will attempt to load one of those DTBs by default.

- In order to manage Device Tree and overlays, the loader supports a number of `config.txt` directives:

    ```
    dtoverlay=acme-board
    dtparam=foo=bar,level=42
    ```

- This will cause the loader to look for `overlays/acme-board.dtbo` in the firmware partition, which Raspberry Pi OS mounts on `/boot/firmware/`.

- It will then search for parameters `foo` and `level`, and assign the indicated values to them.

- The loader will also search for an attached HAT with a programmed EEPROM, and load the supporting overlay from there - either directly or by name from the "overlays" directory; this happens without any user intervention.

- There are multiple ways to tell that the kernel is using Device Tree:

    - The "Machine model:" kernel message during bootup has a board-specific value such as "Raspberry Pi 2 Model B".

    - `/proc/device-tree` exists, and contains subdirectories and files that exactly mirror the nodes and properties of the DT.

- With a Device Tree, the kernel will automatically search for and load modules that support the indicated enabled devices.

- As a result, by creating an appropriate DT overlay for a device you save users of the device from having to edit `/etc/modules`; all of the configuration goes in `config.txt`, and in the case of a HAT, even that step is unnecessary.

- Note, however, that layered modules such as `i2c-dev` still need to be loaded explicitly.

- The flipside is that because platform devices don't get created unless requested by the DTB, it should no longer be necessary to blacklist modules that used to be loaded as a result of platform devices defined in the board support code.

- In fact, current Raspberry Pi OS images ship with no blacklist files.

#### DT parameters

- As described above, DT parameters are a convenient way to make small changes to a device's configuration.

- The current base DTBs support parameters for enabling and controlling the onboard audio, I2C, I2S and SPI interfaces without using dedicated overlays.

- In use, parameters look like this:

    ```
    dtparam=audio=on,i2c_arm=on,i2c_arm_baudrate=400000,spi=on
    ```

> ##### Note
>
> - Multiple assignments can be placed on the same line, but ensure you don't exceed the 80-character limit.

- If you have an overlay that defines some parameters, they can be specified either on subsequent lines like this:

    ```
    dtoverlay=lirc-rpi
    dtparam=gpio_out_pin=16
    dtparam=gpio_in_pin=17
    dtparam=gpio_in_pull=down
    ```

- ...or appended to the overlay line like this:

    ```
    dtoverlay=lirc-rpi,gpio_out_pin=16,gpio_in_pin=17,gpio_in_pull=down
    ```

- Overlay parameters ara only in scope until the next overlay is loaded.

- In the event of a parameter with the same name being exported by both the overlay and the base, the parameter in the overlay takes precedence; it's recommended that you avoid doing this.

- To expose the parameter exported by the base DTB instead, end the current overlay scope using:

    ```
    dtoverlay=
    ```

##### Dynamic Device Tree

- As of Linux 4.4, Raspberry Pi kernels support the dynamic loading of overlays and parameters.

- Compatible kernels manage a stack of overlays that are applied on top of the base DTB.

- Changes are immediately reflected in `/proc/device-tree` and can cause modules to be loaded and platform devices to be created and destroyed.

- The use of the word "stack" above is important - overlays can only be added and removed at the top of the stack; changing something further down the stack requires that anything on top of it must first be removed.

- There are some new commands for managing overlays:

###### The `dtoverlay` command

- `dtoverlay` is a command line utility that loads and removes overlays while the system is running, as well as listing the available overlays and displaying their help information.

- Use `dtoverlay -h` to get usage information:

    ```
    Usage:
      dtoverlay <overlay> [<param>=<val>...]
                               Add an overlay (with parameters)
      dtoverlay -D [<idx>]     Dry-run (prepare overlay, but don't apply -
                               save it as dry-run.dtbo)
      dtoverlay -r [<overlay>] Remove an overlay (by name, index or the last)
      dtoverlay -R [<overlay>] Remove from an overlay (by name, index or all)
      dtoverlay -l             List active overlays/params
      dtoverlay -a             List all overlays (marking the active)
      dtoverlay -h             Show this usage message
      dtoverlay -h <overlay>   Display help on an overlay
      dtoverlay -h <overlay> <param>..  Or its parameters
        where <overlay> is the name of an overlay or 'dtparam' for dtparams
    Options applicable to most variants:
        -d <dir>    Specify an alternate location for the overlays
                    (defaults to /boot/firmware/overlays or /flash/overlays)
        -v          Verbose operation
    ```

- Unlike the `config.txt` equivalent, all parameters to an overlay must be included in the same command line - the dtparam command is only for parameters of the base DTB.

- Command variants that change kernel state require root privilege, so you may need to prefix the command with `sudo`.

- Only overlays and parameters applied at run-time can be unloaded - an overlay or parameter applied by the firmware becomes "baked in" such that it won't be listed by `dtoverlay` and can't be removed.

###### The `dtparam` command

- `dtparam` creates and loads an overlay that has largely the same effect as using a dtparam directive in `config.txt`.

- In usage it is largely equivalent to `dtoverlay` with an overlay name of `-`, but there are a few differences: `dtparam` will list the help information for all known parameters of the base DTB.

- Help on the dtparam command is still available using `dtparam -h`.

- When indicating a parameter for removal, only index numbers can be used.

- Not all Linux subsystems respond to the addition of devices at runtime - I2C, SPI and sound devices work, but some won't.

##### Supported overlays and parameters

- For a list of supported overlays and parameters, see the [README](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README) file found alongside the overlay `.dtbo`d files in `/boot/firmware/overlays`.

- It is kept up-to-date with additions and changes.
