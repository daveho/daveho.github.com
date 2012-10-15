---
layout: post
title: An MC68008 Computer Project
tags: [mc68008, electronics, retrocomputing]
---

For a while I've been thinking about putting together a simple
computer based on the MC68008 microprocessor.  The MC68008 is
a variant of the [Motorola 68000](http://en.wikipedia.org/wiki/Motorola_68000),
a famous and widely used CPU series found in the original (pre-PowerPC)
Macs, the Amiga, Atari ST, many and early Unix workstations.

Unlike the MC68000, the MC68008 has an 8 bit data bus, which means
that it requires fewer wires or circuit traces to interface with
peripherals such as memory and I/O devices.  For this reason,
it's easier to build a computer with the MC68008 than the other
members of the series.

## Why the MC68008?

I think the seed for this project was planted when I read
[The Art of Electronics](http://frank.harvard.edu/aoe/), which has a
chapter describing a computer based on the MC68008.  At the time
I read AOE, I had never imagined that building a complete
computer system from chips was feasible for a student or hobbyist.

Since reading AOE, I have since played around a bit with microcontrollers
such as the [Intel 8051](http://en.wikipedia.org/wiki/Intel_8051)
and the [Atmel AVR](http://en.wikipedia.org/wiki/Atmel_AVR) series.
Microcontrollers are fun, and with relatively modern parts such as
the AVRs, pretty much all of the hardware you need, such as
program memory, RAM, general-purpose I/O pins, UARTs, etc. is
integrated, and you can program them in-curcuit using very simple
hardware.  This makes them so easy to use that they're almost *too* easy.
Putting together a system based on the MC68008 is a challenge, but
one that isn't so difficult as to be infeasible for a relative
novice.  (Famous last words, eh?)

Probably the most important reason I wanted to build something
based on the MC68008 is that it is from the era (the early 80s)
when home computers were still very new. For its time,
it was a fairly powerful CPU, and was used in one well-known
computer, the [Sinclair QL](http://en.wikipedia.org/wiki/Sinclair_ql)
(which famously was one of the computers that
[Linus Torvalds](http://en.wikipedia.org/wiki/Linus_torvalds)
learned to program on).  For those of us who where there,
the early years of home computing have a magic that is difficult
to describe.  Building something based on that technology
recaptures some of that magic.

## Sketch of system design

My planned initial system includes the following hardware:

* MC68008P (actually, TS68008P) CPU
* K6T4008 512 KB static RAM
* 27C512 64 KB EPROM (for firmware)
* MK68901 Multifunction Peripheral (MFP), which integrates timers, a UART, and
  8 general-purpose I/O pins

The initial system will allow the user to connect via RS-232.
A monitor program will handle user input and allow the user to
upload programs into RAM and execute them.

## Development philosophy

My development philosophy is simple:

* Use the *simplest* design I can think of
* Cheat whenever necessary

The MC68008 requires a fair amount of support circuitry for things
like resetting the CPU on power-up, signaling the completion of
bus transactions, dealing with interrupts, etc.  The "old school" approach
to designing the support circuitry using discrete ICs like logic gates,
encoders and decoders, counters, etc.  While this approach has a certain
nostalgia value, it's a pain.  Therefore, I decided to cheat by using
specialized ICs and programmable devices wherever it made sense.

For example:

* I am using a MAX1232 to generate the power-on reset pulse
* I'm using an integrated oscillator module to generate the system clock
* I will use [GAL](http://en.wikipedia.org/wiki/Generic_array_logic)s
  for glue logic such as generating chip select signals, generating
  the DTACK signal required by the MC68008 to end a bus transaction,
  and dealing with interrupts.
* I'm using static RAM rather than dynamic RAM

At the end of the day, I lose a bit of authenticity, but with fewer
and simpler components there are fewer chances to introduce design errors.

## Component availability

With vintage parts such as the MC68008, availability can be an issue.
[Ebay](http://www.ebay.com) is a good resource, although there are lots
of counterfeit parts out there.  I'm pretty sure that two alleged
MC68008 parts I bought on Ebay are fake.

At the time of writing (October 2012),
[West Florida Components](http://www.westfloridacomponents.com/) is selling
the TS68008CP10, which is a second-source version of the MC68008.
I have a few of them, and they seem authentic.

## What next

The first step is to design and build a clock and reset circuit.
That will be the subject of the next article.
