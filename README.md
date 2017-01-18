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


# Setup build tools
**TODO:** Need to write this section
```
/home/jquant/software/Ac6/SystemWorkbench/plugins/fr.ac6.mcu.externaltools.arm-none.linux64_1.7.0.201602291252/tools/compiler/bin/arm-none-eabi-gdb
```
