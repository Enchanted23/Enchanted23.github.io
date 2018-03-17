Logging is a means of tracking events that happen when some software runs. ***The software’s developer adds logging calls to their code to indicate that certain events have occurred.*** An event is described by a descriptive message which can optionally contain variable data (i.e. data that is potentially different for each occurrence of the event). Events also have an importance which the developer ascribes to the event; the importance can also be called the *level* or *severity*.

[Logging HOWTO](https://docs.python.org/2/howto/logging.html)

## Basic Logging Tutorial

### When to use logging

Logging provides a set of convenience functions for simple logging usage. These are [`debug()`](https://docs.python.org/2/library/logging.html#logging.debug), [`info()`](https://docs.python.org/2/library/logging.html#logging.info),[`warning()`](https://docs.python.org/2/library/logging.html#logging.warning), [`error()`](https://docs.python.org/2/library/logging.html#logging.error) and [`critical()`](https://docs.python.org/2/library/logging.html#logging.critical). To determine when to use logging, see the table below, which states, for each of a set of common tasks, the best tool to use for it.

| Task you want to perform                                     | The best tool for the task                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Display console output for ordinary usage of a command line script or program | [`print()`](https://docs.python.org/2/library/functions.html#print) |
| Report events that occur during normal operation of a program (e.g. for status monitoring or fault investigation) | [`logging.info()`](https://docs.python.org/2/library/logging.html#logging.info) (or [`logging.debug()`](https://docs.python.org/2/library/logging.html#logging.debug) for very detailed output for diagnostic purposes) |
| Issue a warning regarding a particular runtime event         | [`warnings.warn()`](https://docs.python.org/2/library/warnings.html#warnings.warn) in library code if the issue is avoidable and the client application should be modified to eliminate the warning[`logging.warning()`](https://docs.python.org/2/library/logging.html#logging.warning) if there is nothing the client application can do about the situation, but the event should still be noted |
| Report an error regarding a particular runtime event         | Raise an exception                                           |
| Report suppression of an error without raising an exception (e.g. error handler in a long-running server process) | [`logging.error()`](https://docs.python.org/2/library/logging.html#logging.error), [`logging.exception()`](https://docs.python.org/2/library/logging.html#logging.exception) or [`logging.critical()`](https://docs.python.org/2/library/logging.html#logging.critical) as appropriate for the specific error and application domain |

The logging functions are named after the level or severity of the events they are used to track. The standard levels and their applicability are described below (in increasing order of severity):

| Level      | When it’s used                                               |
| ---------- | ------------------------------------------------------------ |
| `DEBUG`    | Detailed information, typically of interest only when diagnosing problems. |
| `INFO`     | Confirmation that things are working as expected.            |
| `WARNING`  | An indication that something unexpected happened, or indicative of some problem in the near future (e.g. ‘disk space low’). The software is still working as expected. |
| `ERROR`    | Due to a more serious problem, the software has not been able to perform some function. |
| `CRITICAL` | A serious error, indicating that the program itself may be unable to continue running. |

The default level is `WARNING`, which means that only events of this level and above will be tracked, unless the logging package is configured to do otherwise.

Events that are tracked can be handled in different ways. The simplest way of handling tracked events is to print them to the console. Another common way is to write them to a disk file.

### A simple example

A very simple example is:

```python
import logging
logging.warning('Watch out!')  # will print a message to the console
logging.info('I told you so')  # will not print anything
```

If you type these lines into a script and run it, you’ll see:

```shell
WARNING:root:Watch out!
```

printed out on the console. 

### Logging to a file

A very common situation is that of recording logging events in a file, so let’s look at that next. Be sure to try the following in a newly-started Python interpreter, and don’t just continue from the session described above:

```python
import logging
logging.basicConfig(filename='example.log',level=logging.DEBUG)
logging.debug('This message should go to the log file')
logging.info('So should this')
logging.warning('And this, too')
```

And now if we open the file and look at what we have, we should find the log messages:

```shell
DEBUG:root:This message should go to the log file
INFO:root:So should this
WARNING:root:And this, too
```

This example also shows how you can set the logging level which acts as the threshold for tracking. In this case, because we set the threshold to `DEBUG`, all of the messages were printed.

If you want to set the logging level from a command-line option such as:

```shell
--log=INFO
```

and you have the value of the parameter passed for `--log` in some variable *loglevel*, you can use:

```python
getattr(logging, loglevel.upper())
```

to get the value which you’ll pass to [`basicConfig()`](https://docs.python.org/2/library/logging.html#logging.basicConfig) via the *level* argument. You may want to error check any user input value, perhaps as in the following example:

```Python
# assuming loglevel is bound to the string value obtained from the
# command line argument. Convert to upper case to allow the user to
# specify --log=DEBUG or --log=debug
numeric_level = getattr(logging, loglevel.upper(), None)
if not isinstance(numeric_level, int):
    raise ValueError('Invalid log level: %s' % loglevel)
logging.basicConfig(level=numeric_level, ...)
```

The call to [`basicConfig()`](https://docs.python.org/2/library/logging.html#logging.basicConfig) should come *before* any calls to [`debug()`](https://docs.python.org/2/library/logging.html#logging.debug), [`info()`](https://docs.python.org/2/library/logging.html#logging.info) etc. As it’s intended as a one-off simple configuration facility, only the first call will actually do anything: subsequent calls are effectively no-ops.

If you run the above script several times, the messages from successive runs are appended to the file *example.log*. If you want each run to start afresh, not remembering the messages from earlier runs, you can specify the *filemode* argument, by changing the call in the above example to:

```python
logging.basicConfig(filename='example.log', filemode='w', level=logging.DEBUG)
```

The output will be the same as before, but the log file is no longer appended to, so the messages from earlier runs are lost.

### Logging from multiple modules

If your program consists of multiple modules, here’s an example of how you could organize logging in it:

```python
# myapp.py
import logging
import mylib

def main():
    logging.basicConfig(filename='myapp.log', level=logging.INFO)
    logging.info('Started')
    mylib.do_something()
    logging.info('Finished')

if __name__ == '__main__':
    main()
```

```python
# mylib.py
import logging

def do_something():
    logging.info('Doing something')
```

If you run *myapp.py*, you should see this in *myapp.log*:

```shell
INFO:root:Started
INFO:root:Doing something
INFO:root:Finished
```

which is hopefully what you were expecting to see. You can generalize this to multiple modules, using the pattern in *mylib.py*. Note that for this simple usage pattern, you won’t know, by looking in the log file, *where* in your application your messages came from, apart from looking at the event description. If you want to track the location of your messages, you’ll need to refer to the documentation beyond the tutorial level – see[Advanced Logging Tutorial](https://docs.python.org/2/howto/logging.html#logging-advanced-tutorial).

### Logging variable data

To log variable data, use a format string for the event description message and append the variable data as arguments. For example:

```python
import logging
logging.warning('%s before you %s', 'Look', 'leap!')
```

will display:

```shell
WARNING:root:Look before you leap!
```

As you can see, merging of variable data into the event description message uses the old, %-style of string formatting. This is for backwards compatibility: the logging package pre-dates newer formatting options such as [`str.format()`](https://docs.python.org/2/library/stdtypes.html#str.format) and [`string.Template`](https://docs.python.org/2/library/string.html#string.Template). These newer formatting options *are* supported, but exploring them is outside the scope of this tutorial.

### Changing the format of displayed messages

To change the format which is used to display messages, you need to specify the format you want to use:

```python
import logging
logging.basicConfig(format='%(levelname)s:%(message)s', level=logging.DEBUG)
logging.debug('This message should appear on the console')
logging.info('So should this')
logging.warning('And this, too')
```

which would print:

```shell
DEBUG:This message should appear on the console
INFO:So should this
WARNING:And this, too
```

Notice that the ‘root’ which appeared in earlier examples has disappeared. For a full set of things that can appear in format strings, you can refer to the documentation for [LogRecord attributes](https://docs.python.org/2/library/logging.html#logrecord-attributes), but for simple usage, you just need the *levelname* (severity), *message* (event description, including variable data) and perhaps to display when the event occurred. This is described in the next section.

### Displaying the date/time in messages

To display the date and time of an event, you would place ‘%(asctime)s’ in your format string:

```python
import logging
logging.basicConfig(format='%(asctime)s %(message)s')
logging.warning('is when this event was logged.')
```

which should print something like this:

```shell
2010-12-12 11:41:42,612 is when this event was logged.
```

The default format for date/time display (shown above) is ISO8601. If you need more control over the formatting of the date/time, provide a *datefmt* argument to `basicConfig`, as in this example:

```python
import logging
logging.basicConfig(format='%(asctime)s %(message)s', datefmt='%m/%d/%Y %I:%M:%S %p')
logging.warning('is when this event was logged.')
```

which would display something like this:

```shell
12/12/2010 11:46:36 AM is when this event was logged.
```

The format of the *datefmt* argument is the same as supported by [`time.strftime()`](https://docs.python.org/2/library/time.html#time.strftime).