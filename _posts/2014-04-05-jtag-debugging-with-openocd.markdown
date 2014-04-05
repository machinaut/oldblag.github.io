---
layout: post
title:  "JTAG Debugging with OpenOCD"
date:   2014-04-05 02:06:08
categories:
---

## Fixing the Errors
In my [last post][last post] the OpenOCD output had some errors.  In particular:

    Info : 324 81 core.c:949 jtag_examine_chain_display(): JTAG tap: tms570.jrc tap/device found: 0x1b7b302f (mfg: 0x017, part: 0xb7b3, ver: 0x1)
    Warn : 325 81 core.c:949 jtag_examine_chain_display(): JTAG tap: tms570.jrc       UNEXPECTED: 0x1b7b302f (mfg: 0x017, part: 0xb7b3, ver: 0x1)
    Error: 326 81 core.c:949 jtag_examine_chain_display(): JTAG tap: tms570.jrc  expected 1 of 1: 0x0b8a002f (mfg: 0x017, part: 0xb8a0, ver: 0x0)
(NB: these messages are with the debug level at 3)

The error is because the JTAG ID we’re looking for is 0xb8a002f.  Digging through the config files we find this is defined in the TMS570 config file [target/ti_tms570.cfg][tms570 cfg].

The root of the problem is that our board is reporting different than expected — what we’re expecting is wrong.

[last post]: http://machinaut.github.io/2014/04/01/getting-started-with-the-tms570-usb-kit.html
[tms570 cfg]: http://openocd.zylin.com/gitweb?p=openocd.git;a=blob;f=tcl/target/ti_tms570.cfg;h=589e9d6cfd0c6c313727ad3367f964f8fff7aa70;hb=d7cbdee3e98d5dec9c8760f730d7a5096717ed73

### JTAG Identifiers
The processor datasheet[DS] should document what the proper JTAG ID for the part should be.  In our case, in section 4.8.1 we see the JTAG ID is 0x0B7B302F.  Generally, searching for “JTAG ID” in the datasheet is a good way to find it.

(We can see that this is the same as the response in the error messages above.)

At this point OpenOCD is successfully communicating with the device.

[DS]: http://www.ti.com/lit/ds/spns141f/spns141f.pdf

## GNU Debugger
The [GNU Debugger][GDB] (GDB) is an amazingly useful tool for debugging any software projects, and especially useful in conjunction with JTAG debugging.  OpenOCD acts as a “GDB Remote”, which lets us talk to the chip/memory/program over GDB.

Even though GDB is open source, it can be difficult to configure and build, so I recommend using [Linaro’s toolchain][linaro].

The [GDB documentation][GDB doc] will come in handy — it can get esoteric at times.

[GDB]: http://www.sourceware.org/gdb/
[linaro]: https://launchpad.net/gcc-arm-embedded
[GDB doc]: http://www.gnu.org/software/gdb/documentation/

## Blinking Lights
The quintessential basic action on a dev board is blinking a LED — so let’s try to do that first.  The [schematic][sch] shows that the bottom 6 LEDs are connected to HET0 through HET5.

In the [TRM][trm] (Chapter 18) HET is short for High End Timer, and there’s a lot that can be done to configure them.  For now we’ll just use them as dumb outputs.

This reads back the state of HETDOUT, then sets the odd lights on (by writing a 1 in HETDSET we cause the corresponding bit in HETDOUT to be set to 1).

    (gdb) x 0xfff7b854
    0xfff7b854:	0x00000000
    (gdb) set {int}0xfff7b858 = 0x00000015

Expect to spend quite a bit of time learning how the HETs work in order to do more with them.

[sch]: http://processors.wiki.ti.com/index.php/File:TMS570LS20216_USB_STICK_Schematic.pdf
[trm]: http://www.ti.com/lit/ug/spnu489c/spnu489c.pdf
