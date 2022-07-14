---
layout: post
title: "My new favorite CPU"
---

# Background: keeping older PCs running

When the motherboard in my main PC died in the summer of 2021,
I considered two possible ways to revive it:

1. Buy all new hardware (motherboard, CPU, memory, etc.)
2. Just replace the motherboard

Being a complete cheapskate, I of course opted for option \#2.

My PC is Haswell (Intel 4th-gen) based. Although Haswell CPUs are
a bit on the older side now (being new in 2013–2014), they are still
quite capable. My main PC's CPU, the Core i7-4790K, is still fairly
competetive with modern CPUs, and certainly is more than adequate
for anything I do. (Note that the 4790K is not the CPU this blog
post is about, although it is a great CPU. I guess I'm burying
the lede here.)

I looked around for a replacement motherboard, and discovered
that the Haswell-era Dell Optiplex systems — specifically, the
Optiplex 7020, 9020, and XE2 — are available inexpensively
in large quantities on the used market. They have a few quirks
related to proprietary connectors, fans, and sensors, but these quirks
are fairly easy to deal with. I've made a couple youtube videos
about this:

* [PC Tech, Episode 01: How I fixed my lab PC for (fairly) cheap](https://youtu.be/A5_8kmYOViI)
* [PC Tech, Episode 03: Building a useful PC from (mostly) junk](https://youtu.be/gU25sZN5XGg)

# Haswell Xeon CPUs

Alongside its "desktop" CPUs, the Core i3, i5, and i7, Intel also
has its Xeon CPU lines, which are intended for servers and workstations.  The
<a href="https://en.wikipedia.org/wiki/List_of_Intel_Xeon_processors_(Haswell-based)">E3 Haswell Xeon CPUs</a>
are essentially just repackaged versions of Core i5 and i7 CPUs. For whatever
reason, they tend to be less expensive used than their Core i5 and Core i7
counterparts. Some of them don't have integrated graphics, so you'll need a
discrete GPU in that case.

Which brings me to...

# The Xeon E3-1271 V3

So, here's the star of this blog post (photo from
[CPU world](https://www.cpu-world.com/sspec/SR/SR1R3.html), I forgot
to take a picture of the one I'm using):

<center>
<img style="width:640px;" alt="Xeon E3-1271 V3 photo" src="{{site.baseurl}}/img/e3_1271_v3.jpg">
</center>

Behold the Xeon E3-1271 V3!

It's a "Haswell refresh" CPU, so slightly faster clock and slightly lower energy
consumption than the corresponding non-refresh Haswell CPUs. The E3-1271 V3
has a base frequency of 3.6 GHz, which means it's more or less equivalent to the
Core i7-4790.  However, it's quite a bit less expensive. I had no problem scoring one on
Ebay for $35, while (at the time this blog post was written) you'll generally pay more
than $50 for the i7-4790.  (I guess that means integrated graphics are worth
about $15?)

The E3-1271 V3 doesn't have integrated graphics, so you will need a discrete
GPU.  I've also heard (but can't confirm from direct experience) that some
desktop motherboards might need a BIOS update to handle Xeon E3 CPUs, or might
not work at all with a Xeon CPU installed. I've found that Optiplex 7020, 9020,
and XE2 motherboards support Xeon CPUs without any issues.

I will definitely keep my i7-4790K in my main PC, but for "auxiliary" PCs
I think the E3-1271 V3 will probably be my new preferred option, as long as
I have a reasonable graphics card I can pull out of the bin.

I know many PC enthusiasts like to be running the latest and greatest hardware.
My personal feeling is that all PCs have been ridiculously powerful for the past
10 years, to the point where older hardware is more than adequate for even
demanding tasks like teleconferencing, video editing and rendering,
software development, FPGA synthesis, etc.  So, I find the thought of buying
a CPU like the E3-1271 V3 for $35, and then using it for real work,
to be deeply satisfying somehow.
