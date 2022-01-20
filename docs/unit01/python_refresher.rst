Python Refresher
================

To be successful in this class, students should be able to:

* Write and execute Python code on the class server
* Use variables, lists, and dictionaries in Python
* Write conditionals using a variety of comparison operators
* Write useful while and for loops
* Arrange code into clean, well organized functions
* Read input from and write output to a file
* Import and use standard and non-standard Python libraries

Topics covered in this module include:

* Data types and variables (ints, floats, bools, strings, type(), print())
* Arithmetic operations (+, -, \*, /, \*\*, %, //)
* Lists and dictionaries (creating, interpreting, appending)
* Conditionals and control loops (comparison operators, if/elif/else, while, for, break, continue, pass)
* Functions (defining, passing arguments, returning values)
* File handling (open, with, read(), readline(), strip(), write())
* Importing libraries (import, random, names, pip)


Log in to the Class Server
--------------------------

To log in to ``isp02.tacc.utexas.edu``, follow the instructions for your operating
system or ssh client below.

.. note::

   Replace ``username`` with your TACC username.

**Mac / Linux**

.. code-block:: console

   Open the application 'Terminal'
   ssh username@isp02.tacc.utexas.edu
   (enter password)
   (enter token)

**Windows (use WSL or an SCP client like PuTTY)**

.. code-block:: console

   Open the application 'PuTTY'
   enter Host Name: isp02.tacc.utexas.edu
   (click 'Open')
   (enter username)
   (enter password)
   (enter token)


If you can't access the class server yet, a local or web-based Python 3
environment will work for this guide. However, you will need to access the class
server for future lectures.

Try this `Python 3 environment in a browser <https://www.katacoda.com/scenario-examples/courses/environment-usages/python>`_.



.. note::

   For the first few sections below, we will be using the Python interpreter
   in *interactive mode* to try out different things. Later on when we get to
   more complex code, we will be saving the code in files (scripts) and invoking
   the interpreter non-interactively.


Data Types and Variables
------------------------

Start up the interactive Python interpreter:

.. code-block:: console

   [isp02]$ python3
   Python 3.6.8 (default, Aug  7 2019, 17:28:10)
   [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)] on linux
   Type "help", "copyright", "credits" or "license" for more information.
   >>>

.. tip::

   To exit the interpreter, type ``quit()``.

The most common data types in Python are similar to other programming languages.
For this class, we probably only need to worry about **integers**, **floats**,
**booleans**, and **strings**.

Assign some values to variables by doing the following:

.. code-block:: python3

   >>> my_int = 5
   >>> my_float = 5.0
   >>> my_bool = True      # or False, notice capital letters
   >>> my_string = 'Hello, world!'

In Python, you don't have to declare type. Python figures out the type
automatically. Check using the ``type()`` function:

.. code-block:: python3

   >>> type(my_int)
   <class 'int'>
   >>> type(my_float)
   <class 'float'>
   >>> type(my_bool)
   <class 'bool'>
   >>> type(my_string)
   <class 'str'>

Print the values of each variable using the ``print()`` function:

.. code-block:: python3

   >>> print(my_int)
   5
   >>> print('my_int')
   my_int

(Try printing the others as well). And, notice what happens when we print with
and without single quotes? What is the difference between ``my_int`` and
``'my_int'``?

You can convert between types using a few different functions. For example, when
you read in data from a file, numbers are often read as strings. Thus, you may
want to convert the string to integer or float as appropriate:

.. code-block:: python3

   >>> str(my_int)      # convert int to string
   >>> str(my_float)    # convert float to string
   >>> int(my_string)   # convert string to int
   >>> float(my_string) # convert string to float
   >>>
   >>> value = 5
   >>> print(value)
   5
   >>> type(value)
   <class 'int'>
   >>> new_value = str(value)
   >>> print(new_value)
   '5'
   >>> type(new_value)
   <class 'str'>


Arithmetic Operations
---------------------

Next, we will look at some basic arithmetic. You are probably familiar with the
standard operations from other languages:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Function          Example   Result
   +          Addition          1+1       2
   -          Subtraction       9-5       4
   *          Multiplication    2*2       4
   /          Division          8/4       2
   **         Exponentiation    3**2      9
   %          Modulus           5%2       1
   //         Floor division    5//2      2


Try a few things to see how they work:

.. code-block:: python3

   >>> print(2+2)
   >>> print(355/113)
   >>> print(10%9)
   >>> print(3+5*2)
   >>> print('hello' + 'world')
   >>> print('some' + 1)
   >>> print('number' * 5)


Also, carefully consider how arithmetic options may affect type:

.. code-block:: python3

   >>> number1 = 5.0/2
   >>> type(number1)
   <class 'float'>
   >>> print(number1)
   2.5
   >>> number2 = 5/2
   >>> type(number2)
   <class 'float'>
   >>> print(number2)
   2.5
   >>> print(int(number2))
   2


