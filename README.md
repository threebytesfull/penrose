# Penrose

This was created for a board exchange project organised among members of the
[Boldport Club](https://boldport.club/) in 2019. It has nothing to do with
Boldport, other than being inspired by their work!

## Preview

I haven't finished the code I was working on yet, so in the meantime here's
a binary with a handful of test patterns I used while assembling the
prototypes. There are nine different modes and you can move up and down between
them with the two user buttons. Mode 7 is the most useful if you're chasing
down dodgy LED connections - hit reset and then User1 twice to get there.

I'll put the full project in this repo shortly, but in the meantime there's the
schematic:

* [Penrose Schematic (PDF)](penrose-schematic.pdf)

And three binary images of the test firmware - which you use probably depends
on the tool you're using to program the chip:

* [penrose-test.elf](penrose-test.elf)
* [penrose-test.bin](penrose-test.bin)
* [penrose-test.hex](penrose-test.hex)

## Programming the Chip

Various programmers will be suitable - there are both SWD and JTAG connections
on the board. Instructions for the programmers I have immediately to hand
follow, assuming you've already got the GCC toolchain for ARM installed (I'll
document all that too for people who haven't).

### Black Magic Probe

Use the ELF binary file above. Assuming your Black Magic Probe is at
`/dev/cu.usbmodem123456781` and connected to the Cortex Debug header:

```bash
arm-none-eabi-gdb penrose-test.elf
```

In GDB, you need to connect to the Black Magic Probe, turn the target power on
and flash the chip:

```gdb
tar ext /dev/cu.usbmodem123456781
monitor tpwr enable
monitor swdp_scan
attach 1
load
compare-sections
kill
```

### STLink

You can use either a standalone STLink adapter or the one that's included in
the ST Discovery boards and the larger ST Nucleo boards (the Nucleo-32s are
permanently attached to their own target, I think).

Connect the 3V3 pin on the Penrose SWD header to the 3V3 pin on the STLink one
of the VDD pins on the ST board. Connect the SWCLK, SWDIO and GND pins to the
corresponding pins on the STLink header.

Start the `st-link` tool in a terminal window:

```bash
st-link
```

It should find a debug target and sit waiting for connections, most likely on
port 4242.

In another terminal window, start GDB:

```bash
arm-none-eabi-gdb penrose-test.elf
```

In GDB, you need to connect to the STLink and flash the chip:

```gdb
tar ext :4242
load
compare-sections
kill
```
