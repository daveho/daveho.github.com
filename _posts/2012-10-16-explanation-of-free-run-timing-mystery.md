---
layout: post
title: Explanation of free run timing mystery?
tags: [mc68008, electronics, retrocomputing]
---

In the [last post](/2012/10/14/mc68008-free-run) I described
a circuit in which the MC68008 CPU executed a *free run*; basically,
it executed an unending series of **nop** instructions.
An LED connected to A19 (the high address line) should blink on
and off once each time the CPU cycles through its 1 MB address space.

Since a **nop** takes 8 cycles to execute, the CPU should execute
1,000,000 **nop** instructions per second with an 8 MHz system clock.
With 1,048,576 instructions in the address space, this should
have caused the LED to blink about once per second.  However,
I observed *two* blinks per second, which would imply a **nop**
executing every 4 cycles, not 8.

I thought of a possible explanation: the CPU could be prefetching
the next instruction while the current instruction is executing.
An MC68008 read cycle requires 4 clock cycles, so each pair
of successive **nop** instructions would overlap by 4 cycles,
resulting in the effective execution of one **nop** every 4 cycles.
[One web page I found](http://www.cse.dmu.ac.uk/~cfi/Networks/WorkStations/Workstations4.htm)
does seem to indicate that the MC68000 (and its sibling, the '08)
has a two-word prefetch queue.

I'll know for sure when I observe the low address lines with a
logic analyzer.
