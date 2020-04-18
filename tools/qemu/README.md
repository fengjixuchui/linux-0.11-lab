
qemu 0.10.6 for X86 on Linux.

Built from [Linux Lab](https://github.com/tinyclub/linux-lab)

    $ vim boards/i386/pc/Makefile  // make sure QEMU is v0.10.6
    QEMU = 0.10.6

    $ make B=i386/pc
    $ make qemu
