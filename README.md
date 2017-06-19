Linux 0.11 Lab
==============

[![Codewake](https://www.codewake.com/badges/ask_question.svg)](https://www.codewake.com/p/linux-0-11-lab)

The old Linux kernel source version [0.11](http://oldlinux.org) and the integrated experiment environment.

[CS630 Qemu Lab](https://github.com/tinyclub/cs630-qemu-lab) is a related project, it is a friendly learning environment for the X86 assembly course: [CS630](http://www.cs.usfca.edu/%7Ecruse/cs630f06/).

If you want to learn the latest [Linux Kernel](http://www.kernel.org), please try our [Linux Lab](https://github.com/tinyclub/linux-lab)

[![Linux 0.11 Lab Demo](images/linux-0.11-lab-with-docker-vncserver+novnc.jpg)](http://showdesk.io/2017-03-18-17-54-23-linux-0.11-lab-usage-00-06-42/)

## Contents

- [Introduction](#introduction)

- [Build on Linux](#build-on-linux)
    - [Linux Setup](#linux-setup)
    - [Hack Linux 0.11 on Linux](#hack-linux-011-on-linux)

- [Build on Mac OS X](#build-on-mac-os-x)
    - [Mac OS X Setup](#mac-os-x-setup)
    - [Hack Linux 0.11 on Mac](#hack-linux-011-on-mac)

- [Build on Other Systems (Include Mac OS X, Windows)](#build-on-other-systems-include-mac-os-x-windows)
    - [Setup with Docker Toolbox and Dockerfile](#setup-with-docker-toolbox-and-dockerfile)
    - [Hack Linux 0.11 on other systems](#hack-linux-011-on-other-systems)


- [Hack Rootfs](#hack-rootfs)
    - [RAM image](#ram-image)
    - [Floppy image](#floppy-image)
    - [Harddisk image](#harddisk-image)


- [Examples](#examples)

- [Changes](#changes)

## Introduction

- Basic information
    - Homepage: <http://tinylab.org/linux-0.11-lab>
    - Repository: <https://github.com/tinyclub/linux-0.11-lab>

- Features
    - compilable with many different versions of Gcc.
    - has been tested under modern Linux, Mac OS X.
    - add bulit-in qemu and bochs support, include running and debugging.
    - integrate different prebuilt rootfs (floopy, ramdisk and harddisk).
    - allow to generate callgraph of every specified function
    - a Dockerfile added to simplify this setup on other systems (e.g. Windows).


## Build on Linux

### Linux Setup

* The Linux distributions: debian and ubuntu (>= 14.04) are recommended
* Install the lab

        $ git clone https://github.com/tinyclub/cloud-lab.git
        $ cd cloud-lab/ && tools/docker/choose linux-0.11-lab

        $ tools/docker/build        # Build ourselves
        or
        $ tools/docker/pull         # Pull from docker hub

        $ tools/docker/run

* The above command will start a VNC page, login with the password printed in the console

### Hack Linux 0.11 on Linux

    $ make help		// get help
    $ make  		// compile
    $ make start-hd	// boot it on qemu with hard disk image
    $ make debug-hd	// debug it via qemu & gdb, you'd start gdb to connect it.
    $ make start-hd G=0 // Use curses based terminal instead of graphics, friendly for ssh login
    $ QEMU_PREBUILT=0 make start-hd // Don't use prebuilt qemu. latest qemu only boot with hd rootfs

    $ make switch       // switch to another emulator, between qemu and bochs
    Switch to use emulator: bochs


    $ gdb images/kernel.sym
    (gdb) target remote :1234
    (gdb) b main
    (gdb) c

    $ gdb boot/bootsect.sym  // debug bootsect, step one instruction
    (gdb) target remote :1234
    (gdb) b _start
    (gdb) si

    $ gdb boot/setup.sym     // debug setup, similar as above
    (gdb) target remote :1234
    (gdb) b _start
    (gdb) si


Optional

    $ echo "add-auto-load-safe-path $PWD/.gdbinit" > ~/.gdbinit  // let gdb auto load the commands in .gdbinit

## Build on Mac OS X

**Note**: A simpler method is use Docker Toolbox with our Dockerfile, see [Build on the other systems](#build-on-other-systems-include-mac-os-x-windows)

### Mac OS X Setup

* Install xcode from "App Store"
* Install Mac package manage tool: MacPorts from http://www.macports.org/install.php

    * Check your OS X version from "About This Mac", for example, Lion
    * Go to the "Mac OS X Package (.pkg) Installer" part and download the corresponding version
    * Self update MacPorts

                $ xcode-select --switch /Applications/Xcode.app/Contents/Developer
                $ sudo port -v selfupdate

* Install cross compiler gcc and binutils

        $ sudo port install i386-elf-binutils i386-elf-gcc 

* Install qemu

        $ sudo port install qemu

* Install graphviz and cflow

        $ sudo port install GraphViz
        $ sudo port install cflow

* Install gdb. 'Cause port doesn't provide i386-elf-gdb, use the pre-compiled tools/mac/gdb.xz or download its source and compile it.

        $ cd tools/mac/ ; tar Jxf gdb.xz

Optional

    $ sudo port install cscope
    $ sudo port install ctags

    $ wget ftp://ftp.gnu.org/gnu/gdb/gdb-7.4.tar.bz2
    $ tar -xzvf gdb-7.4.tar.bz2
    $ cd gdb-7.4
    $ ./configure --target=i386-elf
    $ make

### Hack Linux 0.11 on Mac

Same as [Hack Linux-0.11 on Linux](#hack-linux-011-on-linux)

## Build on Other Systems (include Mac OS X, Windows)

If want to use this Lab on the other systems, such as Windows (and even Mac OS
X), with the Dockerfile support, everything is simplified.

Only need to install the docker toolbox, which is a lightweight Linux
distribution made specifically to run Docker containers, with this tool and our
Dockerfile, we can simply build a Linux 0.11 Lab on every system.

### Setup with Docker Toolbox and Dockerfile

- Install Docker Toolbox and boot into it

    please follow [Docker Toolbox Installation](https://www.docker.com/docker-toolbox).

- Build and Start the service

        $ tools/install-docker-lab.sh
        $ tools/run-docker-lab.sh
        $ tools/open-docker-lab.sh

- The above command with start a VNC page, login with 'ubuntu' password

### Hack Linux 0.11 on other systems

Same as [Hack Linux-0.11 on Linux](#hack-linux-011-on-linux)

## Hack Rootfs

Three different root filesystem images are stored in `rootfs/`:

* rootram.img -- RAM image
* rootimage   -- Floppy image
* hdc-0.11.img-- Harddisk image

### Ram image

`rootram.img` is mountable directly:

    $ mkdir /path/to/rootram/
    $ sudo mount -o rootfs/rootram.img /path/to/rootram/

### Floppy image

`rootimage-0.11` is a minix filesystem, must with `-t minix` obviously:

    $ sudo mkdir /path/to/rootimage/
    $ sudo mount -t minix rootfs/rootimage-0.11 /path/to/rootimage

### Harddisk image

`hdc-0.11.img` has a partition table, should be mounted with an offset:

    $ mkdir /path/to/hdc/
    $ fdisk -lu rootfs/hdc-0.11.img
    $ fdisk -l rootfs/hdc-0.11.img

    Disk rootfs/hdc-0.11.img: 127 MB, 127631360 bytes
    16 heads, 38 sectors/track, 410 cylinders, total 249280 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000

                  Device Boot      Start         End      Blocks   Id  System
    rootfs/hdc-0.11.img1               2      124031       62015   81  Minix / old Linux
    rootfs/hdc-0.11.img2          124032      248063       62016   81  Minix / old Linux

    $ sudo mount -o offset=$((2*512)) rootfs/hdc-0.11.img /path/to/hdc/

# Examples

Some examples are stored in `examples/` with their own README.md:

* syscall -- shows how to add a new system call

## Changes

* 2015-03-15, falcon <wuzhangjin@gmail.com>
    * launch a linux-0.11-lab project, see <http://tinylab.org/linux-0.11-lab>
    * integrate prebuilt rootfs images, no need to install extra images
    * integrate both qemu and bochs support and simplify the testing on emulators
    * add callgraph generation support
    * add docker support, allow to build the whole lab with one command
    * rewrite the document: README.md
    * Fix up "Reset-floppy Called"
    * clean up everything
    * repo: <https://github.com/tinyclub/linux-0.11-lab>

* 2012-04-30, yuanxinyu <yuanxinyu.hangzhou@gmail.com>
    * add Mac OS X support
    * add latest Gcc support
    * repo: <https://github.com/yuanxinyu/Linux-0.11>

* 2011-07-31, tigercn <moonlight.yang@gmail.com>
    * port to new system and gcc
    * see: <http://www.oldlinux.org/oldlinux/viewthread.php?tid=13681>

* 2008-10-15, falcon <wuzhangjin@gmail.com>
    * add gcc 4.3.2 with 32/64bit host support
    * rewrite boot/{bootsect.s,setup.s} with AT&T
    * replace tools/build.c with tools/build.sh
    * see: <http://www.oldlinux.org/oldlinux/viewthread.php?tid=11651&extra=page%3D1>

* 2005, jiong.zhao <gohigh@sh163.net>
    * write a [book for Linux 0.11](http://www.oldlinux.org/download/clk011c-3.0.pdf)
    * release a version for RedHat 9 with rootfs, compilers and blabla
    * site: <http://www.oldlinux.org>

* 1991, Linus
    * write and release the original Linux 0.11.

