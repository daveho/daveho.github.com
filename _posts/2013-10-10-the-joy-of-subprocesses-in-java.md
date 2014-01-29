---
layout: post
title: "The joy of subprocesses in Java"
description: ""
category: 
tags: [java, cloudcoder]
---

To support C/C++ exercises in [CloudCoder](http://cloudcoder.org),
we compile each submission using g++, run the resulting executable
for each test case, and check the result of each execution.

Running an OS-level process in Java is both simple and complicated.
The **java.lang.Process** represents a subprocess, and provides reasonable
flexibility.  You can specify the executable and its arguments, define
environment variables, and communicate with the process by writing to
its standard input and reading from its standard output and error.
You can also wait for it to exit, and find out its exit code after
it has terminated.  That's the simple part.

The complicated part is that we have some additional requirements:

1. CloudCoder needs to set resource limits: for example, to limit
   the amout of CPU time (students learning about loops tend to
   submit code with an infinite loop fairly often).
2. CloudCoder needs to know precisely how and why the process terminated.
   For example, was it killed by a signal? Which signal? Were we
   even able to start the process in the first place?
3. CloudCoder needs to sandbox the program so that it doesn't
   read files, open network connections, etc.  We use a library I wrote
   called [EasySandbox](https://github.com/daveho/EasySandbox)
   to do this, which involves setting the **LD_PRELOAD** environment
   variable to force the loading of a shared library, which hijacks
   the startup sequence of the program.

To simplify all of these additional requirements, I wrote a shell script
that sets up resource limits and/or sandboxing, executes the program,
and then writes a file indicating how and why the process exited.
CloudCoder invokes this shell script, which runs the test program on
its behalf.  All very well and good.

Last week, our largest user (a course with around 700 students!)
reported problems where CloudCoder would report compilation and execution
failures.  After some research, including diagnose a **NullPointerException**
for which the JVM
[helpfully omitted the stack trace](http://stackoverflow.com/questions/2411487/nullpointerexception-in-java-with-no-stacktrace), I discovered that

1. Subprocesses were being killed by CloudCoder, but
2. The actual processes weren't getting killed!

CloudCoder will explicitly kill subprocesses that it thinks are taking
too long.  This could happen, for instance, if the process were
sleeping, blocked on input, or otherwise idle in a way that doesn't
consume any CPU cycles.  When CloudCoder detects that a subprocess is
stuck in this way, it kills it using the **Process.destroy()** method.
From looking at the logs, it was clear that this was happening, but
why were the processes not actually being killed?

(You may find that the cause is obvious at this point, in which case I tip my
virtual hat to you.)

It turns out...wait for it...that we killing *the wrapper shell script*,
not the "real" subprocess started by the shell script.  What's worse,
because the shell script and its child had no controlling tty, the
OS didn't kill the real subprocess when its parent died.  Thus, lots and
lots of orphaned processes.

Presumably the failure mode was filling up the process table, or exceeding
a per-process limit on the number of child processes, but in any case the
result was not being able to start any new child processes.

The solution was fairly straightforward: I had the wrapper script
handle the **SIGTERM** signal and relay it to the real subprocess.
Here's the result:

[runProcess2.sh](https://github.com/daveho/CloudCoder/blob/master/CloudCoderBuilder2/src/org/cloudcoder/builder2/process/res/runProcess2.sh)

Several days later, we're still up and running.

Aside from the obvious "duh" moment of realizing that the wrapper
was getting killed instead of the actual process, I think this
episode drove home the point that there's something fundamentally different
about a system that runs correctly a small number of times vs. a system
that runs correctly an indefinite number of times, and that 24/7
server applications fall into the second category.  I did a ton of load
testing of CloudCoder this summer, and I proved that it could handle
fairly high short-term loads, but my testing never addressed the
question of what would happen when the system was pounded with
a large number of requests over time, especially when some of those
requests involved subprocesses being explicitly killed.
