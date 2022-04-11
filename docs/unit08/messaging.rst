Messaging Systems
=================

The Queue is a powerful data structure which forms the foundation of many concurrent design patterns. Often, these
design patterns center around passing messages between agents within the concurrent system. We will explore one of the
simplest and most useful of these message-based patterns - the so-called "Task Queue". Later, we may also look at the
somewhat related "Publish-Subscribe" pattern (also sometimes referred to as "PubSub").

By the end of this module, the student should be able to:

  * Describe the components of a task queue system, and explain how it will be utilized within our 
    Flask-based API system architecture.
  * Create task queues in Redis using the ``hotqueue`` library, and work with the ``put()`` and 
    ``consume()`` methods to queue and receive messages across two Python programs. 
  * Use the ``q.worker`` decorator in the ``hotqueue`` to create a simple Python consumer program.
  * Explain the general approach to organizing Python code into different modules and describe how to
    do this for the flask-based API system we are building. 
  * Implement good code organization practices including denoting objects as public or private. 


Task Queue (or Work Queue)
--------------------------

In a task queue system,

  * Agents called "producers" write messages to a queue that describe work to be done.
  * A separate set of agents called "consumers" receive the messages and do the work. While work is being done,
    no new messages are received by the consumer.
  * Each message is delivered exactly once to a single consumer to ensure no work is "duplicated".
  * Multiple consumers can be processing "work" messages at once, and similarly, 0 consumers can be processing messages
    at a given time (in which case, messages will simply queue up).

The Task Queue pattern is a good fit for our jobs service.

  * Our Flask API will play the role of producer.
  * One or more "worker" programs will play the role of consumer.
  * Workers will receive messages about new jobs to execute and performing the analysis steps.

Task Queues in Redis
--------------------
The ``HotQueue`` class provides two methods for creating a task queue consumer; the first is the ``.consume()`` method
and the second is the ``q.worker`` decorator.

The Consume Method
^^^^^^^^^^^^^^^^^^

With a ``q`` object defined like ``q = HotQueue("some_queue", host="<Redis_IP>", port=6379, db=1)``,
the consume method works as follows:

  * The ``q.consume()`` method returns an iterator which can be looped over using a ``for`` loop (much like a list).
  * The ``q.consume()`` method blocks (i.e., waits indefinitely) when there are no additional messages in the queue
    named ``some_queue``.

The basic syntax of the consume method is this:

.. code-block:: python

    for item in q.consume():
        # do something with item


**Exercises.** Complete the following:

  1. Start/scale two python debug containers with redis and hotqueue installed (you can use the ``jstubbs/redis-client`` image
     if you prefer). In two separate shells, exec into each debug container and start ipython.
  2. In each terminal, create a ``HotQueue`` object pointing to the same Redis queue.
  3. In the first terminal, add three or four Python strings to the queue; check the length of the queue.
  4. In the second terminal, use a ``for`` loop and the ``.consume()`` method to print objects in the queue to the screen.
  5. Observe that the strings are printed out in the second terminal.
  6. Back in the first terminal, check the length of the queue; add some more objects to the queue.
  7. Confirm the newly added objects are "instantaneously" printed to the screen back in the second terminal.

The q.worker Decorator
^^^^^^^^^^^^^^^^^^^^^^
Given a Hotqueue queue object, ``q``, the ``q.worker`` decorator is a convenience utility to turn a function into a consumer
without having to write the for loop. The basic syntax is:

.. code-block:: python

    @q.worker
    def do_work(item):
        # do something with item

In the example above, ``item`` will be populated with the item dequeued.

Then, to start consuming messages, simply call the function:

.. code-block:: python

    >>> do_work()
    # ... blocks until new messages arrive


.. note::

  The ``@q.worker`` decorator replaces the ``for`` loop. Once you call a function decorated with ``@q.worker``, the
  code never returns unless there is an unhandled exception.



**Exercise.** Write a function, ``echo(item)``, to print an item to the screen, and use the ``q.worker`` decorator to
turn it into a consumer. Call your echo function in one terminal and in a separate terminal, send messages to the
redis queue. Verify that the message items are printed to the screen in the first terminal.


In practice, we will use the ``@q.worker`` in a Python source file like so --

.. code-block:: python

  # A simple example of Python source file, worker.py
  q = HotQueue("some_queue", host="<Redis_IP>", port=6379, db=1)

  @q.worker
  def do_work(item):
      # do something with item...

  do_work()


