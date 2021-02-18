---
layout: post
title: "The magnificence of Thinkpads for CS"
---

# Thinkpads: An ideal computing environment for Computer Science

Many academic Computer Science departments have moved to Linux as their main computing
environment.  [My department](https://www.cs.jhu.edu/) is one of them.  Major tech
companies also use Linux as a foundational component of their development environment.

Let's say that you're a student, and you know you will need to use Linux to work
on your CS assignments.  How should you set up a development environment?

For students who are running Windows 10, [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
is a nice option.  Once enabled, several flavors of Linux are available (for free) from the
Microsoft Store, including Ubuntu 18.04 and Ubuntu 20.04.  It is my understanding that
it is possible to [Run an X server](https://techcommunity.microsoft.com/t5/windows-dev-appconsult/running-wsl-gui-apps-on-windows-10/ba-p/1493242)
to allow Linux GUI programs to run.  Personally, I'm comfortable with a fully terminal-based
workflow.

For students using Mac laptops, the options are somewhat less than ideal.
It's possible to [run Docker](https://docs.docker.com/docker-for-mac/install/),
so a development environment could be set up inside a Docker container.
Virtual machine hosts such as [VirtualBox](https://www.virtualbox.org/) are also a possibility, although
performance and memory use can be an issue.  The new ARM-based Macs
(using "Apple Silicon") further complicate the situation, since quite a few
systems-oriented courses require students to code for x86-64 CPUs.

My personal belief is that there is no better environment for Computer Science
than a good laptop running Linux.  The [Thinkpad](https://www.thinkwiki.org/wiki/ThinkWiki)
series has long been the gold standard for Linux compatibility.
Best of all, because they are purchased in huge quantities for corporate use,
then dumped onto the used market as part of the replacement cycle,
they are easy to find and affordable.

I recently purchased a Thinkpad X250 for US $200 on eBay (click for full size):

> <a href="{{site.baseurl}}/img/thinkpad-x250.jpg"><img alt="Thinkpad X250" src="{{site.baseurl}}/img/thinkpad-x250-sm.jpg"></a>

It's not a high-end machine by modern standards (8 GB RAM, 180 GB SSD, 1366x768 IPS display),
but for software development, email and group chat, and writing, it's amazingly good.
I run [Linux Mint](https://www.linuxmint.com/), specifically, Linux Mint 20 (MATE Edition).
I'm getting 6-7 hours per charge.  It's quite light, well-constructed, and the keyboard
is pleasant to use.  From OS install to being fully configured and doing
productive work was maybe 45 minutes (mostly to install the software I need using the
package manager.)

I guess my point is this: you can have an amazingly versatile Linux development
environment for $200.  It will provide a superior experience to WSL, Docker,
or ssh into a remote machine.  I think a lot of people would find that modern
versions of Linux are fully capable of satisfying all of their general computing
needs.  It's an option well worth considering.
