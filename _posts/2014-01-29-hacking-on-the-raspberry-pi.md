---
layout: post
title: "Hacking on the Raspberry Pi"
description: ""
category: 
tags: [electronics, raspberrypi, hacking]
---

\[Note for the impatient: there is a picture if you scroll down.\]

# Whither Pi?

<img alt="Image copyright (c) Raspberry Pi Foundation" style="float: right;" src="{{ site.url }}/img/raspi-logo-sm.png" />

I've been aware of the [Raspberry Pi](http://www.raspberrypi.org/) for a while.
(Despite the long stretches of inactivity on my blog, I don't live under a rock.)
However, until very recently I haven't felt any particular need to have one.

That all changed in December.  As with many families with younger kids,
for us playing movies in the car is an essential component of longer car trips.
Unfortunately, car DVD players are pretty horrible: unreliable, user-hostile,
and just generally permeated with evil and crappiness.  For a while I've been
wanting a device that could play digital videos (i.e., DVD rips) in the car.  Our kids
are a bit young for tablets, and I'm not sold on the tablet concept in general.
Another issue is display hardware: most inexpensive car LCDs take composite
video input, but it's not easy to find a device that does composite video
output.

My realization in December was that the Raspberry Pi outputs composite
video!  Suddenly, I had to have one!  The break between the Fall and
Spring semesters is an excellent time to work on "fun" projects, so
I started ordering hardware.  My vision was a handheld
device (containing the Raspberry Pi) that the parents in the front seat
of the car would control, and which would drive the LCDs in the back seat
via composite video.  Audio output would feed into the aux input on the
car stereo.  All of the hassles of car DVD players would be eliminated:
no interminable delay waiting for the disc menu, no skipping, no
turning around to change discs, etc.

# Building stuff

In pursuit of my vision, here is what I did over my break:

* Learned how to use the Raspberry Pi: I used [Raspbian](http://www.raspbian.org/),
  so I felt right at home, since my preferred Linux flavor is Debian/Ubuntu.
* Learned how to use [LibreCAD](http://librecad.org/cms/home.html), specifically
  to design an enclosure (a durable outer casing to prevent fall apart)
  made with laser cut acrylic.
* Learned about [fbtft](https://github.com/notro/fbtft), a very nifty project
  to support small LCD displays as Linux framebuffers.  I decided to use
  one based on the ILI9340 controller.  These can be found on Ebay for
  about $6.
* Designed a PCB using [Eagle CAD](http://www.cadsoftusa.com/) for the pushbuttons
  that would serve as the mechanism for user input.  I had originally thought
  about using a touchscreen, but they are somewhat tricky to interface with
  the Raspberry Pi, and this would have complicated the software I would need
  to write for the user interface.  I actually designed two PCBs: the
  first one using standard 12mm x 12mm pushbuttons, and the second using
  Cherry MX keyswitches.  The original pushbuttons felt extremely cheesy,
  and I also managed to orient the header connecting the board to the
  Raspberry Pi in a way that would have made the cabling difficult.
  Going with the Cherry MX switches worked out really well (see below!)
* Accumulated massive quantities of parts and hardware from Ebay and other
  sources.  This seems to be an inevitable feature of any hardware project.
* Wrote a simple menu system using [ncurses](http://www.gnu.org/software/ncurses/).
  Not only was this a lot easier than an actual GUI, it is less resource
  intensive, and it allows the LCD to be used as a debug console
  without the need for an external display or network connection: just
  plug in a USB keyboard and mouse.

You can read about all of the gory details in the
[build log](https://raw.github.com/daveho/carpi/master/notes/log.txt)
if you're interested.

All of the stuff I did is in a git repository (and, *of course*, is open source):

> <https://github.com/daveho/carpi>

# The weird thing I made

Anyway, here's what I have so far:

> <a href="https://raw2.github.com/daveho/carpi/master/enclosure/pic-big.jpg"><img src="https://raw2.github.com/daveho/carpi/master/enclosure/pic-sm.jpg" /></a>

Pretty cool, eh?  Just to confirm what you're probably thinking:
it is *really* fun to press those keys.

# What's next

There are still two things I need to do in order to complete the project:

1. Finish the software: right now, it plays music, but playing video
   doesn't work yet.  I don't anticipate that video will be too hard
   to get working, because [omxplayer](https://github.com/popcornmix/omxplayer)
   works fine from the command line, and my menu system just needs to
   run it as a subprocess.
2. Get all of the hardware installed in the car.  I believe I have
   all of the parts I need.  One annoying detail is that our 2007 base model
   Honda Fit didn't come with an aux input for the car stereo, so we
   will need to have one installed.

I will update the blog when I have more progress to report.
