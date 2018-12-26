# This is manifest to download AGL build system with meta-synopsys layer

## Introdution
Automotive Grade Linux is a collaborative open source project that is bringing together automakers, suppliers and technology 
companies to accelerate the development and adoption of a fully open software stack for the connected car.
With Linux at its core, AGL is developing an open platform from the ground up that can serve as the de facto industry standard to enable rapid development of new features and technologies.


## Prerequisites
### Software
AGL demo image can only be built on a Linux development host, so either a real Linux host or a virtual machine with Linux installed in the client should be used.

Before building AGL demo image it's important to make sure the build machine has all required packages installed. Please refer to Automotive Grade Linux official documentation where examples for different host Linux distributions are provided:
http://docs.automotivelinux.org/master/docs/getting_started/en/dev/reference/source-code.html

### Hardware

* **Video output**.
  The HSDK board does not support HDMI so a USB-to-HDMI or USB-to-DVI adapter is required for external video output to HDMI. 
  As of today there's only one such type of devices that fully supported in the Linux kernel - 
  those based on DisplayLink USB 2.0 chips (i.e. DL-1x5, DL-1x0).
  Tested adapter is Accell J131B-001B. On manufacturer's web-site:
    [Accell J131B-001B](https://www.accellww.com/products/ultraav-usb-2-0-to-hdmi-adapter)
* **Bluetooth**.
  The HSDK board does not have onboard bluetooth adapter so a USB Bluetooth dongle is required for demonstration purposes.
  Tested adapter is ASUS USB-BT400. On amazon web-site: 
  [ASUS USB-BT400](https://www.amazon.com/Bluetooth-Receiver-Keyboards-Controllers-USB-BT400/dp/B00DJ83070/ref=sr_1_1?ie=UTF8&qid=1545843433&sr=8-1&keywords=ASUS+USB-BT400)
* **AM/FM Tuner**.
  The AGL demo application "Radio" requires a radio receiver, which is not present on HSDK board. 
  Tested adapter is GIGABYTE GT-U7300: On manufacturer's web-site: [GT-U7300](https://www.gigabyte.com/us/TV-Tuner-Dongle/U7300)
  According [AGL Wiki](https://wiki.automotivelinux.org/agl-distro/ces-2017-demo)  it is recommended to use [RTL-SDR](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/) dongles 

## Build procedure
1. Download necessary files using [repo](https://source.android.com/setup/downloading#installing-repo) utility 
```
  repo init -m agl-demo-2018.09.xml -u https://github.com/EvgeniiDidin/agl_manifest_synopsys
  repo sync -j4 
```
2. Initialize build directory by using template files
```
  source meta-agl/scripts/aglsetup.sh -m hsdk agl-demo agl-netboot agl-appfw-smack
```
3. Build synopsys image (currently, the rootfs is used as initramfs)
```
  bitbake agl-demo-platform
```
## Deploy and startup procedures
To run image it requires microSD-card with a capacity of at least 8 GB and marked up in two partitions:
   * First partition - FAT32, size should not less then 50 MB.
   * Second partition - EXT4, remaining volume.
   
After build procedure have finished the artefacts are stored in **build/tmp/deploy/images/hsdk/** directory.
1) Put **uImage** file in FAT32 partition on microSD-card
2) Deploy **agl-demo-platform-hsdk.ext4** file on **second(EXT4)** partition:
    * On Linux hosts, use dd command as follows: sudo dd if=agl-demo-platform-hsdk.ext4 **of=/dev/mmcblk0p2** bs=1M
    * On Windows hosts, use "Win32 Disk Imager" utility, available here: https://sourceforge.net/projects/win32diskimager/

Once the agl-demo-platform-hsdk.ext4 file is deployed on microSD-card, just insert it in the microSD-card slot of the HSDK and reset the board. Note DIP-switch BIM should be in 1:off, 2:on state while both BMC and BCS should be in 1: on, 2:on state.
In u-Boot terminal run next commands:
```
fatload mmc 0:1
bootm
```

## Running applications

