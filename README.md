# AKTpi - your own RPi audio distro

#### 2017/05/02 - Konrad Krenzlin, Manuel Weber

This is a basic tutorial on how to roll a dedicated distribution for your own audio projects using buildroot.

**Disclaimer: This tutorial is based on the *2017.02.x* branch of buildroot.**

Content
0. [Introduction](#introduction)
1. [Getting started with buildroot](#getting-started-with-buildroot)
2. [Configuring buildroot for Raspberry Pi and audio](#configuring-buildroot-for-raspberry-pi-and-audio)
3. [The first start](#the-first-start)
4. [Audio and MIDI setup](#audio-and-midi-setup)
5. [Compiling your own application](#compiling-your-own-application)
5. [Troubleshooting](#troubleshooting)
 
## Introduction

The aim of this tutorial is to create a dedicated audio system for the Raspberry Pi ready to be used on on stage.
It covers using buildroot to generate an image for the Raspberry Pi, setting up ALSA, Jack and MIDI and compiling your own applications using the generated cross-compilation toolchain of buildroot.

While buildroot makes things relatively easy you still should have a basic knowledge on how to use git, shell and the Linux kernel.

## Getting started with buildroot 

"Buildroot [...] simplifies and automates the process of building a complete Linux system [...]." [buildroot website](https://buildroot.org/)

buildroot is a set of makefiles and configurations for

* default configurations for various target platforms, e.g. Raspberry Pi, Beagle Bone
* creating a cross-compilation toolchain
* downloading and compiling sources

### Install buildroot dependencies

buildroot has some dependencies for creating the cross-compiler toolchain.

> See: [https://buildroot.org/downloads/manual/manual.html#requirement](https://buildroot.org/downloads/manual/manual.html#requirement) 

On Debian or a Debian-based distro like Ubuntu you should be fine with:

    sudo apt-get install build-essential libncurses5-dev

### Getting buildroot

Download the stable version from the [website](https://buildroot.org/).

Or if you want get the latest version via git

    git clone https://github.com/buildroot/buildroot.git

> Note: Make sure to checkout a stable branch, as the latest version can break things. This is probably the case if you get an error message through the course of this tutorial.

## Configuring buildroot for Raspberry Pi and audio

buildroot targets a lot of different platforms (e.g. Raspberry Pi, BeagleBone, i386) and provides configuration templates for these.
In this tutorial we target Raspberry Pi 1 and 3. If you are using an other platform you need to adjust the following steps to your needs. 

### Loading target platform specific configurations

To load the Raspberry Pi template (called *defconfig*) use:

**Raspberry Pi A, B, A+, B+**

    make raspberrypi_defconfig

**Raspberry Pi 3**

    make raspberrypi3_defconfig

> For details see: [https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt](https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt)

### Configuring the system and packages

After making the basic configurations for the target platform, we now can adjust our system to our specific needs.
E.g. add or remove packages, configure kernel and sytem, set passwords or change file systems templates.

buildroot provides different front-ends to the configuration process (see the official documentation).
We are going to use a ncurse-based menu. 

    make menuconfig

![menu start](images/menu-00-start.jpg)

> Note: The basic configuration already includes ALSA, but we want to include a bit more, i.e. jack and MIDI. These decisions are up to you and your needs. 

To install some of the ALSA tools you need to *Enable WCHAR support* in the toolchain.

![toolchain](images/menu-01-toolchain.png)
![wchar](images/menu-02-wchar.png)

Next, we are going to add tools for better ALSA and MIDI debugging.

Go to *Target Packages* -> *Audio and video applications* -> *alsa-utils*

![wchar](images/menu-04-audio.png)
![wchar](images/menu-05-alsa.png)

There select *alsamixer*, *amidi*, *aseqdump*, *speaker-test*.

![wchar](images/menu-06-alsa-tools.png)

In the same *Audio and video applications* menu, you can also select *jack2*

![wchar](images/menu-07-jack.png)

Then go back, exit and say yes to save the new configuration.

![wchar](images/menu-08-exit.png)

### make the image

After you finished your configuration, you are now ready to build your system.

> Note: This step can take *several hours*, as buildroot needs to download and compile all the necessary components of your system. 

    make

buildroot will then download all the necessary source code and start the compilation process (cross-compilation toolchain, system, packages).

## The first start

After buildroot is completed, it generates an image file for your SD card. Now is the time to get your SD card and Raspberry Pi ready.

### Transfer image to SD card

You can find the image under *output/images/sdcard.img*.

Like in the provided [README](https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt) for the Raspberry Pi defconfig, we use the *dd* command to copy the buildroot image to our SD card.

    sudo dd if=output/images/sdcard.img of=/dev/sd??

> Caution: Make sure to provide the correct device file of your SD card to the *of* parameter.

### First Boot 

Some of the configurations for the Raspberry Pi cannot be done within *buildroot* so make sure to checkout the *config.txt* file under the *boot* partition of your SD card.
Checkout [http://elinux.org/RPiconfig](http://elinux.org/RPiconfig) for further information.

Comment out the *display_overscan*, if you have issues with the display.

    # disable_overscan=1

If you want to use the audio out of the Raspberry Pi, add the following line.

    dtparam=audio=on

> Note: To avoid data corruption after editing files on the SD card, make sure to properly un-mount it.

Now you are ready to put th SD into your Raspberry Pi, connect the HDMI and power cable.
The Raspberry Pi should now boot up in a few seconds and show a login prompt.

By default *buildroot* adds the user **root** with no password.  

> Note: You can change the users and passwords in the buildroot configuration.

## Audio and MIDI setup

To use the Raspberry for your audio application you first want to setup the audio interface(s) and MIDI controller(s).

### Audio via HDMI or audio jack

> Note: Some of the following commands need superuser privileges. So you either login as *root* or use *sudo*.

To use the the Raspberry Pi's HDMI or audio jack output you first need to load the soundcards kernel module.

    modprobe snd-bcm2835

> Note: Make sure you have the *dtparam=audio=on* line in the *config.txt*.

Use *aplay* to list the found soundcard(s).

    aplay -l

With the following commands you then can switch between the outputs.

*HDMI*

    amixer cset numid=3 2

*audio jack*

    amixer cset numid=3 1

To test the audio use for ALSA:

    speaker-test

Or for Jack:

    jack_simple_client

### USB audio

To use a class-compilant audio interface via USB you need to load the *snd-usb-audio* kernel module.

    modprobe snd-usb-audio

After that your interface should be available in ALSA. Check by using:

    aplay -l

> Note: The *snd-usb-audio* module is also needed for MIDI via USB.

### MIDI

To use MIDI via USB you need two kernel modules *snd-seq-midi* and *snd-usb-audio*.

    modprobe snd-seq-midi
    modprobe snd-usb-audio

Then connect your MIDI controller via USB. To list all the available MIDI controllers, use:

    aseqdump -l

You can test your controller with

    aseqdump -p <PORTNUM>

If you turn the knobs or press the keys on your controller, the according MIDI notes or CCs should show up.

### adjust init

## Compiling your own applications

### compile tutorial

    buildroot/output/host/usr/bin/arm-linux-g++ src/jackaudioio.cpp sinusoid.cpp sinusoid_example.cpp -I./include -lpthread -ljack -o example

### Add a package

> **Warning:** This part is currently (April 2017) broken.

[See buildroot documentation](http://free-electrons.com/~thomas/buildroot/manual/html/ch11.html)

    mkdir package/jackcpp

add *package/jackcpp/Config.in*

    config BR2_PACKAGE_JACKCPP
        bool "jackcpp"
        depends on BR2_PACKAGE_JACK2
        help
            C++ bindings for jack

            http://www.x37v.info/projects/jackcpp/


edit *package/Config.in*

    source "package/jackcpp/Config.in"

edit *package/jackcpp/jackcpp.mk*

    JACKCPP_VERSION = 488554cf57c73a77aeb146a8689c937970e6b1f6
    JACKCPP_SITE = $(call github,x37v,jackcpp,$(JACKCPP_VERSION))
    JACKCPP_INSTALL_STAGING = YES
    JACKCPP_INSTALL_TARGET = NO 

    define JACKCPP_BUILD_CMDS
        $(MAKE) $(TARGET_CONFIGURE_OPTS) -C $(@D) all
    endef

    define JACKCPP_INSTALL_STAGING_CMDS
        $(INSTALL) -D -m 0755 $(@D)/libjackcpp.a $(STAGING_DIR)/usr/lib/libjackcpp.a
        $(INSTALL) -D -m 0644 $(@D)/include/* $(STAGING_DIR)/usr/include
    endef

    $(eval $(generic-package))

## Troubleshooting

The whole process is very complex, so there will be times when nothing works. These are just some ideas and hints that may help you in your process.
Sometimes you just have to bite the bullet and start from scratch. So document everything you did!

### buildroot exits with an error.

* Are you on a stable branch of buildroot?
* Did you add a new package that breaks?
* Try deleting the misbehaving package from *buildroot/output/build*.
* Tried *make clean*?

### Raspberry Pi won't boot.

* Changed the GPU RAM settings?
* SD card corrupt?

### SD card is corrupted or behaves strange

* Use *sync* heavily.
* Always unmount your SD card properly.
* Sometimes you just have to redo the transfer of the image.

## Resources
* [Using *buildroot* for real projects](http://elinux.org/images/2/2a/Using-buildroot-real-project.pdf) (PDF)
* [buildroot manual](https://buildroot.org/downloads/manual/manual.html)
