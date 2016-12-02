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

Scripts
-------

Scripts allow you to coordinate changes for multiple items over a period
of time.  For example, you might want to dim several lights at different
rates while shifting their color temperatures, too.

A script is a [TOML](https://github.com/toml-lang/toml) file containing a
table array named `changes`.  Each array entry may have the following
keys:

* `item` - **Required**.  The name of the OpenHab item to change.
* `start_value` - Optional.  The value at which to start the change.  If
  omitted, the script will retrieve the current value from OpenHAB at the
  time this change begins to take effect (given by `start_time`, below).
* `start_time` - Optional.  When to start applying this change, given as a
  number of seconds after the time the script was started.  If omitted,
  the value is assumed to be zero, meaning the change will start
  immediately.
* `end_value` - **Required**.  The target value for the change.
* `duration` - Optional.  How long, in seconds, after the `start_time`
  until the item should be set to the `end_value`.  If omitted, the value
  is assumed to be zero, which means that the `end_value` will be applied
  immediately, with no intermediate steps.
* `step` - Optional.  Target amount to increment the item's value before
  setting it again.  Note that this is advisory; if you have several items
  changing simultaneously, the script sets all of them according to the
  schedule of the fastest-changing item.  The default is 1 (or -1, if the
  `end_value` is less than the `start_value`).

Note that you can put your OpenHAB connection parameters into the same
file as your script.  In that case, you can omit the `-c` parameter on the
command line.

### Example

Here's a script that might work as a sunrise simulator.  It gradually
brightens a light while shifting its color temperature, then turns the
light off two hours after starting.

    [rest]
    host = "openhab.example.com"
    port = 8443
    ssl = true
    
    [[changes]]
    # Gradually brighten lights for 30 minutes.
    item = "sunrise_lights"
    start_value = 0
    end_value = 100
    duration = 1800
    
    [[changes]]
    # Set the color temperature to 2500K as soon as the script starts.
    item = "sunrise_lights_color_temperature"
    end_value = 2500
    
    [[changes]]
    # After ten minutes, spend another ten minutes shifting the color
    # temperature to a daylight approximation.  Aim to do it in ten-degree
    # increments so we're not hammering the lights multiple times per second.
    item = "sunrise_lights_color_temperature"
    start_time = 600
    duration = 600
    end_value = 5000
    step = 10
    
    [[changes]]
    # Turn the lights off after two hours.
    item = "sunrise_lights"
    start_time = 7200
    end_value = 0
