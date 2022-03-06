Advanced Flask
==============
We continue using Flask in this module with a look at more complex endpoints and data retrieval 
functions for our REST API. After going through this module, students should be able to:

* Identify valid and invalid Flask route return types
* Convert unsupported types (e.g. ``list``) to valid Flask route return types
* Extract Content-Type and other headers from Flask route responses
* Add query parameters to GET requests, and extract their values inside Flask routes
* Deal with errors from user-supplied input to an API and handle Python exceptions.


Defining the URLs of Our API
----------------------------

One of our first goals for our API will be to provide an interface to a dataset. Since
the URLs in a REST API are defined by the "nouns" or collections of the
application domain, we can use a noun that represents our data.

For example, suppose we have the following dataset that represents the number of
students earning an undergraduate degree for a given year:

.. code-block:: python3

   def get_data():
       return [ {'id': 0, 'year': 1990, 'degrees': 5818},
       {'id': 1, 'year': 1991, 'degrees': 5725},
       {'id': 2, 'year': 1992, 'degrees': 6005},
       {'id': 3, 'year': 1993, 'degrees': 6123},
       {'id': 4, 'year': 1994, 'degrees': 6096} ]


In this case, one collection described by the data is "degrees". So, let's
define a route, ``/degrees``, that by default returns all of the data points.

**EXERCISE**

Create a new file, ``degrees_api.py`` to hold a Flask application then do the
following:

    1) Import the Flask class and instantiate a Flask application 
    object.

    2) Add code so that the Flask server is started when this file is executed
    directly by the Python interpreter.

    3) Copy the ``get_data()`` method above into the application 
    script.

    4) Add a route (``/degrees``) which responds to the HTTP ``GET`` request and
    returns the complete list of data returned by ``get_data()`` as a Python string. **Hint:** 
    You won't 
    be able to return a Python list directly from your route function -- consider
    casting the list to a string using the ``str()`` function. 

In a separate Terminal use ``curl`` to test out your new route. Does it work as
expected?

.. tip::

   Refer back to the `Intro to Flask material <flask_1.html>`_ if
   you need help remembering the boiler-plate code.


Responses in Flask
------------------

If you tried to return the list object directly in your route function
definition, you got an error when you tried to request it with curl. Something
like:

.. code-block:: console

   TypeError: The function did not return a valid response

Flask allows you three options for creating responses:

1) Return a string (``str``) object
2) Return a dictionary (``dict``) object
3) Return a tuple (``tuple``) object
4) Return a ``flask.Response`` object

Some notes:

* Option 1 is good for text or html such as when returning a web page or text
  file.
* Option 2 is good for returning rich information in JSON-esque format.
* Option 3 is good for returning a list of data using a special type of Python
  list - a ``tuple`` - which is ordered and unchangeable.
* Option 4 gives you the most flexibility, as it allows you to customize the
  headers and other aspects of the response.

For our REST API, we will want to return JSON-formatted data. We will use a
special Flask method to convert our list to JSON - ``flask.jsonify``. (More on
this later.)

.. tip::

   Refer back to the `Working with JSON material <../unit02/json.html>`_ for a
   primer on the JSON format and relevant JSON-handling methods.


**EXERCISE 2**

Try doing this exercise in a Python (or ``ipython``) shell.

    1) Serialize the list returned by the ``get_data()`` method above into a
    JSON-formatted string using the Python ``json`` library. Verify that the type
    returned is a string.

    2) Next, Deserialize the string returned in part a) by using the ``json`` library
    to decode it. Verify that the result equals the original list.


Returning JSON (and Other Kinds of Data)
----------------------------------------

You probably are thinking at this point we can fix our solution to the first **Exercise**
by using the ``json`` library (which function?). Let's try that and see what
happens:

**EXERCISE 3**

Update your code from the first Exercise to use the ``json`` library to return a properly
formatted JSON string.

Then, with your API server running in one window, open a Python3 interactive
session in another window and:

* Make a ``GET`` request to your ``/degrees`` URL and capture the response in a
  variable, say ``r``
* Verify that ``r.status_code`` is what you expect (what do you expect it to be?)
* Verify that ``r.content`` is what you expect.
* Use ``r.json()`` to decode the response and compare the type to that of ``r.content``.

HTTP Content Type Headers
-------------------------