Lists and Dictionaries
----------------------

**Lists** are a data structure in Python that can contain multiple elements.
They are ordered, they can contain duplicate values, and they can be modified.
Declare a list with square brackets as follows:

.. code-block:: python3

   >>> my_shape_list = ['circle', 'heart', 'triangle', 'square']
   >>> type(my_shape_list)
   <class 'list'>
   >>> print(my_shape_list)
   ['circle', 'heart', 'triangle', 'square']

Access individual list elements:

.. code-block:: python3

   >>> print(my_shape_list[0])
   circle
   >>> type(my_shape_list[0])
   <class 'str'>
   >>> print(my_shape_list[2])
   triangle

Create an empty list and add things to it:

.. code-block:: python3

   >>> my_number_list = []
   >>> my_number_list.append(5)     # 'append()' is a method of the list class
   >>> my_number_list.append(6)
   >>> my_number_list.append(2)
   >>> my_number_list.append(2**2)
   >>> print(my_number_list)
   [5, 6, 2, 4]
   >>> type(my_number_list)
   <class 'list'>
   >>> type(my_number_list[1])
   <class 'int'>

Lists are not restricted to containing one data type. Combine the lists together
to demonstrate:

.. code-block:: python3

   >>> my_big_list = my_shape_list + my_number_list
   >>> print(my_big_list)
   ['circle', 'heart', 'triangle', 'square', 5, 6, 2, 4]

Another way to access the contents of lists is by slicing. Slicing supports a
start index, stop index, and step taking the form: ``mylist[start:stop:step]``.
Only the first colon is required. If you omit the start, stop, or :step, it is
assumed you mean the beginning, end, and a step of 1, respectively. Here are
some examples of slicing:

.. code-block:: python3

   >>> mylist = ['thing1', 'thing2', 'thing3', 'thing4', 'thing5']
   >>> print(mylist[0:2])     # returns the first two things
   ['thing1', 'thing2']
   >>> print(mylist[:2])      # if you omit the start index, it assumes the beginning
   ['thing1', 'thing2']
   >>> print(mylist[-2:])     # returns the last two things (omit the stop index and it assumes the end)
   ['thing4', 'thing5']
   >>> print(mylist[:])       # returns the entire list
   ['thing1', 'thing2', 'thing3', 'thing4', 'thing5']
   >>> print(mylist[::2])     # return every other thing (step = 2)
   ['thing1', 'thing3', 'thing5']

.. note::

   If you slice from a list, it returns an object of type list. If you access a
   list element by its index, it returns an object of whatever type that element
   is. The choice of whether to slice from a list, or iterate over a list by
   index, will depend on what you want to do with the data.


**Dictionaries** are another data structure in Python that contain key:value
pairs. They are always unordered, they cannot contain duplicate keys, and they
can be modified. Create a new dictionary using curly brackets:

.. code-block:: python3

   >>> my_shape_dict = {
   ...   'most_favorite': 'square',
   ...   'least_favorite': 'circle',
   ...   'pointiest': 'triangle',
   ...   'roundest': 'circle'
   ... }
   >>> type(my_shape_dict)
   <class 'dict'>
   >>> print(my_shape_dict)
   {'most_favorite': 'square', 'least_favorite': 'circle', 'pointiest': 'triangle', 'roundest': 'circle'}
   >>> print(my_shape_dict['most_favorite'])
   square

As your preferences change over time, so to can values stored in dictionaries:

.. code-block:: python3

   >>> my_shape_dict['most_favorite'] = 'rectangle'
   >>> print(my_shape_dict['most_favorite'])
   rectangle

Add new key:value pairs to the dictionary as follows:

.. code-block:: python3

   >>> my_shape_dict['funniest'] = 'squircle'
   >>> print(my_shape_dict['funniest'])
   squircle


Many other methods exist to access, manipulate, interpolate, copy, etc., lists
and dictionaries. We will learn more about them out as we encounter them later
in this course.

Conditionals and Control Loops
------------------------------

Python **comparison operators** allow you to add conditions into your code in
the form of ``if`` / ``elif`` / ``else`` statements. Valid comparison operators
include:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Comparison                 Example   Result
   ==         Equal                      1==2       False
   !=         Not equal                  1!=2       True
   >          Greater than               1>2        False
   <          Less than                  1<2        True
   >=         Greater than or equal to   1>=2       False
   <=         Less Than or equal to      1<=2       True

A valid conditional statement might look like:

.. code-block:: python3

   >>> num1 = 10
   >>> num2 = 20
   >>>
   >>> if (num1 > num2):                  # notice the colon
   ...     print('num1 is larger')        # notice the indent
   ... elif (num2 > num1):
   ...     print('num2 is larger')
   ... else:
   ...     print('num1 and num2 are equal')


