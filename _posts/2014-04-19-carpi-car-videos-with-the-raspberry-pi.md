---
layout: post
title: "CarPi: Car Videos with the Raspberry Pi"
description: ""
category: 
tags: [carpi, electronics, raspberrypi, hacking]
---

# No more DVDs!

As I [mentioned previously](/2014/01/29/hacking-on-the-raspberry-pi.html), having a way to play movies in the car is great for long car trips, but car DVD players are, let's say, frustrating in many ways:

* A parent must unbuckle and turn around to put in a disc, operate the menu, etc.
* Long, long waits before you actually reach the root menu
* DRM in recent DVDs means they often don't play correctly (I'm looking at you, [Disney](http://www.disney.com))
* Unreliability (disc won't load, take it out, brush it off, cross fingers, put it back in, repeat)

Over my winter break I put together a [Raspberry Pi-based solution for playing movies in the car](https://github.com/daveho/carpi).  In this blog post I will describe the whole setup, link to relevant details, and reflect on the project and how it could be improved.  Overall, it works quite well and is (IMO) far less hassle than DVDs.

## Hardware

The concept for the hardware was to combine the Raspberry Pi with a user interface (display and controls) and package them in a form that would be easy for the parents in the front of the car to use.  I initially considered using a touchscreen, but eventually decided to go with a plain LCD and pushbuttons.  The LCD turned out to be easy: [fbtft](https://github.com/notro/fbtft) supports lots of small/cheap LCDs, and I scored an ILI9340-based LCD on eBay for about $6, which worked perfectly with fbtft.  The pushbuttons turned out to more difficult (high-quality PCB-mount pushbuttons are essentially impossible to find), but eventually I arrived at the idea of using Cherry MX keyswitches, which worked out extremely well.  After a bit of breadboarding (using an attiny4313 for debouncing the keyswitches), I designed a PCB with Eagle CAD, sent it off to [OSHPark](http://www.oshpark.com), threw everything together in a laser-cut acrylic enclosure (fabbed by [Pololu](http://www.pololu.com)), and presto, the device:

> <a href="https://raw2.github.com/daveho/carpi/master/enclosure/pic-big.jpg"><img src="https://raw2.github.com/daveho/carpi/master/enclosure/pic-sm.jpg" /></a>

You can read the [instructions for building it](https://github.com/daveho/carpi/wiki/Building) if you're interested.

Here is the device in its native environment:

> <a href="/img/carpi-inplace.jpg"><img alt="CarPi, in place" src="/img/carpi-inplace-sm.jpg"></a>

## Software

The software is pretty much just a menu system and support for running [omxplayer](https://github.com/popcornmix/omxplayer) as a subprocess.  In the interest of getting something up and running quickly, I decided to write the software using [ncurses](http://www.gnu.org/software/ncurses/) rather than an actual GUI.

I wrote up some [documentation](https://github.com/daveho/carpi/wiki/Using) describing how to use the software, which includes some screenshots.  Here's a screenshot which should give you a sense of what it looks like:

> <img alt="main menu" src="https://raw.github.com/wiki/daveho/carpi/img/screenshot0.png" />

It's not fancy, but it gets the job done.

(Believe it or not, that is the famous Sun 12x22 console font.  I started my professional career programming on a [Sparcstation IPX](http://www.obsolyte.com/sun_ipx/), so it's oddly satisfying to see this font again in a new context.)

I encountered a few interesting technical issues while working on the software, including figuring out how to do interrupt-driven GPIO from C++ (pretty easy, as it turned out) and how to keep the LCD backlight from powering down (quite hard: I had to use the uinput device to make the button presses register as actual keypresses so that the backlight would wake up).

## AV setup

The Raspberry Pi-based system replaces a Philips PD7012 dual screen DVD player.  The good news here is that the slave screen from this system is a bog-standard composite video monitor, so after ordering another identical slave screen from eBay for $25, I had the two screens I needed.  I used a Boss BV-AM5 video signal amplifier to ensure a full-strength signal to both screens.  (Perhaps a passive splitter would have sufficed, but it was only $11.)

One interesting characteristic of the PD7012 slave screens is that they have a single 2.5mm input for both video and stereo audio.  As it turns out, the cable that allows the master unit of the PD7012 to connect to connect to an output device (such as a TV) also works fine for feeding input (both video and audio) to the slave screen.  I ordered two such cables from eBay, which connects the outputs of the BV-AM5 to the slave screens.  I only used the video signal, since I'm not using the audio output of the slave screens.

For audio, I am using the aux input of the car stereo.  We have a base model 2007 Honda Fit, so we needed to have a third-party aux adapter installed.  ([Wyvon Audio Installations](http://wyvonaudio.com/) did the installation, for which I am grateful, since removing the stereo on a 2007 Fit is not a task for the faint of heart.)

Here is a schematic of the entire setup:

> <a href="/img/avDiagram.jpg"><img alt="AV setup diagram" src="/img/avDiagram-sm.jpg" /></a>

The "piece of wood" serves two functions: anchoring the video signal amplifier, and distributing power to the devices that need 12V.  One of the benefits of working at an engineering school is access to all kinds of tools and connectors, and I was able to use some heat-shrink tubing, a terminal block, and some ring terminals to achieve a relatively neat end result:

> piece of wood picture

This sits in a slot between the front seats that seems to have been designed to hold CDs, and since the piece of wood is 5"x5", it fits in this slot perfectly:

> <a href="/img/carpi-pieceofwood-inplace.jpg"><img alt="piece of wood, in place" src="/img/carpi-pieceofwood-inplace-sm.jpg" /></a>

## Does it work?

Heck, yeah!

> <a href="/img/carpi-demo.jpg"><img alt="piece of wood, in place" src="/img/carpi-demo-sm.jpg" /></a>

Audience reactions have been positive:

> <a href="/img/carpi-thumbsup.jpg"><img alt="piece of wood, in place" src="/img/carpi-thumbsup-sm.jpg" /></a>
