# HD6309SBC PIA Prototype Module Board

This is an expansion board for the HD6309 
single board computer I designed (see 
https://github.com/jimbro1000/HD6309sbc).

The board uses the highly adaptable
MC6821 as the basis of the design but it
is advised to use the WDC65C21N as a 
substitute to guarantee handling of the
3MHz clock speed used by default.

Dropping the clock speed on the main
compute board will allow the use of
lower rated PIAs

By default the MC6821 is rated at only
1MHz.  
The A rated chip (68A21) is safe to 1.5MHz.  
The B rated chip (68B21) is safe to 2MHz.

Compatible variants are similarly rated
and also top out at 2MHz.

The board includes an 8x32 prototype area
while PL1 exposes all 16 data lanes
available to the PIA. PL2 exposes the
4 PIA control lines (CA0,CA1,CB0,CB1) and
various useful signals from the main
board along with +5V and GND.

The available signal lines are CPU clock
and switched control bits Q1-Q7

## Addressing

By default the PIA is addressable at $xx00 
through to $xx03. Optionally the addressing
can be modified to sit at $xx04 (to $xx07).

The value of xx is determined and managed by
the "mapped memory" signal line, this is
expected to be set to $FF00, so the default
PIA address is $FF00.

Each PIA block offers four registers:

* $FF00 = PIA Data Register A (PA0-PA7) or
Data Direction Register A
* $FF01 = PIA Control Register A
* $FF02 = PIA Data Register B (PB0-PB7) or
Data Direction Register B
* $FF03 = PIA Control Register B

## Example Usage - Keyboard Input

The PIA can be programmed to scan an 8x8
keyboard matrix, using PA0-PA7 for the
vertical and PB0-PB7 for the horizontal

The scan needs to be triggered on a timed
basis to scan and expose the captured data
on the main data bus

The control registers for both sides of
the PIA need to be set for input and this
needs a sequence of writes:

1. bit 2 of control register A must be
set low
2. input bits of data register A must
be set low
3. bit 2 of control register A must be
set high
4. bit 2 of control register B must be
set low
5. output bits of data register B must
be set high
6. bit 2 of control register B must be
set high

This leaves the relevant bits of data
registers A and B in input mode and 
the data registers exposed to the
address bus. Not all of the bits need
to be used and this will be determined
by the keyboard matrix. For example
the CoCo and Dragon keyboard matrices
use a 7x8 matrix (this *is* a 6809 based
computer).

In effect the single bits of register B
are set high in sequence, and at each
step register A is scanned to see which
keys are pressed for the given row or
column (depending on which way the 
matrix is oriented)