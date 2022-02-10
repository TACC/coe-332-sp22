Code Organization
=================

Following standard Python3 code organization practices will make our code easier
to read by other developers, and by our future selves who are looking back to see
what we did. After going through this module, students should be able to:

* Organize their code into ``main()`` functions
* Import their functions into other scripts without executing the ``main()`` block
* Write functions in a generalizable way so they are reusable
* Use a shebang in their Python3 scripts to make them executable



Main Function
-------------

In many Python programs, you will find the developer has organized their code
into a ``main()`` function. Then, they will only call the ``main()`` function
if the variable ``__name__`` is equal to the string ``'__main__'``. For example:

.. code-block:: python3

    def main():
        # the meat of the main function goes here

    if __name__ == '__main__':
        main()


If this script is executed on the command line directly, then the internal
variable ``__name__`` will be set to the string ``'__main__'``. The conditional
evaluates as ``True`` and the ``main()`` function is called.

If this script is instead **imported into another script**, say, to reuse some of
the functions defined within, then the internal variable ``__name__`` will instead
be set to the name of the script. Thus, the ``main()`` function is not called,
but other functions defined in this script would be available.

Consider the script we wrote in the previous unit for analyzing the Meteorite
Landings JSON file (called ``ml_data_analysis.py``):

.. code-block:: python3
    :linenos:

    import json

    def compute_average_mass(a_list_of_dicts, a_key_string):
        total_mass = 0.
        for item in a_list_of_dicts:
            total_mass += float(item[a_key_string])
        return(total_mass / len(a_list_of_dicts) )

    def check_hemisphere(latitude, longitude):
        location = 'Northern' if (latitude > 0) else 'Southern'
        location = f'{location} & Eastern' if (longitude > 0) else f'{location} & Western'
        return(location)

    with open('Meteorite_Landings.json', 'r') as f:
        ml_data = json.load(f)

    print(compute_average_mass(ml_data['meteorite_landings'] ,'mass (g)' ))

    for row in ml_data['meteorite_landings']:
        print(check_hemisphere(float(row['reclat']), float(row['reclong'])))


To reorganize this code, we would put the file read operation and the two function
calls into a main function:

.. code-block:: python3
    :linenos:
    :emphasize-lines: 14,23-24

    import json

    def compute_average_mass(a_list_of_dicts, a_key_string):
        total_mass = 0.
        for item in a_list_of_dicts:
            total_mass += float(item[a_key_string])
        return(total_mass / len(a_list_of_dicts) )

    def check_hemisphere(latitude, longitude):
        location = 'Northern' if (latitude > 0) else 'Southern'
        location = f'{location} & Eastern' if (longitude > 0) else f'{location} & Western'
        return(location)

    def main():           # notice the below lines are now indented
        with open('Meteorite_Landings.json', 'r') as f:
            ml_data = json.load(f)

        print(compute_average_mass(ml_data['meteorite_landings'] ,'mass (g)' ))

        for row in ml_data['meteorite_landings']:
            print(check_hemisphere(float(row['reclat']), float(row['reclong'])))

    if __name__ == '__main__':
        main()


If this code is imported into another Python3 script, that other script will have
access to the ``compute_average_mass()`` and ``check_hemisphere()`` functions,
but it will not execute the code in the ``main()`` function.

EXERCISE
~~~~~~~~

Write a new script to import the above code, assuming that above code is saved
in a file called ``ml_data_analysis.py``:

.. code-block:: python3
    :linenos:

    import ml_data_analysis     # assumes it is in this directory, or installed in known location

    print(ml_data_analysis.check_hemisphere(35.0, 70.0))
    print(ml_data_analysis.check_hemisphere(-35.0, -70.0))

Try executing this new script with and without protecting the imported code in a
``main()`` function. How do the outputs differ?

.. tip::

   The main function does not have to be called literally ``main()``. But, if
   someone else is reading your code, calling it ``main()`` will certainly help
   orient the reader.




Generalizing Functions
----------------------

A good habit to get into while writing functions is to write them in a
*generalizable* way. This means writing them in such a way that they can be used
for multiple purposes or in multiple applications. The trick is to try to think
ahead about how else you might use the function, think about what form the input
data takes, and try not to hardcode indices or variable names.

``compute_average_mass``
~~~~~~~~~~~~~~~~~~~~~~~~

In our ``compute_average_mass`` function, we knew we needed to send it *something*,
and we knew it needed to return an average mass. The main question was what form
should the input take?

.. code-block:: python3

   def compute_average_mass( xyz ):
       # do some computation
       return(average_mass)

We could have just sent the function the entire dictionary data structure, then
have it parse the data to get masses out. But if we did that, we would also need
to hardcode the name of the main key ``'meteorite_landings'`` as well as the name
of the key referring to the masses ``'mass (g)'``.


