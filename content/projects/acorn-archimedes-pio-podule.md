+++
title = 'Acorn Archimedes PIO Podule'
date = 2023-09-05T10:30:17-06:00
draft = false
+++
This has been a very long-running project!  I started it back in 2016 (or was that 2017?).

The aim is to produce a classic Archimedes *Podule* that provides many things!

I started the project when my old Acorn Archimedes A5000 failed to boot after the CMOS battery leaked and chewed through part of the PCB preventing the HDD from working.

To date, there have been two revisions of the Podule.

## Version 1

{{< figure src="/images/pio_v1.png" caption="Original prototype, the level-shifters were too slow" >}}

{{< figure src="/images/pio_v1a.png" caption="Updated prototype, using 8-bit level-shifters" >}}

Cobbled together on vero-board, this was a proof-of concept.  The concept was 100% proven, and I learnt many things.

Skills acquired:

* FPGA design (Xilinx Spartan6)

## Version 2

{{< figure src="/images/pio_v2.jpg" caption="First PCB design - other than one manufacturing defect, it worked perfectly" >}}

An honest-to-goodness real PCB with surface mounted components.

Skills acquired:

* KiCad
* SMD techniques

# Where things stand

The podule currently works 100% and provides access to an image of my old 80MB Connor HDD via a new ADFS-linked Filing System (PiFS).
Relocatable modules written in pure ARM assembler for Podule communication and the Filing system.

# What needs to be done

Since the PIO-Podule provides direct communication to a Raspberry Pi, the possibilities are endless.
I'd like to get a couple more functions complete bfore I think it's ready for release:

* Save the support Modules in the spare memory on the FPGA config EEPROM (so they don't have to be loaded by a !Boot file from HDD/Floppy)
* Implement a Serial Port override so that the Archimedes serial port terminates on the Raspberry Pi Telnet interface