Assuming the file above was saved as ``worker.py``, calling ``python worker.py`` from the shell would result in a
non-terminating program that "processed" the items in the ``"some_queue"`` queue using the ``do_work(item)`` function.
The only thing that would cause our worker to stop is an unhandled exception.

Concurrency in the Jobs API
---------------------------
Recall that our big-picture goal is to add a Jobs endpoint to our Flask system that can process long-running tasks.
We will implement our Jobs API with concurrency in mind. The goals will be:

  * Enable analysis jobs that take longer to run than the request/response cycle (typically, a few seconds or less).
  * Deploy multiple "worker" processes to enable more throughput of jobs.

The overall architecture will thus be:

   a) Save the request in a database and respond to the user that the analysis will eventually be run.
   b) Give the user a unique identifier with which they can check the status of their job and fetch the results when
      they are ready,
   c) Queue the job to run so that a worker can pick it up and run it.
   d) Build the worker to actually work the job.

Parts a), b) and c) are the tasks of the Flask API, while part d) will be a worker, running as a separate pod/container,
that is waiting for new items in the Redis queue.


Code Organization
-----------------

As software systems get larger, it is very important to keep code organized so that finding the functions, classes,
etc. responsible for different behaviors is as easy as possible. To some extent, this is technology-specific, as
different languages, frameworks, etc., have different rules and conventions about code organization. We'll focus on
Python, since that is what we are using.

The basic unit of code organization in Python is called a "module". This is just a Python source file (ends in a ``.py``
extension) with variables, functions, classes, etc., defined in it. We've already used a number of modules, including
modules that are part of the Python standard library (e.g. ``json``) and modules that are part of third-party libraries
(e.g., ``redis``).

The following should be kept in mind when designing the modules of a larger system:

  * Modules should be focused, with specific tasks or functionality in mind, and their names (preferably, short)
    should match their focus.
  * Modules are also the most typical entry-point for the Python interpreter itself, (e.g., ``python some_module.py``).
  * Accessing code from external modules is accomplished through the ``import`` statement.
  * Circular imports will cause errors - if module A imports an object from module B, module B cannot import from module A.

**Examples.** The Python standard library is a good source of examples of module design. You can browse the
standard library for Python 3.9 `here <https://docs.python.org/3/library/>`_.

  * We see the Python standard library has modules focused on a variety of computing tasks; for example, for working
    with different data types, such as the ``datetime`` module and the ``array`` module.  The descriptions are succinct:

    * *The datetime module supplies classes for manipulating dates and times.*
    * *This module defines an object type which can compactly represent an array of basic values: characters, integers, floating point numbers*

  * For working with various file formats: e.g., ``csv``, ``configparser``
  * For working with concurrency: ``threading``, ``multiprocessing``, etc.


With this in mind, a first approach might be to break up our system into two modules:

  * ``api.py`` - this module contains the flask web server.
  * ``worker.py`` - this module contains the code to execute jobs.

However, both the API server and the workers will need to interact with the database and the queue:

  * The API will create new jobs in the database, put new jobs onto the queue, and retrieve the status of jobs
    (and probably the output products of the job).
  * The worker will pull jobs off the queue, retrieve jobs from the database, and update them.

This suggests a different structure:

  * ``api.py`` - this module contains the flask web server.
  * ``jobs.py`` - this module contains core functionality for working with jobs in Redis (and on the queue).
  * ``worker.py`` - this module contains the code to execute jobs.

Common code for working with ``redis``/``hotqueue`` can go in the ``jobs.py`` module and be imported in both ``api.py``
and ``worker.py``.

.. note::

  High-quality modular design is a crucial aspect of building good software. It requires significant thought and
  experience to do correctly, and when done poorly it can have dire consequences. In the best case, poor module
  design can make the software difficult to maintain/upgrade; in the worst case, it can prevent it from running
  correctly at all.


Private vs Public Objects
-------------------------
As software projects grow, the notion of public and private access points (functions, variables, etc.) becomes an increasingly
important part of code organization.

  * Private objects should only be used within the module they are defined. If a developer needs to change the
    implementation of a private object, she only needs to make sure the changes work within the existing module.
  * Public objects can be used by external modules. Changes to public objects need more careful analysis to understand
    the impact across the system.

Like the layout of code itself, this topic is technology-specific. In this class, we
will take a simplified approach based on our use of Python. Remember, this is a simplification to illustrate the basic
concepts - in practice, more advanced/robust approaches are used.

  * We will name private objects starting with a single underscore (``_``) character.
  * If an object does not start with an underscore, it should be considered public.

