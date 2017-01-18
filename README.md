# STM32 open source development notes


## Introduction
**TODO:** Need to write this section

## Install compiler / build tools (generic, "bare metal" ARM)
We'll use the GNU ARM Embedded Toolchain, which appears to be the same software that is used by the System Workbench IDE available via ST's website.
(I didn't dig deeply, just discovered the binary programs like `arm-none-eabi-gcc` in `Ac6/SystemWorkbench/plugins/fr.ac6.mcu.externaltools.arm-none.linux64_1.7.0.201602291252/tools/compiler/bin/`.)
You can download the compiler (here on developer.arm.com)(https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads),
choosing a package suitable for your operating system. However, since there was a 64-bit Linux package available for my distribution (Debian stretch/sid x86_64)


## Pre-reqs:
Install git, cmake, build-essential, libusb-1.0, and libgtk-3-dev packages

## Building open source ST-LINK software:
```
git clone git@github.com:texane/stlink.git
cd stlink
make
```

## Installing the ST-LINK software
Since there are shared libraries, modprobe/udev rules, etc. I recommend installing using `DESTDIR=`
(or omitting the parameter altogether) so that the files are installed into the standard, system-wide places.
(This usually requires root permission so you'd need to use `sudo` or equivalent.)

```
cd build/Release
make install DESTDIR=/installation/path/root
```

After completing these steps successfully you should have `st-flash`, `st-info`, `st-term`, etc. binaries in `$DESTDIR/usr/local/bin`
You should be able to view help information using the `man` command now, e.g. `man st-util`
However, if you try to run one of the programs, e.g. `st-info  --probe`
to see if attached programming/loading devices are detected, you might see an error message like the following:

```
/usr/local/bin/st-info: error while loading shared libraries: libstlink.so.1: cannot open shared object file: No such file or directory
```

This will happen if the system's shared library tools' configuration needs to be updated.
Usually you can fix this by running `ldconfig` as root (i.e. `sudo ldconfig`), which will tell the system to re-check all the places it knows about for library information. If you installed `stlink` to a custom `DESTDIR` then you may need to configure `/etc/ld.so.conf` (or your system's equivalent) to tell `ldconfig` where to find the shared library file(s). 
Once the libraries are set up, you should be able to run `st-info --probe` and get output like `Found 1 stlink programmers` (if you have 1 ST-LINK device connected).

To download the contents of an attached STM32 device's flash memory (assuming protection bits aren't set) to a file try running the following command:
```
st-flash read firmware-dump.bin 0x8000000 $(st-info --flash)
```

You should see output similar to this:
```
st-flash 1.2.0-166-g77acc50
2017-01-18T11:22:11 INFO stlink/src/common.c: Loading device parameters....
2017-01-18T11:22:11 INFO stlink/src/common.c: Device connected is: F334 device, id 0x10016438
2017-01-18T11:22:11 INFO stlink/src/common.c: SRAM size: 0x3000 bytes (12 KiB), Flash: 0x10000 bytes (64 KiB) in pages of 2048 bytes
```
and you should be able to confirm that the size of the output file is as expected using `ls -l firmware-dump.bin`

If you're feeling confident (or just don't care about potentially losing whatever is programmed into the device) you can try erasing it and reprogramming it
with the saved .bin file  by running `st-flash erase` then `st-flash write firmware-dump.bin 0x8000000`

You should see output similar to the following:
```
st-flash 1.2.0-166-g77acc50
2017-01-18T11:28:45 INFO stlink/src/common.c: Loading device parameters....
2017-01-18T11:28:45 INFO stlink/src/common.c: Device connected is: F334 device, id 0x10016438
2017-01-18T11:28:45 INFO stlink/src/common.c: SRAM size: 0x3000 bytes (12 KiB), Flash: 0x10000 bytes (64 KiB) in pages of 2048 bytes
Mass erasing
```

```
st-flash 1.2.0-166-g77acc50
2017-01-18T11:30:51 INFO stlink/src/common.c: Loading device parameters....
2017-01-18T11:30:51 INFO stlink/src/common.c: Device connected is: F334 device, id 0x10016438
2017-01-18T11:30:51 INFO stlink/src/common.c: SRAM size: 0x3000 bytes (12 KiB), Flash: 0x10000 bytes (64 KiB) in pages of 2048 bytes
2017-01-18T11:30:51 INFO stlink/src/common.c: Ignoring 51372 bytes of 0xff at end of file
2017-01-18T11:30:51 INFO stlink/src/common.c: Attempting to write 14164 (0x3754) bytes to stm32 address: 134217728 (0x8000000)
Flash page at addr: 0x08003000 erased
2017-01-18T11:30:51 INFO stlink/src/common.c: Finished erasing 7 pages of 2048 (0x800) bytes
2017-01-18T11:30:51 INFO stlink/src/common.c: Starting Flash write for VL/F0/F3 core id
2017-01-18T11:30:51 INFO stlink/src/flash_loader.c: Successfully loaded flash loader in sram
  6/6 pages written
2017-01-18T11:30:52 INFO stlink/src/common.c: Starting verification of write complete
2017-01-18T11:30:52 INFO stlink/src/common.c: Flash written and verified! jolly good!
```







# Setup build tools
**TODO:** Need to write this section
```
/home/jquant/software/Ac6/SystemWorkbench/plugins/fr.ac6.mcu.externaltools.arm-none.linux64_1.7.0.201602291252/tools/compiler/bin/arm-none-eabi-gdb
```
