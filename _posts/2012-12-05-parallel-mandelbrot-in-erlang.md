---
title: Parallel Mandelbrot in Erlang
layout: post
tags: [programming, concurrency, erlang]
---

I'm continuing to work through
[Seven Languages in Seven Weeks](http://pragprog.com/book/btlang/seven-languages-in-seven-weeks)
in my [programming languages course](http://faculty.ycp.edu/~dhovemey/fall2012/cs340/),
and recently wrapped up Erlang.

Erlang is a bit weird: the syntax is based on Prolog's, although it's
not a logic programming language.  It's really a more or less garden-variety
functional language, so the kinds of stuff you normally see in
functional languages (lists, recursion, tail recursion) apply.

Concurrency in Erlang is based on processes, which were the direct inspiration
for Scala's actor framework.  So, after having done both Prolog and Scala
in recent weeks, I was pretty well prepared for Erlang.

Here is my feeble attempt to implement the Mandelbrot set computation
in Erlang.  Erlang requires all user-defined functions to be defined in
modules, and my program has three modules.

First, complex numbers and the basic Mandelbrot set computation
(determining iteration counts for a row of complex numbers):

{% highlight erlang %}
-module(mandelbrot).
-export([complexadd/2, complexmul/2, complexmagnitude/1,
         computeitercount/1, computerow/1]).

complexadd({A, B}, {C, D}) -> {A+C, B+D}.

complexmul({A, B}, {C, D}) -> {A*C - B*D, B*C + A*D}.

complexmagnitude({A, B}) -> math:sqrt(A*A + B*B).

computeitercountwork(C, Z, Count) ->
  MagnitudeOfZ = complexmagnitude(Z),
  if
  (Count >= 1000) or (MagnitudeOfZ > 2.0) -> Count;
  true -> computeitercountwork(C, complexadd(complexmul(Z, Z), C), Count + 1)
  end.

computeitercount(C) -> computeitercountwork(C, {0.0, 0.0}, 0).

computerowwork(RowNum, Y, XStart, XInc, CurCol, Accum) ->
  if
  (CurCol < 0) -> {rowresult, RowNum, Accum};
  true ->
    X = XStart + (CurCol * XInc),
    IterCount = computeitercount({X, Y}),
    computerowwork(RowNum, Y, XStart, XInc, CurCol - 1, [IterCount | Accum])
  end.

computerow({row, RowNum, Y, XStart, XInc, NumCols}) ->
  computerowwork(RowNum, Y, XStart, XInc, NumCols-1, []).
{% endhighlight %}

Erlang uses tuples for all structured data, where other languages would
have you define explicit record types.  In a way, I found this kind
of refreshing.  The definition of operations on complex numbers
is three lines of code!  (5 if you count the two blank lines.)

Note the use of an explicit **if** construct: this is definitely not
Prolog.

The second module is an actor process to compute a single row:

{% highlight erlang %}
-module(rowactor).
-export([loop/0]).

loopwork(ResultCollector) ->
  receive

  % The result collector process has sent us its pid.
  {resultcollectorpid, ResultCollectorPid} ->
    loopwork(ResultCollectorPid);

  % Received a row to compute: compute it and send result back to result collector.
  {row, RowNum, Y, XStart, XInc, NumCols} ->
    ResultCollector ! mandelbrot:computerow({row, RowNum, Y, XStart, XInc, NumCols}),
    loopwork(ResultCollector)

  end.

loop() -> loopwork(unknown).
{% endhighlight %}

All Erlang processes are started by a call to a no-arg function
which enters a loop waiting to receive messages from other
processes.

One slight weirdness is caused by the lack of mutable state.  I needed a way to
keep track of the process id of the actor to which the results should
be returned, so I had the actor receive this as a message, which then
becomes a parameter value to the next call to the tail-recursive
**loopwork** function.

The third module is an actor that receives the boundaries of a region
of the complex plane, along with a number of columns and rows,
and a specified number of processes,
and computes iteration counts for the complex numbers in that region:

{% highlight erlang %}
-module(mandelbrotactor).
-export([loop/0]).

% Send work to a specified row actor process.
sendwork(Pid, XMin, XMax, YMin, YMax, NumCols, NumRows, NumProcs, RowNum) ->
  if
  % We're done if there is no more work to send
  (RowNum >= NumRows) -> true;
  true ->
     % Send one row
     Pid ! {row, RowNum, YMin + (RowNum*((YMax-YMin)/NumRows)),
                 XMin, (XMax-XMin)/NumCols,
                 NumCols},
     % Send the rest of the rows
     sendwork(Pid, XMin, XMax, YMin, YMax, NumCols, NumRows,
              NumProcs, RowNum + NumProcs)
  end.

% Start row actor processes.
startprocs(_, _, _, _, _, _, N, N, Pids) -> Pids;
startprocs(XMin, XMax, YMin, YMax, NumCols, NumRows, NumProcs, CurProc, Pids) ->
  % Spawn a process to compute rows
  Pid = spawn(fun rowactor:loop/0),
  % Inform process where to send results (back to this process) 
  Pid ! {resultcollectorpid, self()},
  % Send the process the rows it should compute
  sendwork(Pid, XMin, XMax, YMin, YMax, NumCols, NumRows, NumProcs, CurProc),
  % Spawn the rest of the processes
  startprocs(XMin, XMax, YMin, YMax, NumCols, NumRows, NumProcs, CurProc + 1,
             [Pid | Pids]).

loop() ->
  receive

    {start, XMin, XMax, YMin, YMax, NumCols, NumRows, NumProcs} ->

      % Start processes
      startprocs(XMin, XMax, YMin, YMax, NumCols, NumRows,
                 NumProcs, 0, []),
      loop();

    {rowresult, RowNum, Data} ->

      % Just print out the received data
      io:format("~w: ~w~n", [RowNum, Data]), loop()

  end.
{% endhighlight %}

One limitation is that this actor does not attempt to sort the computed row
results by row number, so the output does not print the lines in the
correct order. Example run:

	1> c(mandelbrot).
	{ok,mandelbrot}
	2> c(rowactor).
	{ok,rowactor}
	3> c(mandelbrotactor).
	{ok,mandelbrotactor}
	4> Pid = spawn(fun mandelbrotactor:loop/0).
	<0.49.0>
	5> Pid ! {start, -2, 2, -2, 2, 10, 10, 3}.
	{start,-2,2,-2,2,10,10,3}
	2: [1,2,2,3,3,3,2,2,2,2]
	0: [1,1,1,1,1,2,1,1,1,1]
	3: [1,3,3,4,6,18,4,2,2,2]
	1: [1,1,2,2,2,2,2,2,2,1]
	5: [1000,1000,1000,1000,1000,1000,7,3,2,2]
	8: [1,2,2,3,3,3,2,2,2,2]
	6: [1,3,7,7,1000,1000,9,3,2,2]
	9: [1,1,2,2,2,2,2,2,2,1]
	4: [1,3,7,7,1000,1000,9,3,2,2]
	7: [1,3,3,4,6,18,4,2,2,2]

This would have been fairly easy to correct, but as it stands the program
works well enough as a proof of concept.

One improvement of the Erlang implementation over the
[Scala implementation](/2012/11/14/actors-in-scala/) is that the
Erlang implementation creates a fixed number of row actor processes, and
assigns each a series of evenly spaced rows, whereas the Scala
implementation created one actor per row.  Having a fixed number
of actor processes should reduce the overhead
associated with creating and scheduling processes.

Overall, I found Erlang to be an interesting language, although I'm not sure
I would go so far as to call it "fun".  (It goes without saying, of course,
that programming language preferences are highly subjective and that
Erlang contains much to admire.)  One area of Erlang I would like
to explore further is the ability to monitor processes and spawn replacements
when a process fails, which (as I understand it) is the basic Erlang
approach to fault-tolerance.
