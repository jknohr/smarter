# Matter SiWx917 Lighting Example

An example showing the use of CHIP on the Silicon Labs SiWx917.

<hr>

- [Matter SiWx917 Lighting Example](#matter-siwx917-lighting-example)
  - [Introduction](#introduction)
  - [Building](#building)
  - [Flashing the Application](#flashing-the-application)
  - [Viewing Logging Output](#viewing-logging-output)
  - [Running the Complete Example](#running-the-complete-example)
  - [Notes](#notes)
  - [Memory settings](#memory-settings)
  - [Group Communication (Multicast)](#group-communication-multicast)
  - [Building options](#building-options)
    - [Disabling logging](#disabling-logging)
    - [Debug build / release build](#debug-build--release-build)
    - [Disabling LCD](#disabling-lcd)
    - [KVS maximum entry count](#kvs-maximum-entry-count)

<hr>

> **NOTE:** Silicon Laboratories now maintains a public matter GitHub repo with
> frequent releases thoroughly tested and validated. Developers looking to
> develop matter products with silabs hardware are encouraged to use our latest
> release with added tools and documentation.
> [Silabs Matter Github](https://github.com/SiliconLabs/matter/releases)

<a name="intro"></a>

## Introduction

The SiWx917 lighting example provides a baseline demonstration of a Light control
device, built using Matter, the Silicon Labs gecko SDK and Silicon labs WiseMCU SDK. It can be controlled
by a Chip controller over a Wifi network.

The lighting example is intended to serve both as a means to explore the
workings of Matter as well as a template for creating real products based on the
Silicon Labs platform.

<a name="building"></a>

## Building

-   Download the
    [Simplicity Commander](https://www.silabs.com/mcu/programming-options)
    command line tool, and ensure that `commander` is your shell search path.
    (For Mac OS X, `commander` is located inside
    `Commander.app/Contents/MacOS/`.)

-   Download and install a suitable ARM gcc tool chain:
    [GNU Arm Embedded Toolchain 9-2019-q4-major](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)

-   Install some additional tools (likely already present for CHIP developers):

    -   Linux: `sudo apt-get install git ninja-build`

    -   Mac OS X: `brew install ninja`

-   Supported hardware:

    -   > For the latest supported hardware please refer to the
        > [Hardware Requirements](https://github.com/SiliconLabs/matter/blob/latest/docs/silabs/general/HARDWARE_REQUIREMENTS.md)
        > in the Silicon Labs Matter Github Repo


*   Build the example application:

          cd ~/connectedhomeip
          ./scripts/examples/gn_efr32_example.sh examples/lighting-app/silabs/SiWx917/ out/test BRD4325A ssid=\"<ssid>\" psk=\"<psk>\" --wifi rs911x

    -   > Enter your AP's SSID and passcode for the `ssid` and `psk` build parameters.

-   To delete generated executable, libraries and object files use:

          $ cd ~/connectedhomeip
          $ rm -rf ./out/

    OR use GN/Ninja directly

          $ cd ~/connectedhomeip/examples/lighting-app/silabs/SiWx917
          $ git submodule update --init
          $ source third_party/connectedhomeip/scripts/activate.sh
          $ export EFR32_BOARD=BRD4325A
          $ gn gen out/debug
          $ ninja -C out/debug

-   To delete generated executable, libraries and object files use:

          $ cd ~/connectedhomeip/examples/lighting-app/silabs/SiWx917
          $ rm -rf out/

<a name="flashing"></a>

## Flashing the Application

-   Flashing requires the SiWx917 SoC device to be configured in the Ozone Debugger.
-   Once it's configured, it can be run with the Ozone Debugger by loading the .out file.
    -   > For detailed instructions, please refer to 
        > [Running the Matter Demo on SiWx917 SoC](https://github.com/SiliconLabs/matter/blob/latest/docs/silabs/wifi/RUN_DEMO_SiWx917_SoC.md) 
        > in the Silicon Labs Matter Github Repo

<a name="view-logging"></a>

## Viewing Logging Output

The example application's logging output can be viewed in the Ozone Debugger.

<a name="running-complete-example"></a>

## Running the Complete Example

*   The example application supports provisioning over IP (on-network provisioning).
*   You can provision and control the Chip device using the Chip tool standalone

    [CHIPTool](https://github.com/project-chip/connectedhomeip/blob/master/examples/chip-tool/README.md)

    Here is an example with the CHIPTool:

    chip-tool pairing onnetwork 1 20202021

    chip-tool onoff on 1 1

### Notes

-   Depending on your network settings your router might not provide native ipv6
    addresses to your devices (Border router / PC). If this is the case, you
    need to add a static ipv6 addresses on both device and then an ipv6 route to
    your router on your PC

    -   On PC(Linux): `sudo ip addr add dev <Network interface> 2002::1/64`

    -   Add Ipv6 route on PC(Linux)
        `sudo ip route add <Router global ipv6 prefix>/64 via 2002::2`

## Memory settings

While most of the RAM usage in CHIP is static, allowing easier debugging and
optimization with symbols analysis, we still need some HEAP for the crypto and
Wi-Fi stack. Size of the HEAP can be modified by changing the value of the
`configTOTAL_HEAP_SIZE` define inside of the FreeRTOSConfig.h file of this
example. Please take note that a HEAP size smaller than 13k can and will cause a
Mbedtls failure during the BLE rendez-vous or CASE session

To track memory usage you can set `enable_heap_monitoring = true` either in the
BUILD.gn file or pass it as a build argument to gn. This will print on the RTT
console the RAM usage of each individual task and the number of Memory
allocation and Free. While this is not extensive monitoring you're welcome to
modify `examples/platform/efr32/MemMonitoring.cpp` to add your own memory
tracking code inside the `trackAlloc` and `trackFree` function

## Group Communication (Multicast)

With this lighting example you can also use group communication to send Lighting
commands to multiples devices at once. Please refer to the
[chip-tool documentation](../../chip-tool/README.md) _Configuring the server
side for Group Commands_ and _Using the Client to Send Group (Multicast) Matter
Commands_

## Building options

All of Silabs's examples within the Matter repo have all the features enabled by
default, as to provide the best end user experience. However some of those
features can easily be toggled on or off. Here is a short list of options to be
passed to the build scripts.

### Disabling logging

`chip_progress_logging, chip_detail_logging, chip_automation_logging`

    $ ./scripts/examples/gn_efr32_example.sh ./examples/lighting-app/silabs/SiWx917 ./out/lighting-app BRD4325A "chip_detail_logging=false chip_automation_logging=false chip_progress_logging=false"

### Debug build / release build

`is_debug`

    $ ./scripts/examples/gn_efr32_example.sh ./examples/lighting-app/silabs/SiWx917 ./out/lighting-app BRD4325A "is_debug=false"

### Disabling LCD

`show_qr_code`

    $ ./scripts/examples/gn_efr32_example.sh ./examples/lighting-app/silabs/SiWx917 ./out/lighting-app BRD4325A "show_qr_code=false"

### KVS maximum entry count

`kvs_max_entries`

    Set the maximum Kvs entries that can be stored in NVM (Default 75)
    Thresholds: 30 <= kvs_max_entries <= 255

    $ ./scripts/examples/gn_efr32_example.sh ./examples/lighting-app/silabs/SiWx917 ./out/lighting-app BRD4325A kvs_max_entries=50
