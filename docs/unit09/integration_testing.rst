Integration Testing
===================

Unlike unit tests, integration tests exercise multiple components, functions, or
units of a software system at once. Some properties of integration tests include:

* Each test targets a higher-level capability, requirement or behavior of the
  system, and exercises multiple components of the system working together.
* Broader scope means fewer tests are required to cover the entire application/system.
* A given test failure provides more limited information as to the root cause.

It's worth pointing out that our definition of integration test leaves some
ambiguity. You will also see the term "functional tests" used for tests the
exercise entire aspects of a software system. After going through this module,
students should be able to:

* Identify Python frameworks for integration testing
* Identify aspects of a software system that should be tested with integration testing
* Use the Python requests library to interact with the API of your software system




Challenges When Writing Integration Tests
-----------------------------------------

Integration tests against large, distributed systems with lots of components
that interact face some challenges.

* We want to keep tests independent so that a single test can be run without its
  result depending on other tests.
* Most interesting applications change "state" in some way over time; e.g., files
  are saved/updated, database records are written, queue systems updated. In order
  to properly test the system, specific state must be established before and after
  a test (for example, inserting a record into a database before testing the
  "update" function).
* Some components have external interactions, such as an email server,
  a component that makes an update in an external system (e.g. GitHub) etc. A
  decision has to be made about whether or not this functionality will be
  validated in the test and if so, how.



Initial Integration Tests for Our Flask API
-------------------------------------------

For our first set of integration tests, we'll use the following strategy:

* Start the Flask API, Redis DB, and Worker services
* Use ``pytest`` and ``requests`` to make requests directly to the running API
  server
* Check various aspects of the response; each check can be done with a simple
  assert statement, just like for unit tests


A Simple pytest Example
-----------------------

Similar to unit tests, we will use ``assert`` statements to check that some input
data or command returns the expected result. A simple example of using pytest
might look like:

.. code-block:: python3
   :linenos:

   import pytest, requests

   def test_flask():
       response = requests.get('http://localhost:5000/route')
       assert response.status_code == 200

This small test just checks to make sure curling the route (with the Python
requests library) returns a successful status code, ``200``.

As we have seen before, test scripts should be named strategically and organized
into a subdirectory similar to:

.. code-block:: text

    my-api/
    ├── data
    │   └── dump.rdb
    ├── docker
    │   ├── Dockerfile.api
    │   └── Dockerfile.wrk
    ├── Makefile
    ├── README.md
    ├── src
    │   ├── api.py
    │   ├── jobs.py
    │   └── worker.py
    └── test
        └── test_api.py


Run the test simply by typing this in the top (``my-api/``) directory:

.. code-block:: console

   [isp02]$ pytest
   ========================= test session starts ==========================
   platform linux -- Python 3.6.8, pytest-6.2.3, py-1.10.0, pluggy-0.13.1
   rootdir: /home/wallen/coe-332/my-api
   collected 1 item

   test/test_api.py .                                               [100%]

   ========================== 1 passed in 0.17s ===========================



.. tip::

   Don't forget to ``pip3 install --user pytest`` first.



EXERCISE
~~~~~~~~

Continue working in the test file, ``test_api.py``, and write a new functional
test that use the ``requests`` library to make a ``GET`` request to the ``/jobs``
endpoint and check the response for, e.g.:

* The response returns a 200 status code
* The response returns a valid JSON string
* The response can be decoded to a Python dictionary
* Each element of the decoded list is a Python dictionary
* Each dictionary in the result has two keys
* Verify that the type of each key’s value is correct

Remember, your services should be running and as much as possible, functional tests
should be testing the end-to-end functionality of your entire app.
