---
layout: post
title: "CarPi - Testing the AV setup"
description: ""
category: 
tags: [carpi, electronics, raspberrypi, hacking]
---

# CarPi

As I mentioned in the [last post](/2014/01/29/hacking-on-the-raspberry-pi.html), I'm working on a system to play videos in our car using a Raspberry Pi, which I have given the very creative name [CarPi](https://github.com/daveho/carpi).  The Raspberry Pi and the software are pretty much working at this point, so the main work remaining is the AV setup in the car.

## A plan

I made a sketch of the planned setup (click for full size):

> <a href="/img/avDiagram.jpg"><img alt="AV setup diagram" src="/img/avDiagram-sm.jpg" /></a>

The composite video signal from the Raspberry Pi goes to a video signal amplifier (Boss BV-AM5), which then feeds it to the two LCD screens.  The audio output will go to the aux input on the car stereo.  To avoid having a lot of lose parts floating around, and also to have a central distribution point for the 12V power going to the various devices, I designed (as you can see in the diagram) a very high-tech "piece of wood".

## The test

Today I made a successful test run:

> <a href="/img/avTestRun.jpg"><img alt="AV test run" src="/img/avTestRun-sm.jpg" /></a>

Everything seems to work!  The Raspberry Pi is hiding just behind the keyboard (with the red keycaps that serve as the user input for the UI.)  You can see the aforementioned "piece of wood" in the lower left, working very much as intended.  Although the AV setup is a giant mess on my desk, it will be wired more neatly in the actual car.  Note that the lower of the two LCDs is not the one I'm planning to use in the car: I will use the slave screen from our current Philips DVD player (the same kind as the upper LCD screen in the picture.)

At this point we're very close to being ready to install the system in the car!  I'll post a follow-up when that happens.

<!-- vim:set wrap: ­-->
<!-- vim:set linebreak: -->
<!-- vim:set nolist: -->
