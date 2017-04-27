# AKTpi - buildroot tutorial for a dedicated audio RPi buildroot distro

This is a basic tutorial on how to roll a dedicated distribution for your own audio projects using buildroot.

**Disclaimer: This tutorial is based on the *2017.02.x* branch of buildroot.**

Content
1. [Getting started with buildroot](#getting-started-with-buildroot)
2. [Configuring buildroot for Raspberry Pi and audio](#configuring-buildroot-for-raspberry-pi-and-audio)
 
## Getting started with buildroot 

[buildroot](https://buildroot.org/) TODO buildroot description

### install buildroot dependencies
*(~1min)*
buildroot has some dependencies for creating the cross-compiler chain.

> See: [https://buildroot.org/downloads/manual/manual.html#requirement](https://buildroot.org/downloads/manual/manual.html#requirement) 

On Debian or a Debian-based Distro like Ubuntu you should be fine with:

    sudo apt-get install build-essential libncurses5-dev

### getting buildroot
*(~1min)*

Download the stable version from the [website](https://buildroot.org/).

Or if you want get the newest version via git

    git clone https://github.com/buildroot/buildroot.git

> Note: Make sure to checkout a stable branch, as the latest version can break things. This is probably the case if you get an error message through the course of this tutorial.

## Configuring buildroot for Raspberry Pi and audio

buildroot targets a lot of different platforms (e.g. Raspberry Pi, BeagleBone, i386) and provides configuration templates for these.
In this tutorial we target Raspberry Pi 1 and 3. If you are using an other platform you need to adjust the following steps to your needs. 

### Loading target platform specific configurations

To load the Raspberry Pi 1 template (called *defconfig*) use:

    make raspberrypi_defconfig

> For details see: [https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt](https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt)

### Configuring the system and packages

After making the basic configurations for the target platform, we now can adjust our system to our specific needs.
For that you can use different types of menues (see the official documentation), but we going to use a console-based one.

   make menuconfig

This greets you with this screen. You can use the arrow keys, enter and escape to navigate.

![menu start](images/menu-00-start.jpg)

#### an audio system

The basic configuration already includes ALSA, but we want to include a bit more, i.e. jack and MIDI

To install some of the ALSA tools you need to enable the WCHAR support in the toolchain.

![toolchain](images/menu-01-toolchain.png)
![wchar](images/menu-02-wchar2-wchar.png)

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