In addition, conditional statements can be combined with **logical operators**.
Valid logical operators include:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Description                           Example
   and        Returns True if both are True         a < b and c < d
   or         Returns True if at least one is True  a < b or c < d
   not        Negate the result                     not( a < b )

For example, consider the following code:

.. code-block:: python3

   >>> num1 = 10
   >>> num2 = 20
   >>>
   >>> if (num1 < 100 and num2 < 100):
   ...     print('both are less than 100')
   ... else:
   ...     print('at least one of them is not less than 100')

**While loops** also execute according to conditionals. They will continue to
execute as long as a condition is True. For example:

.. code-block:: python3

   >>> i = 0
   >>>
   >>> while (i < 10):
   ...     print( f'i = {i}' )       # literal string interpolation
   ...     i = i + 1

The ``break`` statement can also be used to escape loops:

.. code-block:: python3

   >>> i = 0
   >>>
   >>> while (i < 10):
   ...     print( f'i = {i}' )
   ...     i = i + 1
   ...     if (i==5):
   ...         break
   ...     else:
   ...         continue


**For loops** in Python are useful when you need to execute the same set of
instructions over and over again. They are especially great for iterating over
lists:

.. code-block:: python3

   >>> my_shape_list = ['circle', 'heart', triangle', 'square']
   >>>
   >>> for shape in my_shape_list:
   ...     print(shape)
   >>>
   >>> for shape in my_shape_list:
   ...     if (shape == 'circle'):
   ...         pass                    # do nothing
   ...     else:
   ...         print(shape)

You can also use the ``range()`` function to iterate over a range of numbers:

.. code-block:: python3

   >>> for x in range(10):
   ...     print(x)
   >>>
   >>> for x in range(10, 100, 5):
   ...     print(x)
   >>>
   >>> for a in range(3):
   ...     for b in range(3):
   ...         for c in range(3):
   ...             print( f'{a} + {b} + {c} = {a+b+c}' )


.. note::

   The code is getting a little bit more complicated now. It will be better to
   stop running in the interpreter's interactive mode, and start writing our
   code in Python scripts.


Functions
---------

**Functions** are blocks of codes that are run only when we call them. We can
pass data into functions, and have functions return data to us. Functions are
absolutely essential to keeping code clean and organized.

On the command line, use a text editor to start writing a Python script:

.. code-block:: console

   [isp02]$ vim function_test.py


Enter the following text into the script:

.. code-block:: python3
   :linenos:

   def hello_world():
       print('Hello, world!')

   hello_world()

After saving and quitting the file, execute the script (Python code is not
compiled - just run the raw script with the ``python3`` executable):

.. code-block:: console

   [isp02]$ python3 function_test.py
   Hello, world!


.. note::

   Future examples from this point on will assume familiarity with using the
   text editor and executing the script. We will just be showing the contents of
   the script and console output.

More advanced functions can take parameters and return results:

.. code-block:: python3
   :linenos:

   def add5(value):
       return(value + 5)

   final_number = add5(10)
   print(final_number)

.. code-block:: console

   15

Pass multiple parameters to a function:

.. code-block:: python3
   :linenos:

   def add5_after_multiplying(value1, value2):
       return( (value1 * value2) + 5)

   final_number = add5_after_multiplying(10, 2)
   print(final_number)

.. code-block:: console

   25

It is a good idea to put your list operations into a function in case you plan
to iterate over multiple lists:

.. code-block:: python3
   :linenos:

   def print_ts(mylist):
       for x in mylist:
           if (x[0] == 't'):      # a string (x) can be interpreted as a list of chars!
               print(x)

   list1 = ['circle', 'heart', 'triangle', 'square']
   list2 = ['one', 'two', 'three', 'four']

   print_ts(list1)
   print_ts(list2)

.. code-block:: console

   triangle
   two
   three

There are many more ways to call functions, including handing an arbitrary
number of arguments, passing keyword / unordered arguments, assigning default
values to arguments, and more.

File Handling
-------------

The ``open()`` function does all of the file handling in Python. It takes two
arguments - the *filename* and the *mode*. The possible modes are read (``r``),
write (``w``), append (``a``), or create (``x``).

For example, to read a file do the following:


.. code-block:: python3
   :linenos:

   with open('/usr/share/dict/words', 'r') as f:
       for x in range(5):
           print(f.readline())

.. code-block:: text

   1080

   10-point

   10th

   11-point

   12-point



.. tip::

   By opening the file with the ``with`` statement above, you get built in
   exception handling, and it automatically will close the file handle for you.
   It is generally recommended as the best practice for file handling.


You may have noticed in the above that there seems to be an extra space between
each word. What is actually happening is that the file being read has newline
characters on the end of each line (``\n``). When read into the Python script,
the original new line is being printed, followed by another newline added by the
``print()`` function. Stripping the newline character from the original string
is the easiest way to solve this problem:

.. code-block:: python3
   :linenos:

   with open('/usr/share/dict/words', 'r') as f:
       for x in range(5):
           print(f.readline().strip('\n'))

.. code-block:: text

   1080
   10-point
   10th
   11-point
   12-point


Read the whole file and store it as a list:

.. code-block:: python3
   :linenos:

   words = []

   with open('/usr/share/dict/words', 'r') as f:
       words = f.read().splitlines()                # careful of memory usage

   for x in range(5):
       print(words[x])

.. code-block:: text

   1080
   10-point
   10th
   11-point
   12-point


Write output to a new file on the file system; make sure you are attempting to
write somwhere where you have permissions to write:

.. code-block:: python3
   :linenos:

   my_shapes = ['circle', 'heart', 'triangle', 'square']

   with open('my_shapes.txt', 'w') as f:
       for shape in my_shapes:
           f.write(shape)


.. code-block:: console

   (in my_shapes.txt)
   circlehearttrianglesquare


You may notice the output file is lacking in newlines this time. Try adding
newline characters to your output:

.. code-block:: python3
   :linenos:

   my_shapes = ['circle', 'heart', 'triangle', 'square']

   with open('my_shapes.txt', 'w') as f:
       for shape in my_shapes:
           f.write( f'{shape}\n' )

.. code-block:: console

   (in my_shapes.txt)
   circle
   heart
   triangle
   square


Now notice that the original line in the output file is gone - it has been
overwritten. Be careful if you are using write (``w``) vs. append (``a``).

Importing Libraries
-------------------

The Python built-in functions, some of which we have seen above, are useful but
limited. Part of what makes Python so powerful is the huge number and variety
of libraries that can be *imported*. For example, if you want to work with
random numbers, you have to import the 'random' library into your code, which
has a method for generating random numbers called 'random'.

.. code-block:: python3
   :linenos:

   import random

   for i in range(5):
       print(random.random())

.. code-block:: bash

   0.47115888799541383
   0.5202615354150987
   0.8892412583071456
   0.7467080997595558
   0.025668541754695906

More information about using the ``random`` library can be found in the
`Python docs <https://docs.python.org/3.6/library/random.html>`_

Some libraries that you might want to use are not included in the official
Python distribution - called the *Python Standard Library*. Libraries written
by the user community can often be found on `PyPI.org <https://pypi.org/>`_ and
downloaded to your local environment using a tool called ``pip3``.

For example, if you wanted to download the
`names <https://pypi.org/project/names/>`_ library and use it in your Python
code, you would do the following:

.. code-block:: bash

   [isp02]$ pip3 install --user names
   Collecting names
     Downloading https://files.pythonhosted.org/packages/44/4e/f9cb7ef2df0250f4ba3334fbdabaa94f9c88097089763d8e85ada8092f84/names-0.3.0.tar.gz (789kB)
       100% |████████████████████████████████| 798kB 1.1MB/s
   Installing collected packages: names
     Running setup.py install for names ... done
   Successfully installed names-0.3.0

Notice the library is installed above with the ``--user`` flag. The class server
is a shared system and non-privileged users can not download or install packages
in root locations. The ``--user`` flag instructs ``pip3`` to install the library
in your own home directory.

.. code-block:: python3
   :linenos:

   import names

   for i in range(5):
       print(names.get_full_name())


.. code-block:: bash

   Johnny Campbell
   Lawrence Webb
   Johnathan Holmes
   Mary Wang
   Jonathan Henry


Exercises
---------

Test your understanding of the materials above by attempting the following
exercises.

* Create a list of ~10 different integers. Write a function (using modulus and
  conditionals) to determine if each integer is even or odd. Print to screen
  each digit followed by the word 'even' or 'odd' as appropriate.
* Using nested for loops and if statements, write a program that iterates over
  every integer from 3 to 100 (inclusive) and prints out the number only if it
  is a prime number.
* Create three lists containing 10 integers each. The first list should contain
  all the integers sequentially from 1 to 10 (inclusive). The second list
  should contain the squares of each element in the first list. The third list
  should contain the cubes of each element in the first list. Print all three
  lists side-by-side in three columns. E.g. the first row should contain 1, 1, 1
  and the second row should contain 2, 4, 8.
* Write a script to read in /usr/share/dict/words and print just the last 10
  lines of the file. Write another script to only print words beginning with the
  letters "pyt".




Additional Resources
--------------------

* `The Python Standard Library <https://docs.python.org/3/library/>`_
* `PEP 8 Python Style Guide <https://www.python.org/dev/peps/pep-0008/>`_
* `Python3 environment in a browser <https://www.katacoda.com/scenario-examples/courses/environment-usages/python>`_
* `Jupyter Notebooks in a browser <https://jupyter.org/try>`_
