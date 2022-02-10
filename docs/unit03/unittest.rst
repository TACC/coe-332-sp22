Unit Testing
============

As our code continues to grow, how can we be sure it is working as expected? If
we make minor changes to the code, what tests can we run to make sure we didn't
break anything? Are our functions written well enough to capture and correctly
handle all of the edge cases we throw at them? In this module, we will use the
Python ``pytest`` library to write unit tests: small tests that are designed to
test specific individual components of code. After working through this module,
students should be able to:

* Find the documentation for the Python ``pytest`` library
* Identify parts of code that should be tested and appropriate assert methods
* Write and run reasonable unit tests


Getting Started
---------------

Unit tests are designed to test small components (e.g. individual functions) of
your code. They should demonstrate that things that are expected to work
actually do work, and things that are expected to break raise appropriate errors.
The Python ``pytest`` unit testing framework supports test automation, set up
and shut down code for tests, and aggregation of tests into collections. It is
not part of the Python Standard Library, so we must install it.

.. code-block:: console

   [isp02]$ pip3 install --user pytest

Find the `documentation here <https://docs.pytest.org/en/7.0.x/>`_.

Pull a copy of the
`meteorite landings data analysis script <https://raw.githubusercontent.com/TACC/coe-332-sp22/main/docs/unit03/scripts/ml_data_analysis.py>`_
we have been working on, and a copy of the
`meteorite landings json file <https://raw.githubusercontent.com/TACC/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.json>`_,
if you don't have copies already.


Devise a Reasonable Test
------------------------

The functions in this Python3 script are relatively simple, but how can we be
sure they are working as intended? Let's begin with the ``compute_average_mass()``
function. We might choose to test it manually using the Python3 interactive
interpreter:

.. code-block:: python3

   >>> from ml_data_analysis import compute_average_mass
   >>>
   >>> data = [{'thing': 1}, {'thing': 2}]
   >>>
   >>> print(compute_average_mass(data, 'thing'))
   1.5

So simple! We import our code, hand-craft a simple data structure, and send the
data plus the key we are interested in to our function. We know off the top of
our heads that the average of 1 and 2 is 1.5, and that is in fact the number we
get back.

Instead of writing that out each time we want to test, let's instead put this
into another Python3 script. When writing test scripts, it is a common convention
to name them the same name as the script you are testing, but with the ``test_``
prefix added at the beginning.


.. code-block:: console

   [isp02]$ ls
   Meteorite_Landings.json  ml_data_analysis.py
   [isp02]$ touch test_ml_data_analysis.py
   [isp02]$ ls
   Meteorite_Landings.json  ml_data_analysis.py  test_ml_data_analysis.py


Open up the script with VIM and put in our testing code from before:

.. code-block:: python3
   :linenos:

   from ml_data_analysis import compute_average_mass

   data = [{'thing': 1}, {'thing': 2}]
   print(compute_average_mass(data, 'thing'))


Next try to execute the test script on the command line:

.. code-block:: console

   [isp02]$ python3 test_ml_data_analysis.py
   1.5

Great! We assume the test is working. But we still have to look at the output
(1.5) and remember back to our hand-crafted data and make sure that is the correct
result. It would be more efficient if we had a way to check that the correct
answer is returned in our test script itself. To do this, we can use the ``assert``
statement.

.. code-block:: python3
   :linenos:
   :emphasize-lines: 4

   from ml_data_analysis import compute_average_mass

   data = [{'thing': 1}, {'thing': 2}]
   assert(compute_average_mass(data, 'thing') == 1.5)

Now instead of printing the result, we use ``assert`` to make sure it is equal
to our expected outcome. If the conditional is true, nothing will be printed. If
the conditional is false, we will see an ``AssertionError``.

EXERCISE
~~~~~~~~

* Write a few more tests to convince yourself that the function is in fact returning
  the average of the input values.
* Modify one of the tests so that it should fail, and execute the tests to confirm
  that it does fail.
* If you have multiple tests that pass and multiple tests that fail, how would you
  know?



Automate Testing with Pytest
----------------------------

Pytest is an excellent framework for small unit tests and for large functional
tests (as we will see later in the semester). If you previously installed pytest
with pip3, now would be a good time to double check that the installation worked
and there is an executable called ``pytest`` in your PATH:

.. code-block:: console

   [isp02]$ pytest --version
   pytest 7.0.0


Next, we just need to make a minor organizational change to our test code. Pytest
will automatically look in our working tree for files that start with the
``test_`` prefix, and execute the tests within.

.. code-block:: python3
   :linenos:
   :emphasize-lines: 3

   from ml_data_analysis import compute_average_mass

   def test_compute_average_mass():
       assert compute_average_mass([{'a': 1}, {'a': 2}], 'a') == 1.5
       assert compute_average_mass([{'a': 1}, {'a': 2}, {'a': 3}], 'a') == 2
       assert compute_average_mass([{'a': 10}, {'a': 1}, {'a': 1}], 'a') == 4


Call the ``pytest`` executable in your top directory, it will find your test
function in your test script, run that function, and finally print some
informative output:

.. code-block:: console

   ==================================== test session starts ====================================
   platform linux -- Python 3.6.8, pytest-7.0.0, pluggy-1.0.0
   rootdir: /home/wallen/coe-332/code-organization
   collected 1 item

   test_ml_data_analysis.py .                                                            [100%]

   ===================================== 1 passed in 0.01s =====================================


What Else Should We Test?
-------------------------

The simple tests we wrote above seem almost trivial, but they are actually great
sanity tests to tell us that our code is working. What other behaviors of our
``compute_average_mass()`` function should we test? In no particular order, we
could test the following non-exhaustive list:

* If the list only contains one dictionary object, the function still behaves as
  expected
* The return value should be type ``float``
* If we send it an empty list, that should raise some sort of exception
* If we send it a list of non-uniform dictionaries (e.g. the dictionaries don't
  all have the expected key), we should get a ``KeyError``
* If we send it bad values (e.g. a value is a string instead of an expected
  float), we should get a ``ValueError``
* If we send it a string that doesn't appear in the dictionaries, we should get
  a ``KeyError``

.. tip::

   A list of all of the built-in Python3 exceptions can be found in the
   `Python docs <https://docs.python.org/3.6/library/exceptions.html>`_.


To test some of these behaviors, let's create some additional assertions and
organize them into their own functions.


.. code-block:: python3
   :linenos:
   :emphasize-lines: 3

   from ml_data_analysis import compute_average_mass

   def test_compute_average_mass():
       assert compute_average_mass([{'a': 1}], 'a') == 1
       assert compute_average_mass([{'a': 1}, {'a': 2}], 'a') == 1.5
       assert compute_average_mass([{'a': 1}, {'a': 2}, {'a': 3}], 'a') == 2
       assert compute_average_mass([{'a': 10}, {'a': 1}, {'a': 1}], 'a') == 4
       assert isinstance(compute_average_mass([{'a': 1}, {'a': 2}], 'a'), float) == True

   def test_compute_average_mass_exceptions():
       with pytest.raises(ZeroDivisionError):
           compute_average_mass([], 'a')                               # send an empty list
       with pytest.raises(KeyError):
           compute_average_mass([{'a': 1}, {'b': 1}], 'a')             # dictionaries not uniform
       with pytest.raises(ValueError):
           compute_average_mass([{'a': 1}, {'a': 'x'}], 'a')           # value not a float
       with pytest.raises(KeyError):
           compute_average_mass([{'a': 1}, {'a': 2}], 'b')             # key not in dicts


After adding the above testes, run ``pytest`` again:

.. code-block:: console

   ==================================== test session starts ====================================
   platform linux -- Python 3.6.8, pytest-7.0.0, pluggy-1.0.0
   rootdir: /home/wallen/coe-332/code-organization
   collected 2 items

   test_ml_data_analysis.py ..                                                           [100%]

   ===================================== 2 passed in 0.01s =====================================

Success! The tests for our first function are passing. Our test suite essentially
documents our intent for the behavior of the ``compute_average_mass()`` function.
And, if ever we change the code in that function, we can see if the behavior we
intend still passes the test.


EXERCISE
~~~~~~~~

In the same test script, but under new test function definitions:

* Write tests for the ``check_hemisphere()`` function
* Write tests for the ``count_classes()`` function



Additional Resources
--------------------

* `Pytest documentation <https://docs.pytest.org/en/7.0.x/>`_.
* `Exceptions in Python <https://docs.python.org/3.6/library/exceptions.html>`_
