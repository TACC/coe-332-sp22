Logging
=======

As your Python3 applications grow in complexity, so too does your need to be
able to track what is going on in the code during run time. *Logging* can be used
to track arbitrary events as the code runs. As we will see, logging will be useful
to keep track of the progression of your code, and it will also be useful to help
us track what parts of our code are causing errors. This is especially useful
when working with multiple applications across distributed systems. After going
through this module, students should be able to:

* Import the logging module into their Python3 scripts
* Set log level to either DEBUG, INFO, WARNING, ERROR, or CRITICAL
* Write appropriate log messages for each log level
* Read output logs and track warnings / errors back to specific spots in their code

Log Levels
----------

We probably all have used (use?) arbitrary print statements in our code as a
means to debug errors. Yes, there is a better way! The Python3 ``logging`` module,
part of the Standard Library, provides functions for reporting different types of
events that occur during run time. Save print statements for printing out the
normal things that the code is supposed to display, and use exceptions to interrupt
the code when it encounters errors. Use logging for everything else, including:

* Printing detailed information about normal things that are supposed to occur,
  but should not be in the standard output
* Printing warnings about particular run time events
* Printing when an error has occurred but was suppressed by, e.g., an error
  handler.

Log levels are used to distinguish between the severity or importance of the
different events. Using different log levels, you can always leave the log
statements which print useful information in your code, but toggle them on and
off depending on which level of severity you want to monitor. The standard log
levels and their purposes are:

* ``DEBUG``: Detailed information, typically of interest only when diagnosing problems
* ``INFO``: Confirmation that things are working as expected.
* ``WARNING``: An indication that something unexpected happened, or indicative of
  some problem in the near future (e.g. ‘disk space low’). The software is still working as expected.
* ``ERROR``: Due to a more serious problem, the software has not been able to perform some function.
* ``CRITICAL``: A serious error, indicating that the program itself may be unable to continue running.

(Source: Python Docs `<https://docs.python.org/3/howto/logging.html>`_)


Initialize Logging
------------------

Let's work through an example. Add the following lines to a script called, e.g.
``log_test.py``:

.. code-block:: python3
    :linenos:

    import logging
    logging.basicConfig()    # configs the logging instance

    logging.debug('This is a DEBUG message')
    logging.info('This is an INFO message')
    logging.warning('This is a WARNING message')
    logging.error('This is an ERROR message')
    logging.critical('This is a CRITICAL message')

Executing that code will output the following messages:

.. code-block:: console

   [isp02]$ python3 log_test.py
   WARNING:root:This is a WARNING message
   ERROR:root:This is an ERROR message
   CRITICAL:root:This is a CRITICAL message

By default, the log level is set to ``WARNING``, and only messages that are
``WARNING`` or higher in level of severity will be printed to screen.

If you set a lower log level, e.g. to ``DEBUG``, all levels of log messages will
be printed:

.. code-block:: python3
   :linenos:
   :emphasize-lines: 2

   import logging
   logging.basicConfig(level=logging.DEBUG)

   logging.debug('This is a DEBUG message')
   logging.info('This is an INFO message')
   logging.warning('This is a WARNING message')
   logging.error('This is an ERROR message')
   logging.critical('This is a CRITICAL message')

.. code-block:: console

   [isp02]$ python3 log_test.py
   DEBUG:root:This is a DEBUG message
   INFO:root:This is an INFO message
   WARNING:root:This is a WARNING message
   ERROR:root:This is an ERROR message
   CRITICAL:root:This is a CRITICAL message

An even better set up would be to pass the desired log level dynamically each time
you execute the code. But for now, it will be sufficient to manually edit the
``basicConfig()`` line if we want to change the log level.



What to Include in a Log
------------------------

As we work toward systems in which we are running multiple applications distributed
over remote systems, it is important to be mindful of what sort of log information
will be useful. In particular, it would be a good idea to be able to gather information
about:

* Timestamp: when the error occurred, also acts as a good reference point when
  referring to a specific log message
* Hostname: what (virtual) machine the error occurred on, as you may have multiple
  instances of an application running on different machines
