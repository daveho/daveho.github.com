---
layout: post
title: "Using ATX power supplies with 4th-gen Optiplex systems"
---

# ATX power supplies and 4th Optiplex systems

If you read [my previous post]({{site.baseurl}}/2022/07/14/my-new-favorite-cpu.html), you're
aware that I really like the 4th-gen Optiplex systems (7020, 9020,
and XE2) as a source of cheap but good PC components.

One challenge of building PCs with this era of Dell hardware is dealing
with various proprietary connectors. One such connector is the 8-pin
connector to connect the PSU with the motherboard.  You can, of course,
use a Dell power supply in your build, which has this connector natively.
However, you might want to use a standard ATX power supply with a 24-pin
connector. In this case, you can use a [24 pin to 8 pin adapter
cable](https://www.moddiy.com/products/Dell-OptiPlex-9020-PSU-Main-Power-24-Pin-to-8-Pin-Adapter-Cable-30cm.html).
But...not every ATX PSU will work, even if you use this adapter!

# What the adapter does

My understanding is that besides adapting the 24-pin output from the PSU
to the 8 pin connector on the motherboard, it also has a boost converter to
convert the 5V standby voltage from the PSU to the 12V standby voltage
that the Dell motherboard will expect.

# Why some ATX PSUs work and some don't

It is also my understanding that Dell motherboards derive all of the system
power supply rails from a single 12V output from the PSU.  I think this is
the reason that some ATX PSUs work and some don't: Dell motherboards need
lots of current on the 12V rail, and not all ATX PSUs are capable of delivering it.

I will *speculate* that it is better to have an ATX PSU that delivers 12V on
a single beefy rail, rather than splitting it over several 12V rails.
(However, one of the PSUs I've tested has a single large 12V rail, but it
doesn't work with Optiplex motherboards.)

So...basically I have no real idea why some PSUs work with Optiplex motherboards,
and others don't.

# Experiments

Here is a table summarizing ATX power supplies that I have tried personally,
and whether or not they work with 4th-gen Dell motherboards.

ATX PSU | Power | 12V rail(s) | Result
------- | ----- | ----------- | ------
[Xigmatek NRP-PC402](https://www.newegg.com/xigmatek-nrp-pc-series-acxtnrp-pc402-400w/p/N82E16817815007) | 400W | Split |  Does not work
[Raidmax RX-700AC](https://www.newegg.com/raidmax-blackstone-series-rx-700ac-700w-continuous-power/p/N82E16817152042) | 700W | Split | Does not work
[Aresgame AGW650](https://www.amazon.com/Supply-Bronze-Certified-ARESGAME-AGW650/dp/B09C5G1S9X) | 650W | Single | Does not work
[EVGA 700 BR](https://www.amazon.com/EVGA-100-BR-0700-K1-Bronze-Power-Supply/dp/B07DTP6MWS) | 700W | Single | Works!
[EVGA 600W W1](https://www.amazon.com/EVGA-Certified-100-W1-0600-K1-Power-Supply/dp/B0160XJAQK) | 600W | Single | Works!
[Corsair CX600](https://www.amazon.com/CORSAIR-Bronze-Certified-Non-Modular-Supply/dp/B0092ML0OC) | 600W | Single | Works!

All of the above are 80+ Bronze rated for efficiency, with the exception of the
EVGA 600W W1, which is 80+ White.

The EVGA 700 BR is a very solid PSU, and Best Buy occasionally has them on sale for $40,
which is a great deal. This is my go-to ATX PSU if I'm not deciding to be a complete
cheapskate.  I'm using one in my main PC, a case-swapped Optiplex 7020 that has
been an absolute workhorse, with no issues whatsoever.

The EVGA W1 series seems to have a less than stellar reputation among PC enthusiasts.
However, my impression is that they're an acceptable choice if you aren't going to be
pushing them hard. I tend to run fairly low end hardware, and I don't have any graphics
cards which require auxiliary power, so I'm pretty sure the EGVA 600W W1 will
work fine in any application I would need to use it in.

I bought the Corsair CX600 cheaply on Ebay just for fun, and it seems to work fine
with 4th-gen Optiplexes, and also have a reasonable reputation.

I really wish I knew why the Aresgame AGW650 doesn't work. It claims to have a
single 12V rail capable of delivering 588W, which seems like it would be more than
adequate for a 4th-gen Optiplex. But, I couldn't get it to work.
