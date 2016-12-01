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
 * [toml](https://github.com/uiri/toml)

Use
---

    oh-set-gradually --help

OpenHAB Communication
---------------------

At the moment, only OpenHAB's REST interface is supported.  By default,
the program will connect to localhost on port 80.  You can change that
either via command line options or a configuration file.

### Config File

Create a [TOML](https://github.com/toml-lang/toml) configuration file,
with any of `host`, `port`, and `ssl` values in a `rest` section.  e.g.:

    [rest]
    host = "openhab.example.com"
    port = 8443
    ssl = true

Pass the config file to `oh-set-gradually` via command line options:

    oh-set-gradually --config openhab.toml ...

### Command Line

The `--host`, `--port`, and `--ssl` parameters can be used.  e.g.:

    oh-set-gradually --host openhab.example.com --port 8443 --ssl ...

Command line parameters override configuration file parameters.  If the
config file has `ssl = true`, you can override it with `--no-ssl`.
