# AKTpi
## buildroot tutorial for a dedicated audio RPi buildroot distro

This is a basic tutorial on how to roll a dedicated distribution for your own audio projects using buildroot.

[Getting started with buildroot](#getting-started-with-buildroot)
 
## Getting started with buildroot 

### buildroot dependencies
https://buildroot.org/downloads/manual/manual.html#requirement

for Debian-based (Ubuntu) (~1min)

sudo apt-get install build-essential
sudo apt-get install libncurses5-dev

### clone buildroot repo (~1min)
git clone https://github.com/buildroot/buildroot.git

### configure buildroot w/ RPi defaults
see https://git.busybox.net/buildroot/tree/board/raspberrypi/readme.txt
make raspberrypi_defconfig

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
