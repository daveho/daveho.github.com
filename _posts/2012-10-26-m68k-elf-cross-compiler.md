---
title: Building an m68k-elf cross compiler under Ubuntu
layout: post
tags: [crosscompiler, m68k, ubuntu, mc68008]
---

I'm getting closer to being able to run code on the
[MC68008 system](/2012/10/13/an-mc68008-computer-project)
I'm building.  I've designed glue logic (using two 22V10 GALs
and an 74hct138 3-to-8 decoder) for address decoding, DTACK generation,
and interrupt logic.  (Actually, the interrupt logic GAL is mostly
unused, since I don't yet have an interrupt-generating device
in the circuit.)  I've also added a 27C512 EPROM and a 74ls374
output port to the circuit.  (Schematics and photos soon.)  My goal
is to burn an EPROM that will cause an LED connected to the
 output port to blink.  That, of course, requires that I have
some way of writing code to burn onto the EPROM!

I thought about hand-assembling a short program, but in the end I decided
to build a cross-development toolchain using GNU binutils and gcc.

Here are the magic incantations I used (save this as a file
called **build.sh**):

    set -e
    export M68KPREFIX=/home/dhovemey/linux/m68k-elf
    export PATH=$M68KPREFIX/bin:$PATH
    mkdir crossgcc
    wget http://ftp.gnu.org/gnu/binutils/binutils-2.23.tar.gz
    gunzip -c binutils-2.23.tar.gz | tar xvf -
    wget http://ftp.gnu.org/gnu/gcc/gcc-4.4.2/gcc-core-4.4.2.tar.bz2
    bunzip2 -c gcc-core-4.4.2.tar.bz2 | tar xvf -
    mkdir build
    cd build
    mkdir binutils
    mkdir gcc
    cd binutils
    ../../binutils-2.23/configure --target=m68k-unknown-elf --prefix=$M68KPREFIX
    make -j 3
    make install
    cd ../gcc
    ../../gcc-4.4.2/configure --target=m68k-unknown-elf --disable-libssp \
      --prefix=$M68KPREFIX
    make -j 3
    make install

You can change the value of **M68KPREFIX** to whatever directory you'd
like to install the toolchain in.  You can also change the **-j 3** flag
on the **make** commands.  I have a CPU with 3 cores, which is why I used
that particular value.

Execute using the command

    sh build.sh

in some scratch directory.

You may find that various development packages (such as **libgmp-dev**)
must be installed before the build will succeed.

Note that all of the tools will be prefixed with **m68k-unknown-elf-**.
So, for example, gcc is **m68k-unknown-elf-gcc**.

I used the latest version of binutils, but an older version of
gcc, based on [a gcc bug report](http://gcc.gnu.org/bugzilla/show_bug.cgi?id=42557)
that happens to mention a specific configure command for gcc 4.4.2 
that builds an m68k/elf embedded target.  Binutils is generally
very straightforward to compile for an embedded target, but gcc
is usually more complicated, so it was useful to start
with a known-good configure command.

I have only tested binutils (as, ld, objcopy, and objdump) so far;
I'm hoping that the C compiler will work as well, although it will be
a while before I'm at the point where I will be trying to execute C code.
