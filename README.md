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
To run image it requires microSD-card with a capacity of at least 8 GB.
   
After build procedure have finished the artefacts are stored in **build/tmp/deploy/images/hsdk/** directory.
Deploy **agl-demo-platform-hsdk.wic.xz** file on microSD-card:
  * On Linux hosts, use dd command as follows:   
      ```xz -cd agl-demo-platform-hsdk.wic.xz | sudo dd of=/dev/mmcblkp0 bs=4M; sync```
  * On Windows hosts, use "Win32 Disk Imager" utility, available here: https://sourceforge.net/projects/win32diskimager/

Once the agl-demo-platform-hsdk.ext4 file is deployed on microSD-card, just insert it in the microSD-card slot of the HSDK and reset the board. Note DIP-switch BIM should be in 1:off, 2:on state while both BMC and BCS should be in 1: on, 2:on state.
In u-Boot terminal run next commands:
```
fatload mmc 0:1
bootm
```

## Running applications
After boot on the screen you will see next main menu:
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/main.png" align="center" height="480" width="320">
</p>
Here 8 of 9 applications are currently working. Poi application is not working currently due to incorrect geolocation. Tapping on round buttons will start an application. Using "Home" button on the top will swith screen to main menu.

### Settings application
The Settings application can be used to connect board via Bluetooth to smartphone and to WI-FI spot. Also it can show version info. Changing Date&Time is not changing real onboard time.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/settings.png" align="center" height="480" width="320">
</p>
To connect HSDK board to smartphone using Bluetooth click on "Bluetooth" title. Then in the list of device click "Pair" button of the device you would like to connect, and on smartphone confirm connection. When the device is at the top of the list click "Connect" button. Under the name of the device MAC address and "Handsfree & AV Connection" title should appear.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/Bluetooth.png" align="center" height="480" width="320">
</p>

### Mixer application
The Mixer application can change sound volume of input and output audio devices.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/mixer.png" align="center" height="480" width="320">
</p>

### Mediaplayer
The Mediaplayer is an application which can play audio files from smartphone using Bluetooth connection. Connect HSDK board to smatphone using "Settings"->Bluetooth, then open audioplayer on smartphone and play audiofile. 
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/mediaplayer.png" align="center" height="480" width="320">
</p>

### Phone
The Phone application requires established Bluetooth connection with smartphonw. Enter the number and press button with cellphone symblol. 
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/phone.png" align="center" height="480" width="320">
</p>

### Radio
The Radio application requires AM/FM Tuner plugged into HSDK board. Using arrows or slider it is possible to change radio frequency.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/radio.png" align="center" height="480" width="320">
</p>

### HVAC
The HVAC application is used for automotive climate control. Currently devices like ventilator or air conditioning are not supported on HSDK board. **NOTE:** scrolling temperature causes crash of HVAC application
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/hvac.png" align="center" height="480" width="320">
</p>

### Navigation
The Navigation application can simulate automotive driving on map. All movement changes are accompanied by the sound of a voice assistant. 
Usage:
1) Make a log click in some place of the map (similar to finger tap).
2) When a point on the map is present, click the button on the top of right corner.
3) When the root is drawn, click the button from the paragraph "2" again.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/navi.png" align="center" height="480" width="320">
</p>


### Dashboard
The Dashboard application is a monitor of car condition. Currently no sensors are supported to demonstrate capability of this application.
<p align="center">
 <img src="https://github.com/EvgeniiDidin/agl_manifest_synopsys/blob/master/screenshots/dashboard.png" align="center" height="480" width="320">
</p>

## Usefull tips
1) *afm-util*  - utility, which can start application without GUI. 
```
$ afm-util list
$ afm-util run <application_id>
```
2) In case of multiple audio devices use pacmd and pactl commands:
```
$ pactl list sinks short
$ pacmd set-default-sink 1
```
3) Bluetooth:
  * Usefull link: https://wiki.automotivelinux.org/bluetooth
  * To reset any know pairings just remove everything in directory /var/lib/bluetooth/<bluetooth-adapter-mac>

## Known issues
