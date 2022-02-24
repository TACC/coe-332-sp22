Introduction to APIs
=====================
In this section, we will discuss Application Programming Interfaces (APIs)
focusing on Web APIs, and in particular REST APIs. We
will learn how to interact with APIs using Python scripts. After going through
this module, students should be able to:

* Identify and describe Web APIs (including REST APIs)
* Find API endpoints to various websites, e.g. Bitbucket and GitHub
* List and define the four most important HTTP verbs
* Install and import the Python requests library
* Interact with a web API using the Python requests library, and parse the
  return information


An Application Programming Interface (API) establishes the protocols and methods
for one piece of a program to communicate with another. APIs are useful for: 
  (1) Allowing larger software systems to be built from smaller components, 
  (2) Allowing the same component/code to be used by different systems, and 
  (3) Insulating consumers from changes to the implementation.

Some examples of APIs:

* In OOP languages, abstract classes provide the interface for all concrete
  classes to implement.
* Software libraries provide an external interface for consuming programs.
* Web APIs (or “web services”) provide interfaces for computer programs to
  communicate over the internet.

While a User Interface connects humans to computer programs, an API is an interface 
that connects one piece of software to another. 

APIs:

  (1) Provide functionality to external software in the form of a contract that specifies 
      the inputs that the consuming software must provide and then outputs that the API
      will produce from the inputs.
  (2) Conceals the implementation of this functionality from the consuming software so 
      that changes can be made to the implementation without impacting consumers.
  (3) Provide errors when the consuming software doesn't fullfill the contract or when 
      unexpected circumstances are encountered. 


We have already been working with APIs. For example, the Python ``json`` library 
presents us with an API for working with JSON data. 

.. code-block:: python3

    >>> import json
    >>> dir(json)
    ['JSONDecodeError',
    'JSONDecoder',
    'JSONEncoder',
    . . . 
    'codecs',
    'decoder',
    'detect_encoding',
    'dump',
    'dumps',
    'encoder',
    'load',
    'loads',
    'scanner']

We use ``json.dumps()`` to convert Python objects to JSON (string) data and we use 
``json.loads()`` to convert JSON strings to Python objects.

.. code-block:: python3

    >>> json.dumps({'a': 1})
    '{"a": 1}'
    >>> type(_)
    str
    >>> json.loads('{"a": 1}')
    {'a': 1}
    >>> type(_)
    dict

We say that ``dumps`` and ``loads`` are "part of the Python ``json`` API". 