We can see public and private objects in use within the standard library as well. If we open up the source code for the
``datetime`` module, which can be found `on GitHub <https://github.com/python/cpython/blob/3.9/Lib/datetime.py>`_ we see a mix
of public and private objects and methods.

  * Private objects are listed first.
  * Public objects start on `line 473 <https://github.com/python/cpython/blob/3.9/Lib/datetime.py#L473>`_ with
    the ``timedelta`` class.


**Exercise.** Create three files, ``api.py``, ``worker.py`` and ``jobs.py`` in your local repository, and update
them by working through the following example.

Here are some function and variable definitions, some of which have incomplete implementations and/or have invalid syntax.

To begin, place them in the appropriate files. Also, determine if they should be public or private.

.. code-block:: python

    def generate_jid():
        """
        Generate a pseudo-random identifier for a job.
        """
        return str(uuid.uuid4())

    app = Flask(__name__)

    def generate_job_key(jid):
        """
        Generate the redis key from the job id to be used when storing, retrieving or updating 
        a job in the database. 
        """
        return 'job.{}'.format(jid)

    q = HotQueue("queue", host='172.17.0.1', port=6379, db=1)

    def instantiate_job(jid, status, start, end):
        """
        Create the job object description as a python dictionary. Requires the job id, status, 
        start and end parameters.
        """
        if type(jid) == str:
            return {'id': jid,
                    'status': status,
                    'start': start,
                    'end': end
            }
        return {'id': jid.decode('utf-8'),
                'status': status.decode('utf-8'),
                'start': start.decode('utf-8'),
                'end': end.decode('utf-8')
        }

    @app.route('/jobs', methods=['POST'])
    def jobs_api():
        """
        API route for creating a new job to do some analysis. This route accepts a JSON payload 
        describing the job to be created. 
        """
        try:
            job = request.get_json(force=True)
        except Exception as e:
            return True, json.dumps({'status': "Error", 'message': 'Invalid JSON: {}.'.format(e)})
        return json.dumps(jobs.add_job(job['start'], job['end']))

    def save_job(job_key, job_dict):
        """Save a job object in the Redis database."""
        rd.hset(.......)

    def queue_job(jid):
        """Add a job to the redis queue."""
        ....

    if __name__ == '__main__':
        """
        Main entrypoint of the API server
        """
        app.run(debug=True, host='0.0.0.0')

    def add_job(start, end, status="submitted"):
        """Add a job to the redis queue."""
        jid = generate_jid()
        job_dict = instantiate_job(jid, status, start, end)
        save_job(......)
        queue_job(......)
        return job_dict

    @<...>   # fill in
    def execute_job(jid):
        """
        Retrieve a job id from the task queue and execute the job.
        Monitors the job to completion and updates the database accordingly. 
        """
        # fill in ...
        # the basic steps are: 
        # 1) get job id from message and update job status to indicate that the job has started
        # 2) start the analysis job and monitor it to completion. 
        # 3) update the job status to indicate that the job has finished. 

    rd = redis.StrictRedis(host='172.17.0.1', port=6379, db=0)

    def update_job_status(jid, status):
        """Update the status of job with job id `jid` to status `status`."""
        job = get_job_by_id(jid)
        if job:
            job['status'] = status
            save_job(generate_job_key(jid), job)
        else:
            raise Exception()


*Solution.* We start by recognizing that ``app = Flask(__name__)`` is the instantiation of a Flask app, the ``@app.route``
is a flask decorator for defining an endpoint in the API, and the ``app.run`` line is used to launch the flask server,
so we add those both in the ``api.py`` file:

.. code-block:: python

  # api.py

    app = Flask(__name__)

    @app.route('/jobs', methods=['POST'])
    def jobs_api():
        """
        API route for creating a new job to do some analysis. This route accepts a JSON payload 
        describing the job to be created. 
        """
        try:
            job = request.get_json(force=True)
        except Exception as e:
            return True, json.dumps({'status': "Error", 'message': 'Invalid JSON: {}.'.format(e)})
        return json.dumps(jobs.add_job(job['start'], job['end']))

    if __name__ == '__main__':
        app.run(debug=True, host='0.0.0.0')

We also recognize that several functions appear to be jobs-related:

  * ``generate_jid``
  * ``generate_job_key``
  * ``instantiate_job``
  * ``save_job``
  * ``queue_job``
  * ``add_job``
  * ``execute_job``
  * ``update_job_status``