* Locale: what script and/or what function did the message originate from, helps
  to pinpoint where the message is coming from

To include some of this information in a log message, we need to specify a little
more information in the basic config. We also need to import the ``socket`` module
from the Standard Library so we can grab information about the hostname from the
environment. We also will be calling a few other of the logging formatter's
pre-defined macros.

.. code-block:: python3
    :linenos:
    :emphasize-lines: 2-4

    import logging
    import socket
    format_str=f'[%(asctime)s {socket.gethostname()}] %(filename)s:%(funcName)s:%(lineno)s - %(levelname)s: %(message)s'
    logging.basicConfig(level=logging.DEBUG, format=format_str)

    logging.debug('This is a DEBUG message')
    logging.info('This is an INFO message')
    logging.warning('This is a WARNING message')
    logging.error('This is an ERROR message')
    logging.critical('This is a CRITICAL message')

.. code-block:: console

    [isp02]$ python3 log_test.py
    [2022-02-08 02:22:11,627 isp02.tacc.utexas.edu] log_test.py:<module>:9 - DEBUG: This is a DEBUG message
    [2022-02-08 02:22:11,627 isp02.tacc.utexas.edu] log_test.py:<module>:10 - INFO: This is an INFO message
    [2022-02-08 02:22:11,628 isp02.tacc.utexas.edu] log_test.py:<module>:11 - WARNING: This is a WARNING message
    [2022-02-08 02:22:11,628 isp02.tacc.utexas.edu] log_test.py:<module>:12 - ERROR: This is an ERROR message
    [2022-02-08 02:22:11,628 isp02.tacc.utexas.edu] log_test.py:<module>:13 - CRITICAL: This is a CRITICAL message


Later in the semester, most of the work we will do will be containerized. It is a
little difficult to retrieve log *files* from inside containers, especially if they
crash with an error. An easy work around is to use logging to print to standard
out (as above), and those messages will end up in the container logs from which
they are easily extracted.

EXERCISE
~~~~~~~~

Given the Meteorite Landings analysis script we have been working on, add some
logging throughout the script, focusing on DEBUG, INFO, and ERROR messages.

.. note::

   Note that docstrings below were shortened to save space.


.. code-block:: python3
   :linenos:

   #!/usr/bin/env python3
   import json
   from typing import List

   def compute_average_mass(a_list_of_dicts: List[dict], a_key_string: str) -> float:
       """
       Iterates through a list of dictionaries, pulling out values associated with
       a given key. Returns the average of those values.
       """
       total_mass = 0.
       for item in a_list_of_dicts:
           total_mass += float(item[a_key_string])
       return(total_mass / len(a_list_of_dicts) )

   def check_hemisphere(latitude: float, longitude: float) -> float:
       """
       Given latitude and longitude in decimal notation, returns which hemispheres
       those coordinates land in.
       """
       location = 'Northern' if (latitude > 0) else 'Southern'
       location = f'{location} & Eastern' if (longitude > 0) else f'{location} & Western'
       return(location)

   def count_classes(a_list_of_dicts: List[dict], a_key_string: str) -> dict:
       """
       Iterates through a list of dictionaries, and pulls out the value associated
       with a given key. Counts the number of times each value occurs in the list of
       dictionaries and returns the result.
       """
       classes_observed = {}
       for item in a_list_of_dicts:
           if item[a_key_string] in classes_observed:
               classes_observed[item[a_key_string]] += 1
           else:
               classes_observed[item[a_key_string]] = 1
       return(classes_observed)

   def main():
       with open('Meteorite_Landings.json', 'r') as f:
           ml_data = json.load(f)

       print(compute_average_mass(ml_data['meteorite_landings'], 'mass (g)'))

       for row in ml_data['meteorite_landings']:
           print(check_hemisphere(float(row['reclat']), float(row['reclong'])))

       print(count_classes(ml_data['meteorite_landings'], 'recclass'))

   if __name__ == '__main__':
       main()



Additional Resources
--------------------

* `Python3 Logging How To Guide <https://docs.python.org/3/howto/logging.html>`_
