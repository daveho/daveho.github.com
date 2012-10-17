---
layout: post
title: Free run with logic analyzer - great success!
tags: [mc68008, electronics, retrocomputing, logicanalyzer]
---

I wanted to be sure that my
[mc68008 free run circuit](/2012/10/14/mc68008-free-run) was really
working, so I connected the low 8 pins of the address bus to a logic
analyzer.  This allows me to see the low byte of the addresses
from which the CPU is (hopefully) fetching instructions.

Sure enough, the CPU is generating successive addresses,
at least in the low 8 bits (click for larger image):

<a href="/img/figures/addrBusCapture.png"><img style="margin-left: 80px; width: 422px;" src="/img/figures/addrBusCapture.png" /></a>

Yay logic analyzer!

An additional benefit from this experiment was using a really
fancy Agilent E3648A power supply to power the circuit,
from which I learned that the circuit is drawing 0.024 amps
of current.  I wouldn't mind owning one of these myself,
but they'll set you back about $1,000.

At this point I am declaring the free run circuit a success.
The next step will be to add a ROM, so that I can have the CPU
execute actual code, and some kind of output device so that the
code can provide some indication that it is running correctly.
The output device will probably be an 8 bit register
(74hct74 or similar) connected to a bank of LEDs:
in other words, [blinkenlights](http://en.wikipedia.org/wiki/Blinkenlights)!