Note that the ``jobs_api()`` function, which we just put in ``api.py``, actually references 
``jobs.add_job``, so we can put ``add_job`` in the ``jobs.py`` file as a public function, and 
anything that it calls can be added to ``jobs.py`` as a (potentially private) function. Note
that ``add_job`` calls the following functions:

  * ``generate_jid``
  * ``instantiate_job``
  * ``save_job``
  * ``queue_job``

so we can put all of these in jobs.py:


.. code-block:: python

  # jobs.py
    def generate_jid():
        """
        Generate a pseudo-random identifier for a job.
        """
        return str(uuid.uuid4())

    def instantiate_job(jid, status, start, end):
        """
        Create the job object description as a python dictionary. Requires the job id, status, 
        start and end parameters.
        """
        if type(jid) == str:
            return {'id': jid,
                    'status': status,
                    'start': start,
                    'end': end
            }
        return {'id': jid.decode('utf-8'),
                'status': status.decode('utf-8'),
                'start': start.decode('utf-8'),
                'end': end.decode('utf-8')
        }

    def save_job(job_key, job_dict):
        """Save a job object in the Redis database."""
        rd.hset(.......)

    def queue_job(jid):
        """Add a job to the redis queue."""
        ....

    def add_job(start, end, status="submitted"):
        """Add a job to the redis queue."""
        jid = _generate_jid()
        job_dict = instantiate_job(jid, status, start, end)
        save_job(......)
        queue_job(......)
        return job_dict

That leaves the following:

  * ``q = HotQueue(..)`` 
  * ``rd = StrictRedis(..)``
  * ``update_job_status()``
  * ``generate_job_key``
  * ``execute_job()``

Consider that:

  * We know ``worker.py`` is responsible for actually executing the job, so ``execute_job`` should go there.
  * The ``update_job_status()`` is a jobs-related task, so it goes in the ``jobs.py`` file -- it also makes a call to
    ``instantiate_job`` which is already in ``jobs.py``.
  * The jobs.py file definitely needs access to the ``rd`` object so that goes there.
  * Lastly, the ``q`` will be needed by both ``jobs.py`` and ``worker.py``, but ``worker.py`` is
    already importing from ``jobs``, so we better put it in ``jobs.py`` as well.

Therefore, the final placement of all the functions looks like the following: 

.. code-block:: python

  # api.py

    app = Flask(__name__)

    @app.route('/jobs', methods=['POST'])    
    def jobs_api():
        """
        API route for creating a new job to do some analysis. This route accepts a JSON payload
        describing the job to be created.
        """
        try:
            job = request.get_json(force=True)
        except Exception as e:
            return True, json.dumps({'status': "Error", 'message': 'Invalid JSON: {}.'.format(e)})
        return json.dumps(jobs.add_job(job['start'], job['end']))

    if __name__ == '__main__':
        app.run(debug=True, host='0.0.0.0')


.. code-block:: python

  # jobs.py
    q = HotQueue("queue", host='172.17.0.1', port=6379, db=1)
    rd = redis.StrictRedis(host='172.17.0.1', port=6379, db=0)

    def generate_jid():
        """
        Generate a pseudo-random identifier for a job.
        """
        return str(uuid.uuid4())

    def generate_job_key(jid):
    """
    Generate the redis key from the job id to be used when storing, retrieving or updating
    a job in the database.
    """
    return 'job.{}'.format(jid)

    def instantiate_job(jid, status, start, end):
        """
        Create the job object description as a python dictionary. Requires the job id, status, 
        start and end parameters.
        """
        if type(jid) == str:
            return {'id': jid,
                    'status': status,
                    'start': start,
                    'end': end
            }
        return {'id': jid.decode('utf-8'),
                'status': status.decode('utf-8'),
                'start': start.decode('utf-8'),
                'end': end.decode('utf-8')
        }

    def save_job(job_key, job_dict):
        """Save a job object in the Redis database."""
        rd.hset(.......)

    def queue_job(jid):
        """Add a job to the redis queue."""
        ....

    def add_job(start, end, status="submitted"):
        """Add a job to the redis queue."""
        jid = _generate_jid()
        job_dict = instantiate_job(jid, status, start, end)
        save_job(......)
        queue_job(......)
        return job_dict

    def update_job_status(jid, status):
        """Update the status of job with job id `jid` to status `status`."""
        job = get_job_by_id(jid)
        if job:
            job['status'] = status
            _save_job(_generate_job_key(jid), job)
        else:
            raise Exception()

