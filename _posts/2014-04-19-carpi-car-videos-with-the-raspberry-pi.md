---
layout: post
title: "CarPi: Car Videos with the Raspberry Pi"
description: ""
category: 
tags: [carpi, electronics, raspberrypi, hacking]
---

# No more DVDs!

As I [mentioned previously](/2014/01/29/hacking-on-the-raspberry-pi.html), having a way to play movies in the car is great for long car trips, but car DVD players are, shall we say, frustrating in various ways:

* A parent must unbuckle and turn around to put in a disc, operate the menu, etc.
* Long, long waits before you actually reach the root menu
* DRM in recent DVDs means they often don't play correctly (I'm looking at you, [Disney](http://www.disney.com))
* Unreliability (disc won't load, take it out, brush it off, cross fingers, put it back in, repeat)

Over my winter break I put together a [Raspberry Pi-based solution for playing movies in the car](https://github.com/daveho/carpi).  In this post I will describe the whole setup, link to relevant details, and reflect on the project and how it could be improved.  Overall, it works quite well and is (IMO) far less hassle than DVDs.

If you're interested in all of the gory details, there is a [build log](https://raw.githubusercontent.com/daveho/carpi/master/notes/log.txt).

## Hardware

The concept for the hardware was to combine the Raspberry Pi with a user interface (display and controls) and package them in a form that would be easy for the parents in the front of the car to use.  I initially considered using a touchscreen, but eventually decided to go with a plain LCD and pushbuttons.  The LCD turned out to be easy: [fbtft](https://github.com/notro/fbtft) supports lots of small/cheap LCDs, and I scored an ILI9340-based LCD on eBay for about $6, which worked perfectly with fbtft.  The pushbuttons turned out to more difficult (high-quality PCB-mount pushbuttons are essentially impossible to find), but eventually I arrived at the idea of using Cherry MX keyswitches, which worked out extremely well.  After a bit of breadboarding (using an attiny4313 for debouncing the keyswitches), I designed a PCB with Eagle CAD, sent it off to [OSHPark](http://www.oshpark.com), threw everything together in a custom laser-cut acrylic enclosure (fabbed by [Pololu](http://www.pololu.com)), and presto, the device:

> <a href="https://raw.githubusercontent.com/daveho/carpi/master/enclosure/pic-big.jpg"><img src="https://raw.githubusercontent.com/daveho/carpi/master/enclosure/pic-sm.jpg" /></a>

You can read the [instructions for building it](https://github.com/daveho/carpi/wiki/Building) if you're interested.

Here is the device in its native environment:

> <a href="/img/carpi-inplace.jpg"><img alt="CarPi, in place" src="/img/carpi-inplace-sm.jpg"></a>

## Software

The software is pretty much just a menu system and support for running [omxplayer](https://github.com/popcornmix/omxplayer) as a subprocess.  In the interest of getting something up and running quickly, I decided to write the software using [ncurses](http://www.gnu.org/software/ncurses/) rather than an actual GUI.

I wrote up some [documentation](https://github.com/daveho/carpi/wiki/Using) describing how to use the software, which includes some screenshots.  Here's a screenshot which should give you a sense of what it looks like:

> <img alt="main menu" src="https://raw.github.com/wiki/daveho/carpi/img/screenshot0.png" />

It's not fancy, but it gets the job done.

I encountered a few interesting technical issues while working on the software, including figuring out how to do interrupt-driven GPIO from C++ (pretty easy, as it turned out) and how to keep the LCD backlight from powering down (quite hard: I had to use the uinput device to make the button presses register as actual keypresses so that the backlight would wake up).

## AV setup

The Raspberry Pi-based system replaces a Philips PD7012 dual screen DVD player.  The good news here is that the slave screen from this system is a bog-standard composite video monitor, so after ordering another identical slave screen from eBay for $25, I had the two screens I needed.  I used a Boss BV-AM5 video signal amplifier to ensure a full-strength signal to both screens.  (Perhaps a passive splitter would have sufficed, but it was only $11.)

One interesting characteristic of the PD7012 slave screens is that they have a single 2.5mm input for both video and stereo audio.  As it turns out, the cable that allows the master unit of the PD7012 to connect to connect to an output device (such as a TV) also works fine for feeding input (both video and audio) to the slave screen.  I ordered two such cables from eBay, which together connect the outputs of the BV-AM5 to the slave screens.  I only used the video signal, since I'm not using the speakers of the slave screens.

For audio, I am using the aux input of the car stereo.  We have a base model 2007 Honda Fit (which lacks an aux input as standard equipment), so we needed to have a third-party aux adapter installed.  ([Wyvon Audio Installations](http://wyvonaudio.com/) did the installation, for which I am grateful, since removing the stereo on a 2007 Fit is not a task for the faint of heart.)

Here is a schematic of the entire setup:

> <a href="/img/avDiagram.jpg"><img alt="AV setup diagram" src="/img/avDiagram-sm.jpg" /></a>

The "piece of wood" serves two functions: anchoring the video signal amplifier, and distributing power to the devices that need 12V.  One of the benefits of working at an engineering school is access to all kinds of tools and connectors, and I was able to use some heat-shrink tubing, a terminal block, and some ring terminals to achieve a relatively neat end result:

> <a href="/img/carpi-pieceofwood.jpg"><img alt="piece of wood" src="/img/carpi-pieceofwood-sm.jpg" /></a>

This sits in a slot between the front seats that seems to have been designed to hold CDs, and since the piece of wood is 5"x5", it fits in this slot perfectly:

> <a href="/img/carpi-pieceofwood-inplace.jpg"><img alt="piece of wood, in place" src="/img/carpi-pieceofwood-inplace-sm.jpg" /></a>

## Does it work?

Heck, yeah!

> <a href="/img/carpi-demo.jpg"><img alt="CarPi demo" src="/img/carpi-demo-sm.jpg" /></a>

Audience reactions have been positive:

> <a href="/img/carpi-thumbsup.jpg"><img alt="CarPi audience reaction" src="/img/carpi-thumbsup-sm.jpg" /></a>

## Things that could work better

As with all projects, there is room for improvement.

One problem I didn't anticipate is that the audio output on the Raspberry Pi isn't that great.  There is definitely some noise, which seems to be worse when the car is traveling at highway speeds.  I'm a software guy, and my knowledge of analog electronics is virtually nil, so I'd be interested to hear an explanation from someone knowledgeable.  However, the sound quality is still a big improvement over the built-in speakers on the PD7012.  At some point I may experiment with using a USB audio adapter to see if it would improve the sound quality.

Another possible improvement would be simplifying the cabling.  The Pi requires three cables: USB (power), audio, and video.  This creates a bit of a rats nest in the front seat, but it's not too bad.

One nice feature of the original DVD player was that it could resume playback automatically after being powered down.  My UI has commands for seeking by +/- 30 sec and +/- 10 minutes, so it's fairly easy to get back to the correct place, but it would be nice for this to happen automatically.  The good news is that this could be hacked into the software fairly easily: just periodically save the current file and playback position to a file.

## Final thoughts

Overall, this was a fun project, and I'm quite satisfied with the result.

Here is one final thought: the Raspberry Pi is awesome.  When I first heard about it, I thought it sounded awesome, and after the experience of using it in this project, it is *much* more awesome than I expected.  It is a full Linux desktop and software development system.  It is a media player that plays H264 video (including HD).  It is a server that is happy to run headless when connected to your network.  It is an embedded board with a price point that allows you to drop it into a project where you might otherwise have used a microcontroller.  *It is all of these things at the same time.*  That is frickin' **magic**.