.. code-block:: python3

   # BAD
   def compute_average_mass( a_dictionary ):
       total_mass = 0.
       for item in a_dictionary['meteorite_landings']:
           total_mass += float(item['mass (g)'])
       return(total_mass / len(a_dictionary['meteorite_landings']) )

   print(compute_average_mass(ml_data))

Since we will be working with lists of dictionaries most frequently in this class,
it makes more sense to send it a list of dictionaries data structure and the
name of the key to extract.


.. code-block:: python3

   # GOOD
   def compute_average_mass(a_list_of_dicts, a_key_string):
       total_mass = 0.
       for item in a_list_of_dicts:
           total_mass += float(item[a_key_string])
       return(total_mass / len(a_list_of_dicts) )

   print(compute_average_mass(ml_data['meteorite_landings'] ,'mass (g)' ))




``check_hemisphere``
~~~~~~~~~~~~~~~~~~~~

The ``check_hemisphere`` function is very similar - we send it *something* and
it returns (or prints) a string.

.. code-block:: python3

   def check_hemisphere( xyz )
       # run through some conditionals
       return(location)

Here we could have also sent a list of dictionaries along with the names of two
keys representing the latitude and longitude. That would have been ok, and would
have worked for most of the data structures we use in this class.

.. code-block:: python3

   # NOT TERRIBLE
   def check_hemisphere(a_list_of_dicts, lat_key, long_key):
       for item in a_list_of_dicts:
           location = 'Northern' if (float(item[lat_key]) > 0) else 'Southern'
           location = f'{location} & Eastern' if (float(item[long_key]) > 0) else f'{location} & Western'
           print(location)
       return

   check_hemisphere(ml_data['meteorite_landings'], 'reclat', 'reclong')


However, to make it even more generalizable, we could abstract one layer further
and just send it two floats: latitude and longitude. That would make the function
useful for our list of dictionaries data structure, and for one-off checks given
just a pair of floats:

.. code-block:: python3

   # BETTER
   def check_hemisphere(latitude, longitude):
       location = 'Northern' if (latitude > 0) else 'Southern'
       location = f'{location} & Eastern' if (longitude > 0) else f'{location} & Western'
       return(location)

   for row in ml_data['meteorite_landings']:
       print(check_hemisphere(float(row['reclat']), float(row['reclong'])))


EXERCISE
~~~~~~~~

Write a new function to count how many of each 'class' of meteorite there is
in the list. The output should look something like:

.. code-block:: console

   type, number
   H, 1
   H4, 2
   L6, 6
   ...etc

Consider carefully what inputs you are sending to the function. How can you write
it in a generalizable way?


Shebang
-------

A "shebang" is a line at the top of your script that defines what interpreter should
be used to run the script when treated as a standalone executable. You will often
see these used in Python, Perl, Bash, C shell, and a number of other scripting
languages. In our case, we want to use the following shebang, which should appear
on the first line of our Python3 scripts:

.. code-block:: python3

   #!/usr/bin/env python3

The ``env`` command simply figures out which version of ``python3`` appears first
in your path, and uses that to execute the script. We usually use that form instead
of, e.g., ``#!/usr/bin/python3.6`` because the location of the Python3 executable
may differ from machine to machine, whereas the location of ``env`` will not.

Next, you also need to make the script executable using the Linux command
``chmod``:

.. code-block:: console

   [isp02]$ chmod u+x ml_data_analysis.py

That enables you to call the Python3 code within as a standalone executable without
invoking the interpreter on the command line:

.. code-block:: console

   [isp02]$ ./ml_data_analysis.py

This is helpful to lock in a Python version (e.g. Python3) for a script that may
be executed on multiple different machines or in various environments.


Other Tips
----------

As our Python3 scripts become longer and more complex, we should put more thought
into how the different contents of the script are ordered. As a rule of thumb, try
to organize the different sections of your Python3 code into this order:

.. code-block:: python3

   # Shebang

   # Imports

   # Global variables / constants

   # Class definitions

   # Function definitions

   # Main function definition

   # Call to main function

Other general tips for writing code that is easy to read can be found in the
`PEP 8 Style Guide <https://www.python.org/dev/peps/pep-0008/>`_, including:

* Use four spaces per indentation level (no tabs)
* Limit lines to 80 characters, wrap and indent where needed
* Avoid extraneous whitespace unless it improves readability
* Be consistent with naming variables and functions

  * Classes are usually ``CapitalWords``
  * Constants are usually ``ALL_CAPS``
  * Functions and variables are usually ``lowercase_with_underscores``
  * Consistency is the key

* Use functions to improve organization and reduce redundancy
* Document and comment your code

.. note::

   Beyond individual Python3 scripts, there is a lot more to learn about organizing
   *projects* which may consist of many files. We will get into this later in the
   semester.



Additional Resources
--------------------

* `PEP 8 Style Guide <https://www.python.org/dev/peps/pep-0008/>`_