Requests and responses have ``headers`` which describe additional metadata about
them. Headers are ``key: value`` pairs (much like dictionary entries). The ``key``
is called the header name and the ``value`` is the header value.

There are many pre-defined headers for common metadata such as specifying the
size of the message (``Content-Length``), the domain the server is listening on
(``Host``), and the type of content included in the message (``Content-Type``).


We can use ``curl`` or the python ``requests`` library to see all of the headers
returned on a response from our flask server. Let's try it.

**EXERCISE 4**

  1) Use ``curl`` to make a GET request to your ``/degrees`` endpoint 
  and pass the ``-v`` (for "verbose") option. This will show you additional information, 
  including the headers. Note that with ``-v``, curl shows headers on both the request and
  the response. Request headers are lines that start with a ``>`` while response headers are
  lines that start with a ``<``.
  
  2). Use ``curl`` again to make the same request, but this time pass the ``--head``
  option instead of the ``-v``; this will show you **only** the headers being 
  returned in the response. 

  3) Inside a python shell, use ``requests`` to make the same GET request to your ``/degrees``
  endpoint, and capture the result in a variable, ``r``. Inspect the ``r.header`` attribute.
  What is the type of ``r.headers``?


.. code-block:: console

  curl localhost:5000/degrees -v

   * About to connect() to localhost port 5000 (#0)
   *   Trying ::1...
   * Connection refused
   *   Trying 127.0.0.1...
   * Connected to localhost (127.0.0.1) port 5000 (#0)
   > GET /degrees HTTP/1.1
   > User-Agent: curl/7.29.0
   > Host: localhost:5000
   > Accept: */*
   > 
   * HTTP 1.0, assume close after body
   < HTTP/1.0 200 OK
   < Content-Type: text/html; charset=utf-8
   < Content-Length: 210
   < Server: Werkzeug/2.0.3 Python/3.6.8
   < Date: Fri, 04 Mar 2022 01:12:34 GMT


.. code-block:: python

   In [1]: import requests

   In [2]: r = requests.get('http://127.0.0.1:5000/degrees')

   In [3]: r.headers
   Out[3]: {'Content-Type': 'text/html; charset=utf-8', 'Content-Length': '210', 'Server': 'Werkzeug/2.0.3 Python/3.6.8', 'Date': 'Fri, 04 Mar 2022 01:21:41 GMT'}

We see that we are sending a ``Content-type`` of ``'text/html``. In some ways, that is true, but
we can do better; we can tell the client we are sending ``json`` data.


Media Type (or Mime Type)
~~~~~~~~~~~~~~~~~~~~~~~~~

The allowed values for the ``Content-Type`` header are the defined
**media types** (formerly, **mime types**). The main thing you want to know
about media types are that they:

* Consist of a type and subtype
* The most common types are application, text, audio, image, and multipart
* The most common values (type and subtype) are application/json,
  application/xml, text/html, audio/mpeg, image/png, and multipart/form-data

Content Types in Flask
~~~~~~~~~~~~~~~~~~~~~~

The Flask library has the following built-in conventions you want to keep in
mind:

* When returning a string as part of a route function in Flask, a
  ``Content-Type`` of ``text/html`` is returned.
* To convert a Python object to a JSON-formatted string **and** set the content
  type properly, use the ``flask.jsonify()`` function.

For example, the following code will convert the list to a JSON string and
return a content type of aplication/json:

.. code-block:: python3

   return flask.jsonify(['a', 'b', 'c'])


**EXERCISE 4**

Use the ``flask.jsonify()`` method to update your code from Exercise 1. Then:


* Validate that your ``/degrees`` endpoint works as expected by using the
  ``requests`` library to make an API request and check that the ``.json()``
  method works as expected on the response.
* Use the ``.headers()`` method on the response to verify the ``Content-Type``
  is what you expect.

Query Parameters
----------------

The HTTP specification allows for parameters to be added to the URL in form of
``key=value`` pairs. Query parameters come after a ``?`` character and are
separated by ``&`` characters; for example, the following request to a hypothetical API:

.. code-block:: console

      GET https://api.example.com/degrees?limit=3&offset=2

passes two query parameters: ``limit=3`` and ``offset=2``. Note that the URL path in 
the example above is still ``/degrees``; that is, the ``?`` character terminates the URL
path, and any characters that follow create the query parameter set for the request.

In REST architectures, query parameters are often used to allow clients to
provide additional, optional arguments to the request.

Common uses of query parameters in RESTful APIs include:

* Pagination: specifying a specific page of results from a collection
* Search terms: filtering the objects within a collection by additional search
  attributes
* Other parameters that might apply to most if not all collections such as an
  ordering attribute (``ascending`` vs ``descending``)


Extracting Query Parameters in Flask
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Flask makes the query parameters available on the ``request.args`` object, which
is a "dictionary-like" object. To work with the query parameters supplied on a
request, you must import the Flask request method (this is different from the
Python3 ``requests`` library), and use an imbedded method to extract the passed
query parameter into a variable:

.. code-block:: python3

   from flask import Flask, request

   @app.route('/degrees', methods=['GET'])
   def degrees():
       start = request.args.get('start')
       # additional code...


The ``start`` variable will be the value of the ``start`` parameter, if one is
passed, or it will be ``None`` otherwise:

.. code-block:: console

   GET https://api.example.com/degrees?start=2


.. note::

   ``request.args.get()`` will always return a ``string``, regardless of the
   type of data being passed in.


Let's use this idea to update our ``degrees_api`` to only return the years starting from the
``start`` query parameter year, if that parameter is provided. 


.. code-block:: python

   @app.route('/degrees', methods=['GET'])
   def degrees():
      d = get_data()
      start = int(request.args.get('start', 0))
      return jsonify(d)


Error Handling
--------------

This brings up the topic of error handling. What happens if the user sends a value for the ``start``
query parameter that isn't an integer? We can test it ourselves.

.. code-block:: console

  curl 127.0.0.1:5000/degrees?start=abc


If we try this we get some nasty stuff that ends with a traceback, like this:

.. code-block:: console
   # ....

   Traceback (most recent call last):
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 2091, in __call__
      return self.wsgi_app(environ, start_response)
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 2076, in wsgi_app
      response = self.handle_exception(e)
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 2073, in wsgi_app
      response = self.full_dispatch_request()
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 1518, in full_dispatch_request
      rv = self.handle_user_exception(e)
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 1516, in full_dispatch_request
      rv = self.dispatch_request()
   File "/home/jstubbs/.local/lib/python3.6/site-packages/flask/app.py", line 1502, in dispatch_request
      return self.ensure_sync(self.view_functions[rule.endpoint])(**req.view_args)
   File "/home/jstubbs/coe332-sp22/flask_2/degrees_api_2.py", line 31, in degrees3
      start = int(request.args.get('start', 0))
   ValueError: invalid literal for int() with base 10: 'foo'


Checking User Input
~~~~~~~~~~~~~~~~~~~

If we get a request like this, in the best case, the user didn't understand what kind of data to put
in the ``start`` query parameter; in the worst case, they were intentionally trying to send our 
program bad data to break it. We should always be very careful with user-supplied data and make sure
it contains the kind of data we expect.

So, what is it we expect from the ``start`` query parameter? At a minimum, it needs to be some kind
of integer data, because we are casting it to the ``int`` type. Therefore, at a minimum, we should
check if it is an integer.

We can use the Python ``isnumeric()`` method on a Python string to test whether a string 
contains non-negative integer data.

Let's try some examples in the Python shell:

.. code-block:: python

   >>> '123'.isnumeric()
   True
   >>> 'abc'.isnumeric()
   False
   >>> '1.23'.isnumeric()
   False
   >>> '-1'.isnumeric()
   False


Now, let's fix our route function; we can check if it is numeric before casting to an ``int``.
If it is not numeric, we can return an error message to the user.

.. code-block:: python

    d = get_data()
    start = request.args.get('start', 0)
    if not start.isnumeric():
        return 'Invalid start value; start must be numeric.'
    start = int(start)
    return jsonify(d[start:])


Exceptions 
~~~~~~~~~~
Using the ``isnumeric()`` function allowed us to check for invalid user input in the specific
case above, but Python provides a far more general and powerful error handling capability, called 
Exceptions, that we will discuss next. 

In Python, exceptions are the mechanism one typically uses to communicate and deal with run-time
errors. Exceptions are different from syntax errors where, in general, there is no hope of the code 
working. Exceptions occur with statements that are syntactically correct but nonetheless generate 
some kind of error at runtime. Typically, the program can recover from these types of errors.

In Python, exceptions are instances of the class ``Exception`` or a child class. We say that a 
statement *generates* or *raises* an exception.

Some common situations that generate exceptions are:

* Trying to open a file that does not exist raises a ``FileNotFoundError``.

* Trying o divide by zero raises a ``ZeroDivisionError``.

* Trying to access a list at an index beyond its length raises an ``IndexError``.

* Trying to use an object of the wrong type in a function raises a ``TypeError`` (for example,
  trying to call ``json.dumps()`` with an object that is not of type ``str``.)

* Trying to use an object with the wrong kind of value in a function raises a ``ValueError`` 
  (for example, calling ``int('abc')``.)

* Trying to access a non-existent attribute on an object raises an ``AttributeError`` (a special 
  case is accessing a null/uninitialized object, resulting in the dreaded 
  ``AttributeError: 'NoneType' object has no attribute 'foo'`` error.)



Handling Exceptions
~~~~~~~~~~~~~~~~~~~

If a statement we execute in our code, such as a call to the ``int()`` function to cast an object 
to an integer, could raise an exception, we can handle the exception by using the ``try...except...`` 
statement. It works like this:

.. code-block:: python

    try:
        # execute some statements that could raise an exception...
        f(x, y, z)
    except ExceptionType1 as e:
        # do something if the exception was of type ExceptionType1...
    except ExceptionType2 as e:
        # do something if the exception was of type ExceptionType2...
    
    # . . . additional except blocks . . .
     
    finally:
        # do something regardless of whether an exception was raised or not.

A few notes:

* If a statement(s) within the ``try`` block does not raise an exception, the ``except`` blocks are 
  skipped.
* If a statement within the ``try`` block does raise an exception, Python looks at the 
  ``except`` blocks for the first one matching the type of the exception raised and executed that 
  block of code.
* The ``finally`` block is optional but it executes regardless of whether an exception was raised
  by a statement in the 
* The ``as e`` clause puts the exception object into a variable (``e``) that we can use.
* The use of ``e`` was arbitrary; we could choose to use any other valid variable identifier.
* We can also leave off the ``as e`` part altogether if we don't need to reference the exception
  object in our code.

Here's how we could deal with an invalid ``start`` parameter provided by the user 
using exceptions:

.. code-block:: python

    try:
        start = int(start)
    except ValueError:
        # return some kind of error message...

    # at this point in the code, we know the int(start) "worked" and so we are safe 
    # to use it as an integer..
   

Exception Hierarchy
~~~~~~~~~~~~~~~~~~~

Exceptions form a class hierarchy with the base ``Exception`` class being at the root. So, 
for example: 

* ``FileNotFoundError`` is a type of ``OSError`` as is ``PermissionError``, which is raised in case 
  the attempted file access is not permitted by the OS due to lack of permissions.
* ``ZeroDivisionError`` and ``OverflowError`` are instances of ``ArithmeticError``, the latter
  being raised whenever the result of a calculation exceeds the limits of what can be represented 
  (try running ``2.**5000`` in a Python shell).
* Every built-in Python exception is of type ``Exception``.

Therefore, we could use any of the following to deal with a ``FileNotFoundError``:

* ``except FileNotFoundError``
* ``except OSError``
* ``except Exception``


Here are some best practices to keep in mind for handling exceptions:

* Put a minimum number of statements within a ``try`` block so that you can detect which 
  statement caused the error.
* Similarly, put the most specific exception type in the ``except`` block that is appropriate 
  so that you can detect exactly what went wrong. Using ``except Exception...`` should 
  be seen as a last resort 
  because an ``Exception`` could be any kind of error.


Here is the full code for our route function with exception handling. 

.. code-block:: python

   @app.route('/degrees', methods=['GET'])
   def degrees():
      d = get_data()
      start = request.args.get('start')
      if start:
         try:
             start = int(start)
         except ValueError:
             return "Invalid start parameter; start must be an integer."
      return jsonify(d[start:])



**EXERCISE 5**


Add support for a ``limit`` parameter to the code you wrote for Exercise 4. The
``limit`` parameter should be optional. When passed with an integer value, the
API should return no more than ``limit`` data points.



Additional Resources
--------------------

* `Flask JSON support <https://flask.palletsprojects.com/en/1.1.x/api/?highlight=jsonify#module-flask.json>`_
* `Flask query parameter support <https://flask.palletsprojects.com/en/1.1.x/api/?highlight=jsonify#flask.Request.args>`_