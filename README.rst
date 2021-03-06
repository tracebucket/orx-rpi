=========================
ORx build for Raspbery Pi
=========================

This repository contains open-source build scripts and closed-source binary
blobs for building an ORx Raspberry Pi image. 

ORx is a home-made receiver based around Raspberry Pi. It can be used to
receive files from Outernet networks using DVB-S/S2 signal (satellite TV). It
also has support for creating Wireless hotspots. Please look at the docs
directory in the sources for more information on required/supported devices.

License
=======

The complete code for the build system is released under GPLv3. See the COPYING
file in the source tree for more information.

The ONDD binary located in the ``rpi/rootfs_overlay/usr/sbin`` directory is
released under a different proprietary license. Please see ONDD_LICENSE in the
source tree for more information.

Requirements
============

You will need a Linux system to build the image.

The following should be installed on the build box:

- build tools gcc, make, etc
- util-linux
- git
- libstdc++6 32-bit (on 64-bit systems)

Also rquired for creting the SD card image:

- e2fsprogs
- dosfstools
- parted
- qemu

About the image
===============


Cloning the repository
======================
::
    $ git clone --recursive https://github.com/Outernet-Project/orx-rpi.git

Compiling the toolchain
=======================

The toolchain confiuration supplied with this code::

    $ wget http://crosstool-ng.org/download/crosstool-ng/crosstool-ng-1.20.0.tar.bz2
    $ tar xvf crosstool-ng-1.20.0.tar.bz2
    $ cd crosstool-ng-1.12.0
    $ ./configure --enable-local
    $ cp path/to/orx-buildroot/rpi/toolchain/crosstool-ng.rpi-armv6hf-gcc-4.9 .config
    $ ./ct-ng build

The toolchain will be installed in `~/x-tools/arm-rpi-linux-gnueabihf`. The
default Buildroot configuration for this build is configured to look for the
toolchain in `/opt/toolchains`, so unless you want to customize that, it is
recommended to copy it there::

    $ sudo mkdir -p /opt/toolchains
    $ sudo cp -r ~/x-tools/arm-rpi-linux-gnueabihf /opt/toolchains

Building
========

To build the rootfs, run make with default target::

    $ make

Once build completes, you can build the image file::

    $ sudo make image
 
The latter will execute the script `rpi/tools/mkimage.sh`. This script runs
several commands that require root privileges, so the target is separated.

Once build completes, you filld the image in the root of the repository named
`ORxPi-x.y.z.img`.

To get more help on available make targets, type::

    $ make help

By default, all make commands will use the overlay in the ``rpi`` directory.
This creates an image for Raspberry Pi v1. To build for Raspberry Pi v2 using 
the ``rpi2`` overlay, use the B environment variable::

    $ make B=rpi2

.. note::
    RPi 2 support is experimental and untested.

Copying the image file to SD card
=================================

To copy the image file to an SD card, use the following command::

    $ sudo make sdcard

By default, the above command uses ``/dev/sdb`` as SD card drive. To use
another drive, use the SD_CARD environment variable::

    $ sudo make SD_CARD=/dev/sdX sdcard

Rebuilding after changes to rootfs overlay
==========================================

When modifying files in the rootfs overlay, use the following commands to
rebuild::

    $ make clean-build
    $ make
    $ sudo make image

The `clean-build` target will remove rootfs images and compressed kernel image
and restart the build using packages that are already compiled.

Reporting bugs
==============

Please report bugs to the `issue tracker`_.

.. _issue tracker: https://github.com/Outernet-Project/orx-rpi/issues