.. code-block:: python

  # worker.py
    @<...>   # fill in
    def execute_job(jid):
        """
        Retrieve a job id from the task queue and execute the job.
        Monitors the job to completion and updates the database accordingly.
        """    
        # fill in ...
        # the basic steps are: 
        # 1) get job id from message and update job status to indicate that the job has started
        # 2) start the analysis job and monitor it to completion. 
        # 3) update the job status to indicate that the job has finished. 

Now that we have placed all of the functions, we can determine which ones should be public and which
ones should be private. In general, we want to limit the number of public functions we have. Public 
functions represent an API for other modules, and the larger the API, the more difficult it will be 
to make changes in the future. 

To this end, we need to determine which functions are called from external modules and which ones 
are only used locally. We see that ``add_job`` is called from the ``jobs_api`` function in ``api.py``,
so ``add_job`` should be public. Additionally, while the code isn't explicitly provided, it is clear 
that the ``execute_job`` function will need to call ``update_job_status``, so that should be public 
as well. All the other jobs functions are only used internally and can be made private. 


**Exercise.** After placing the functions in the correct files, add the necessary ``import`` statements.

*Solution.* Let's start with ``api.py``. We know we need to import the ``Flask`` class to create the ``app`` object and to
use the flask ``request`` object. We also use the ``json`` package from the standard library. Finally, we are using our
own ``jobs`` module.

.. code-block:: python

    # api.py
    import json
    from flask import Flask, request
    import jobs

    # rest of the code same as above...

For ``jobs.py``, there is nothing from our own code to import (which is good since the other modules will be importing
from it, but we do need to import the ``StrictRedis`` and ``HotQueue`` classes. Also, don't forget the use of the
``uuid`` module from the standard lib! So, ``jobs.py`` becomes:

.. code-block:: python

    # jobs.py
    import uuid
    from hotqueue import HotQueue
    from redis import StrictRedis

    # rest of the code same as above...


Finally, on the surface it doesn't appear that the worker needs to import anything, but we know it needs the ``q``
object to get items. It's hidden by the missing decorator. Let's go ahead and import it:

.. code-block:: python

    # worker.py
    from jobs import q

    # rest of the code same as above...


**Exercise.** Write code to finish the implementations for ``_save_job`` and ``_queue_job``.

*Solution.* The ``_save_job`` function should save the job to the database, while the ``_queue_job`` function
should put it on the queue. We know how to write those:

.. code-block:: python

    def _save_job(job_key, job_dict):
        """Save a job object in the Redis database."""
        rd.hset(job_key, mapping=job_dict)

    def _queue_job(jid):
        """Add a job to the redis queue."""
        q.put(jid)


**Exercise.** Fix the calls to ``_save_job`` and ``execute_job`` within the ``add_job`` function.
*Solution.* The issue in each of these are the missing parameters. The ``_save_job`` takes ``job_key, job_dict``, so
we just need to pass those in. Similarly, ``_queue_job`` takes ``jid``, so we pass that in. The ``add_job`` function
thus becomes:

.. code-block:: python

    def add_job(start, end, status="submitted"):
        """Add a job to the redis queue."""
        jid = _generate_jid()
        job_dict = _instantiate_job(jid, status, start, end)
        # update call to save_job:
        save_job(_generate_job_key(jid), job_dict)
        # update call to queue_job:
        queue_job(jid)
        return job_dict


**Exercise.** Finish the ``execute_job`` function. This function needs a decorator (which one?)
and it needs a function body.

The function body needs to:

  * update the status at the start (to something like "in progress").
  * update the status when finished (to something like "complete").

For the body, we will use the following (incomplete) simplification:

.. code-block:: python

    update_job_status(jid, .....)
    # todo -- replace with real job.
    time.sleep(15)
    update_job_status(jid, .....)

*Solution.*
As discussed before, we saw in class we can use the ``q.worker`` decorator to turn the worker into a consumer.

As for ``execute_job`` itself, we are given the body, we just need to fix the calls to the ``update_job_status()``
function. The first call puts the job "in progress" while the second sets it to "complete". So the function becomes:

.. code-block:: python

    @<...>   # fill in
    def execute_job(jid):
        update_job_status(jid, "in progress")
        time.sleep(15)
        update_job_status(jid, "complete")

Note that we are using the ``update_job_status`` function from ``jobs.py`` now, so we need to import it.
The final ``worker.py`` is thus:

.. code-block:: python

    from jobs import q, update_job_status

    @q.worker
    def execute_job(jid):
        jobs.update_job_status(jid, 'in progress')
        time.sleep(15)
        jobs.update_job_status(jid, 'complete')
