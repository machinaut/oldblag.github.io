---
layout: post
title:  "Getting Started with the TMS570 USB Kit"
date:   2014-04-02 00:32:08
categories:
---

The [TMS570 USB Kit][TMS570USB]($79) is an affordable development kit for the [TMS570][TMS570] microcontroller from TI (Texas Instruments) with dual [Cortex-R4][CortexR4] cores — the deeply embedded real-time processor based on the [ARMv7-R][ARMv7R] architecture.  In the past I’ve worked with Cortex-A and Cortex-M cores, but this will be my first project with the R series.

My first goal is to get through the steps in [TMS570LS Getting Started][TMS570LSGettingStarted] using open source software tools.  This rules out TI’s [Code Composer Studio][CodeComposerStudio], which is usually used to interface to these chips.

[TMS570USB]: http://www.ti.com/tool/TMDX570LS20SUSB
[TMS570]: http://www.ti.com/lsds/ti/microcontroller/safety_mcu/tms570_arm_cortex-r4/overview.page
[CortexR4]: http://www.arm.com/products/processors/cortex-r/cortex-r4.php
[ARMv7R]: http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.ddi0406c/index.html
[TMS570LSGettingStarted]: http://processors.wiki.ti.com/images/b/bd/TMS570LS_Getting_Started.pdf
[CodeComposerStudio]: http://www.ti.com/tool/ccstudio

## Documents
Two important references for the chip are the [Technical Reference Manual][TRM] (often abbreviated “TRM”) and the [datasheet][DS].

[TRM]: http://www.ti.com/lit/ug/spnu489c/spnu489c.pdf
[DS]: http://www.ti.com/lit/ds/spns141f/spns141f.pdf

## Schematic
Looking at the [schematic][Schematic] shows the chip connected to USB via a [FT2232H][FT2232H].  We have a [JTAG][JTAG] connection through the A bus and a [UART][UART] through the B bus to the chip’s SCI/LIN 1 (Serial Communication Interface/Local Interconnect Network) port.  More info on this in Chapter 13 of the [TRM][TRM].

I’m focusing on the JTAG connection for now, but the UART will be useful later when interacting applications running on the chip.

[Schematic]: http://processors.wiki.ti.com/index.php/File:TMS570LS20216_USB_STICK_Schematic.pdf
[FT2232H]: http://www.ftdichip.com/Support/Documents/DataSheets/ICs/DS_FT2232H.pdf
[JTAG]: http://en.wikipedia.org/wiki/Joint_Test_Action_Group
[UART]: http://en.wikipedia.org/wiki/Universal_asynchronous_receiver/transmitter

## JTAG
Plugging in the device it enumerates as a [XDS100v2][XDS100v2].  To check this on a Mac, use the `system_profiler SPUSBDataType` command.

[XDS100v2]: http://processors.wiki.ti.com/index.php/XDS100#XDS100v2_Features

## OpenOCD
The software to control the JTAG connection will be [OpenOCD][OpenOCD].  Basic instructions for [running][OCD Running] OpenOCD require a config file.  OpenOCD ships with a lot of config files pre made, but we’ll have to make one ourselves for this board.

[OpenOCD]: http://openocd.sourceforge.net/
[OCD Running]: http://openocd.sourceforge.net/doc/html/Running.html

### Config File
My first shot at a config file is up on [github][config file], as well as a simple script to test it.  It uses the existing configs for the FT2232 and the TMS570, but generates some errors.

[config file]: https://github.com/machinaut/tms570

## To Be Continued
I’ll try to keep on documenting my progress in these posts.  Hopefully the progression will be JTAG -> Programming -> Software Development.

## Meta
As I’m writing this, I’m trying to err on the side of over-linking to external resources.  Embedded engineers might already know what a [microcontroller][microcontroller] is, but I still may link to it for the sake of less-familiar readers.

This blog will target a technically advanced audience, though, and I don’t expect non-technical folk to get much from it.

[microcontroller]: http://en.wikipedia.org/wiki/Microcontroller

