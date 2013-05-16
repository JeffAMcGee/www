Title: GWJeroo
Category: Projects
Date: 2008-09-15


[GWJeroo](https://github.com/JeffAMcGee/GWJeroo) is a library that lets
students run [Jeroo](http://home.cc.gatech.edu/dorn/jeroo) programs in
[GridWorld](http://www.horstmann.com/gridworld/).
GWJeroo is intended for students in Pre-AP and AP classes who are transitioning
from Jeroo to Java.

I developed this in 2007 when I was a high school computer science teacher. I
no longer teach, so I don't expect to update it.

## Features

* GWJeroo code looks almost identical to Jeroo code.
* GWJeroo can use any feature in the Java language including variables, for
  loops, and out.println().
* GWJeroo is just a standard Java library that is distributed in a jar file. It
  compiles and runs in any normal Java IDE.
* When a GWJeroo program runs, students run their program one line at a time
  using Gridworld's step button.
* GWJeroo can read .jev files from Jeroo.
* In GWJeroo, the class Jeroo extends the class Actor. This means you can call
  any of Actor's methods, such as setColor() or moveTo() (But I don't plan on
  telling my students this too quickly. (Things like moveTo() make some
  problems too easy!))
* The island can have any number of Jeroos. You can also have multiple classes
  that extend Jeroo, so you can have different types of Jeroos.

# Misc.
Here's some assignments and resources other people created for GWJeroo:
http://www.mathwarehouse.com/cs/gwjeroo/ and
http://www.mrmonline.org/cs/gwjeroo/ .

I made a few small changes to the original GridWorld source code.  They are
documented in the file gridworld.diff .
