# AKTpi - buildroot tutorial for a dedicated audio RPi buildroot distro

This is a basic tutorial on how to roll a dedicated distribution for your own audio projects using buildroot.

Content
* [Getting started with buildroot](#getting-started-with-buildroot)
* [Configuring buildroot for Raspberry Pi and audio](#configuring-buildroot-for-raspberry-pi-and-audio)
 
## Getting started with buildroot 

[buildroot](https://buildroot.org/) TODO 

### install buildroot dependencies
*(~1min)*
buildroot has some dependencies for creating the cross-compiler chain.

> See: [https://buildroot.org/downloads/manual/manual.html#requirement](https://buildroot.org/downloads/manual/manual.html#requirement) 

On Debian or a Debian-based Distro like Ubuntu you should be fine with:

    sudo apt-get install build-essential libncurses5-dev

### getting buildroot (~1min)
*(~1min)*

Download the stable version from the [website](https://buildroot.org/).

Or if you want get the newest version via git

    git clone https://github.com/buildroot/buildroot.git

> Note: The latest version sometimes break stuff. If you get an error message through the course of this tutorial. Try a stable branch of the git repo instead.

## Configuring buildroot for Raspberry Pi and audio

buildroot targets a lot of different platforms (e.g. Raspberry Pi, BeagleBone, i386) and provides configuration templates for these.
In this tutorial we target Raspberry Pi 1 and 3. If you are using an other platform you need to adjust the following steps to your needs. 

### configure buildroot w/ RPi defaults

To load the Raspberry Pi 1 template or *defconfig* use:

    make raspberrypi_defconfig

> For details see: [https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt](https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt)

### configure packages, etc.
make menuconfig

Toolchain -> Enable WCHAR support
Target Packages -> Audio and video applications -> alsa-utils -> alsamixer, amidi
						jack2

exit and save

### make
make

### transfer to SD card

### adjusting system
modprobe snd_usb ding

### adjust init

### add jackcpp package

### compile tutorial

### add midi
