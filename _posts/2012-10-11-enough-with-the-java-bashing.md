---
layout: post
title: Enough with the Java bashing, already
tags: [java, programming]
---

Everyone has their favorite programming languages.  Lately, Java seems
to be the object of a certain amount of scorn.  I realize that Java is
not the world's most elegant language, and that other languages are
superior in various ways.  However, much of the Java criticism I see
fails to acknowledge Java's merits.  In order to have an intellectually honest
discussion, I think we need to acknowledge *both* the good and bad.
That's what I will try to do in this post.

So, for the record, here is how I see Java:

1. Java has flaws.  It is not the best language for any problem domain.
2. Java is a *pretty good* language for a large number of problem domains.
   For many important problem domains, it is very much a credible choice.
3. One of the things that Java got right, pretty much from day 1, was
   that it should be easy to build a program out of ready-made components.
   Because of this, Java has a vast array of useful libraries that are easy
   to reuse.

## The bad bits and the good bits

Let's delve a bit deeper.

**Java is flawed**.  It's too verbose.
Its handling of generics is awkward.  The distinction between primitive
types and object types is artificial and confusing.  Anonymous classes
are a poor substitute for closures.  JNI is ugly.  In general, it's
just not that elegant.  These are valid criticisms.  I've been doing
some Ruby programming lately, and I must say that it was a bit painful
to come back to Java.  (Java 8 lambdas and the new collections methods
to support them should bring some of the "let's filter this collection
with that closure" style of programming to Java.)

One redeeming characteristic of Java (as a language) is that it's simple.
I teach it to students every year, and am always impressed at the
extent to which they can accomplish interesting things (e.g., GUIs,
multi-threaded programs) with relative ease.  I'm aware that there are
other languages (say, Python) with this property, but Java puts in a
pretty good showing.

Another way to look at this is that Java is a language for the 99%.
There are people who write useful code in Java who probably wouldn't be
very effective as OCaml programmers.  (I'm almost certainly one of them.)
I think this is a good thing: our industry needs software developers,
and Java has lowered the bar for entry into the profession.

**Java works pretty well in many problem domains**.  Show me another language that would be a good choice
for all of the following kinds of programs:

* Rich client-side web applications
* Massively-scalable web services
* High performance shared-memory parallel programs
* Robotics and other soft-real-time systems

Java works pretty well for all of these.
[GWT](https://developers.google.com/web-toolkit/) is rather nice for
client-side web applications.  While J2EE can (and should) be criticized
as excessively complex, there is a subset (servlets and JSPs) that works
well for your standard MVC2 types of server-side web applications and/or
web services, and [Jetty](http://www.eclipse.org/jetty/) is an amazingly nice platform to deploy them on.
Anyone who is familiar with [Doug Lea](http://g.oswego.edu/)'s work on java.util.concurrent should
acknowledge Java's strengths for parallel and concurrent computation.
(Java's fork/join framework kicks ass.)  I'm not a robot guy, but [my
colleague who is one](http://www.drpatrickmartin.com/) loves Java and informs me that it is respected in
the robotics community.

So...if I work in multiple problem domains, should I learn multiple
languages, or should I stick with the general-purpose language that works
reasonably well in all of them?  Given the amount of time it takes to
become proficient (weeks/months) or expert (years) in any language, I
think a case can be made that there is value in general-purpose languages
that can span problem domains.

Please note that I *like* learning new things.  However, like everyone,
I have a limited amount of time, and if I can leverage my current skills
in a new context, isn't that a good thing?  The [project I'm currently
working on](http://cloudcoder.org) probably would not have gotten off
the ground if it hadn't been for GWT and its ability to turn any
Java programmer into a web application developer.

**Java gets reuse right**.  Jar files are a brilliant mechanism for the packaging
and reuse of code.  To use someone else's code, I just need to drop their
jar file in my project, and I'm done.  No package manager (requiring
admin privileges to run!), no repository, no source code.  Just the
goddamn jar file.  This is *huge*.

(Transitive dependencies can be an issue, and Java has [Maven](http://maven.apache.org/)
for that, although I've never found tracking down dependencies manually to be
a serious burden.)

Not only are reusable components readily available, but thanks to
Javadoc, they tend to be reasonably easy to learn and use.  Java culture places a high priority
on API documentation, to the extent that it is somewhat unusual to find
a Java library that does not have good API documentation.  I point this
out because not all languages seem to place as high a value on good API
documentation.  (My limited experiences with Javascript seem to indicate
that API documentation is not emphasized as much in that community.)

Please note that I am by no means saying that every Java library out
there is good.  Lots of Java libraries are crap.  However, if you're
looking for a library to do X in Java, your odds of finding a reasonably
good one are pretty high.

## A meta-issue

Ultimately, I think much of the criticism of Java is not so much
criticism of Java the language, but criticism of Java the culture.
There are surely some valid things to criticize about Java culture.
The tendency to over-abstract (FactorySingletonAdapterFactoryPlugin and
such) is real.  There is an "enterprisey" flavor to many Java technologies
that is off-putting.

However, tasteful Java libraries and programs can be and have been
written.  There are elegant ways to use inelegant technologies (like
J2EE).  With good judgment, you can build awesome
stuff with Java.

## In conclusion

Java has flaws.  Java has strengths.  Let's acknowledge them and move forward.

One final thought - I'm very excited about [Scala](http://www.scala-lang.org/), and I think it may be
the right language to address Java's flaws while retaining its strengths.
I'll know more later in the semester when we cover it in depth in the programming
languages course I'm teaching.
