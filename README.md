oh-set-gradually
================

This is a program that will gradually change the value of an [OpenHAB][]
item over a user-specified length of time.  You can, for example,
gradually brighten a light from off to full brightness over the course of
half an hour, simulating sunrise.  Similarly, you can gradually shift a
light bulb's color temperature to match the setting sun.

  [OpenHAB]: http://www.openhab.org

This is a work in progress, but the basic functionality described above is
present.  Run `oh-set-gradually --help` to see its options.

Installation
------------

`oh-set-gradually` is a Python program.  You will need at least Python 2.7
and the following modules:

 * [requests](http://python-requests.org)

Use
---

    oh-set-gradually --help
    