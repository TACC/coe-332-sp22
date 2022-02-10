Documentation
=============

As we have probably all heard before, good documentation is almost as important
(if not equally as important) as good code itself. You may have written some
elegant and powerful code to solve all your problems today, but weeks or months
today does that code become functionally useless if you forget what it does or
how to call it? Python3 users have a special built-in tool at their disposal
called *docstrings* that make documenting functions easy. After going through
this module, students should be able to:

* Write well-crafted docstrings for all functions
* Add type hints to function definitions
* Write effective READMEs for a project

Docstrings
----------

Docstrings are special strings that appear immediately following function
definitions in our code. They should be surrounded by three double-quotation
marks on each side, and they may span multiple lines. For example:

.. code-block:: python3

   def a_function():
       """
       This is a docstring.
       """
       # code goes here
       return


The above is a valid docstring, but it is not a very helpful docstring. When you
write docstrings, at a minimum try to include the following sections:

1. A short description of the purpose of the function
2. A list of arguments, including type
3. A list of returned values, including type

A better template for a docstring (based on the
`Google Style Guide <https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings>`_)
might look like:

.. code-block:: python3

   def a_function(arg1, arg2):
       """
       This function does XYZ.

       Args:
           arg1 (type): Define what is expected for arg1.
           arg2 (type): Define what is expected for arg2.

       Returns:
           result (type): Define what is expected for result.
       """
       # code goes here
       return(result)

The description should be succinct, yet complete. Arguments should be listed by
name and the expected type (e.g., bool, float, str, etc) should be stated. And
the return result(s) should be listed along with the expected type(s).

Let's look at one more example using a real function:

.. code-block:: python3

   def add_and_square(num1, num2):
       """
       Given two numbers, this function will first add them together, then square the sum
       and return the result.

       Args:
           num1 (float): The first number.
           num2 (float): The second number.

       Returns:
           result (float): The square of the sum of input arguments.
       """
       result = (num1+num2)**2
       return(result)


.. note::

   Notice above we are using more-or-less complete sentences with proper grammar.


Next, let's add docstrings to our ``ml_data_analysis.py`` code we have been
working on:

.. code-block:: python3
    :linenos:
    :emphasize-lines: 5-17,24-34,40-42

    #!/usr/bin/env python3
    import json

    def compute_average_mass(a_list_of_dicts, a_key_string):
        """
        Iterates through a list of dictionaries, pulling out values associated with
        a given key. Returns the average of those values.

        Args:
            a_list_of_dicts (list): A list of dictionaries, each dict should have the
                                    same set of keys.
            a_key_string (string): A key that appears in each dictionary associated
                                   with the desired value (will enforce float type).

        Returns:
            result (float): Average value.
        """
        total_mass = 0.
        for item in a_list_of_dicts:
            total_mass += float(item[a_key_string])
        return(total_mass / len(a_list_of_dicts) )

    def check_hemisphere(latitude, longitude):
        """
        Given latitude and longitude in decimal notation, returns which hemispheres
        those coordinates land in.

        Args:
            latitude (float): Latitude in decimal notation.
            longitude (float): Longitude in decimal notation.

        Returns:
            location (string): Short string listing two hemispheres.
        """
        location = 'Northern' if (latitude > 0) else 'Southern'
        location = f'{location} & Eastern' if (longitude > 0) else f'{location} & Western'
        return(location)

    def count_classes(a_list_of_dicts, a_key_string):
        """
        ???
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


In general, your ``main()`` function usually does not need a docstring. It is
good habit to write the ``main()`` function simply and clearly enough that it is
self explanatory, with perhaps a few comments to help. If you do add a docstring
to  the ``main()`` function, you may write a few short summary sentences but omit
the Args and Returns sections.

EXERCISE
~~~~~~~~

Write the missing docstring for the ``count_classes()`` function above.


EXERCISE
~~~~~~~~

Open up the Python3 interactive interpreter. Import your ``ml_data_analysis.py``
methods. Use the commands ``dir()`` and ``help()`` to find and read the docstrings
that you wrote.



Type Hints
----------

Type hints in function definitions indicate what types are expected as input and
output of a function. No checking actually happens at runtime, so if you send the
wrong type of data as an argument, the type hint itself won't cause it to return
an error. Think of type hints simply as documentation or annotations to help the
reader understand how to use a function.

.. warning::

   In the code blocks below, we omit docstrings for brevity only. Please keep
   including docstrings in your code.

Type hints should take form:

.. code-block:: python3

   def a_function(arg_name: arg_type) -> return_type:
       # code goes here
       return(result)

In the above example, we are providing a single argument called ``arg_name`` that
should be of type ``arg_type``. The expected return value should be ``return_type``.
Let's look at an example using a real function:

.. code-block:: python3

   def add_and_square(num1: float, num2: float) -> float:
       result = (num1+num2)**2
       return(result)


Next, add type hints to the function definitions of the ``ml_data_analysis.py``
script (only showing snippets below):

.. code-block:: python3

   from typing import List
   def compute_average_mass(a_list_of_dicts: List[dict], a_key_string: str) -> float:

.. code-block:: python3

   def check_hemisphere(latitude: float, longitude: float) -> float:

.. code-block:: python3

   def count_classes(a_list_of_dicts, a_key_string):  # what about this one?

In the first example above we need to include the line ``from typing import List``
to get access to a special object called ``List`` (capital **L**).
We use that to object to not only hint that ``a_list_of_dicts`` should be a list,
but it also includes information about what type of list we expect - in this case
it is a list of dictionaries. In Python3.6 you cannot do this with the normal
``list`` (lowercase **l***) object.

Although Python3 does not check or enforce types at run time, there are other
tools that make use of type hints to check types at the time of development. For
example, some IDEs (including PyCharm) will evaluate type hints as you write code
and provide an alert if you call a function in a way other than what the type
hint suggests. In addition, there are Python3 libraries like Mypy that can wrap
your Python3 programs and check / evaluate type hints as you go, provided errors
where types don't match.

.. warning::

   Be aware that there is some redundancy in the information contained in type hints
   and in the docstrings. Be careful not to let them get out of sync as your code
   evolves.



README
------

A README file should be included at the top level of every coding project you
work on. Websites like GitHub will automatically look for README files and render
them directly in the web interface. Markdown is probably the most common syntax
people use to write READMEs. It is very easy to create headers, code blocks,
tables, text emphases, and other fancy renderings to make the README pleasant and
easy to read.

.. note::

   In this class we ask you to include READMEs in each of your homework folders
   on GitHub. Each homework is essentially a standalone project, so a dedicated
   README for each is warranted.

At a minimum, plan to include the following sections in all of your READMEs:

* Title: a descriptive, self-explanatory title for the project.
* Description: a high-level description of the project that informs the reader
  what the code does, why it exists, what problem it solves, etc.
* Installation: As we advance into the semester our code bases will become more
  complex with more moving parts. Eventually we will need to start providing
  detailed instructions about getting the project working plus any requirements.
* Usage: The key here is **examples**! Show code blocks of what it looks like
  to execute the code from start to finish. Describe what output is expected and
  how it should be interpreted.

Other general advice includes:

* Use proper grammar and more-or-less complete sentences.
* Use headers, code blocks, and text emphases (e.g. bold, italics) to make the
  document readable. There are plenty of tools to preview Markdown before committing
  to GitHub, so plan to go through several cycles of editing -> previewing to
  make your README look nice.
* Be prepared to include other information about authors, acknowledgements, and
  licenses in the READMEs as appropriate
* Spend some time browsing GitHub and look for READMEs of other popular projects.
  There is no one correct way to write a README, but there are plenty of wrong ways.

Remember, the README is your chance to document for yourself and explain to others
why the project is important, what the code is, and how to use it / interpret the
outputs. The advice above is general advice, but it is not one-size-fits-all.
Every project is different and ultimately your README may include other sections
or organization schemes that are unique to your project.




Additional Resources
--------------------

* `Google Style Guide for docstrings <https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings>`_
* `Type hints spec <https://www.python.org/dev/peps/pep-0484/>`_
* `Mypy project <http://mypy-lang.org/index.html>`_
* `Markdown syntax <https://www.markdownguide.org/basic-syntax/>`_
* `Tips on writing a good README <https://www.makeareadme.com/>`_
