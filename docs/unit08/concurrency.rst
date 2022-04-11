Concurrency and Queues
======================

We begin with an introduction to concurrency and a basic treatment of the queue data structure.
By the end of this the module, the student should be able to:

  * Describe concurrency and some basic examples of concurrent and nonconcurrent algorithms.
  * Explain at a high level how concurrency will be used to implement a long-running task in our 
    flask-based API system.
  * Utilize Python in-memory queues as well as the hotqueue library to work with queues in Redis.


Motivation
----------

Our Flask API is useful because it can return information about objects in our database, and in general, looking up
or storing objects in the database is a very "fast" operation, on the order of a few 10s of milliseconds. However,
many interesting and useful operations are not nearly as quick to perform. They are many examples from both research
computing and industrial computing where the computations take much longer; for example, on the order of minutes, hours,
days or even longer.

Examples include:

  * Executing a large mathematical model simulating a major weather event such as a hurricane, 
    or an astronomical process such as galaxy formation
  * Aligning a set of genomic sequence fragments to a reference genome
  * Running the payroll program at the end of the month to send checks to all employees of a large enterprise.
  * Sending a "welcome back" email to every student enrolled at the university at the start of the semester.

We want to be able to add functionality like this to our API system. We'd like to provide a new API endpoint where a user
could describe some kind of long-running computation to be performed and have our system perform it for them. But there
are a few issues:

  * The HTTP protocol was not built for long-running tasks, and most programs utilizing HTTP expect responses "soon", on
    the order of a few seconds. Many programs have hard timeouts around 30 or 60 seconds.
  * The networks on which HTTP connections are built can be interrupted (even just briefly) over long periods of time. If
    a connection is severed, even for a few milliseconds, what happens to the long-running computation?
  * Long-running tasks like the ones above can be computationally intensive and require a lot of computing resources. If our
    system becomes popular (even with a single, enthusiastic user), we may not be able to keep up with demand. We need
    to be able to throttle the number of computations we do.

To address these challenges, we will implement a "Jobs API" as an *asynchronous* endpoint. Over the next few lectures,
we will spell out precisely what this means, but for now, we'll give a quick high-level overview as motivation. Don't
worry about understanding all the details here.


Jobs API -- An Introduction
---------------------------

The basic idea is that we will have a new endpoint in our API at a path ``/jobs`` (or something similar). A user wanting
to have our system perform a long-running task will create a new job. We will use RESTful semantics, so the user will
create a new job by making an HTTP POST request to ``/jobs``, describing the job in the POST message body (in JSON).

However, instead of performing the actual computation, the Jobs API will simply record that the user has requested
such a computation. It will store that in Redis and immediately respond to the user. So, the response will not include
the result of the job itself but instead it will indicate that the request has been received and it will be worked on
in due time. Also, and critically, it will provide an ``id`` for the job that the user can use to check the status later
and, eventually, get the actual result.

So, in summary:

  1. User makes an HTTP POST to ``/jobs`` to create a job.
  2. Jobs API validates that the job is a valid job, creates an ``id`` for it, and stores the job description with the
     ``id`` in Redis.
  3. Jobs API responds to the user immediately with the ``id`` of the job it generated.
  4. In the background, *some other python program* we write (referred to as a "worker") will, at some point in the
     future, actually start the job and monitor it to completion.

This illustrates the *asynchronous* and *concurrent* nature of our Jobs API, terms we will define precisely in the
sequel. Intuitively, you can probably already imagine what we mean here -- multiple jobs can be worked on at the same
time by different instances of our program (i.e., different workers), and the computation happens asynchronously from
the original user's request.



Concurrency and Queues
----------------------

A computer system is said to be *concurrent* if multiple agents or components of the system can be in progress at the
same time without impacting the correctness of the system.

While components of the system are in progress at the same time, the individual operations themselves may happen
sequentially. In general, a system being concurrent means that the different components can be executed at the same time
or in different orders without impacting the overall correctness of the system.

There are many techniques for making programs concurrent; we will primarily focus on a technique that leverages the
*queue* data structure. But first, an example.


A First Example
***************

Suppose we want to build a system for maintaining the balance of a bank account where multiple agents are acting on the account
(withdrawing and/or depositing funds) at the same time. We will consider two different approaches.

**Approach 1.** Whenever an agent receives an order to make a deposit or withdraw, the agent does the following steps:

  1. Makes a query to determine the current balance.
  2. Computes the new balance based on the deposit or withdraw amount.
  3. Makes a query to update the balance to the computed amount.

This approach is not concurrent because the individual operations of different agents cannot be reordered.

For example, suppose we have:

  * Two agents, agent A and agent B, and a starting balance of $50.
  * Agent A gets an order to deposit $25 at the same time that agent B gets an order to withdraw $10.


In this case, the final balance should be $65 (=$50 + $25 - $10).

The system will arrive at this answer as long as steps 1, 2 and 3 for one agent are done before any steps for
the other agent are started; for ex, A1, A2, A3, B1, B2, B3.

However, if the steps of the two agents are mixed then the system will
not arrive at the correct answer.

For example, suppose the steps of the two agents were performed in this order: A1, A2, B1, B2, A3, B3.
What would the final result be? The listing below shows what each agents sees at each step.

  * A1. Agent A determines the current balance to be $50.
  * A2. Agent A computes a new balance of $50 + $25 = $75.
  * B1. Agent B determines the current balance to be $50.
  * B2. Agent B computes a new balance of $50 - $10 = $40.
  * A3. Agent A updates the balance to be $75.
  * B3. Agent B updates the balance to be $40.

In this case, the system will compute the final balance to be $40! Hopefully this is not your account! :)


We will explore an alternative approach that is concurrent, but to do that we first need to introduce the concept of
a queue.


Queues
******

A queue is data structure that maintains an ordered collection of items. The queue typically supports just two
operations:

  * Enqueue (aka "put") - add a new item to the queue.
  * Dequeue (aka "get") - remove an item from the queue.

Items are removed from a queue in First-In-First-Out (FIFO) fashion: that is, the item removed from the first dequeue
operation will be the first item added to the queue, the item removed from the second dequeue operation will be the
second item added to the queue, and so on.

Sometimes queues are referred to as "FIFO Queues" for emphasis.


Basic Queue Example
^^^^^^^^^^^^^^^^^^^
Consider the set of (abstract) operations on a Queue object.

.. code-block:: bash

  1. Enqueue 5
  2. Enqueue 7
  3. Enqueue A
  4. Dequeue
  5. Enqueue 1
  6. Enqueue 4
  7. Dequeue
  8. Dequeue

The order of items returned is:

.. code-block:: bash

  5, 7, A

And the contents of the Queue after Step 8 is

.. code-block:: bash

  1, 4


In-memory Python Queues
^^^^^^^^^^^^^^^^^^^^^^^

The Python standard library provides an in-memory Queue data structure via its ``queue`` module. To get started, import the
``queue`` module and instantiate a ``queue.Queue`` object:


.. code-block:: python

  >>> import queue
  >>> q = queue.Queue()

The Python Queue object has the following features:

  * The ``q`` object supports ``.put()`` and ``.get()`` to put a new item on the queue, and get an item off
    the queue, respectively
  * ``q.put()`` can take an arbitrary Python object and ``q.get()`` returns a Python object from the queue.


Let's perform the operations above using the ``q`` object.


**Exercise.** Use a series of ``q.put()`` and ``q.get()`` calls to perform Steps 1-8 above. Verify the the order of items returned.

**Exercise.** Verify that arbitrary Python objects can by put onto and retrieved from the queue by inserting a list and a
dictionary.

Queues are a fundamental ingredient in concurrent programming, a topic we will turn to next.


A Concurrent Approach to Our Example
************************************

**Approach 2.** Whenever an agent receives an order to make a withdraw or deposit, the agent simply writes the
order to a queue; a positive number indicates a deposit while a negative number indicates a withdraw. The account
system keeps a running "balancer" agent whose only job is to read items off the queue and update the balance.

This approach is concurrent because the order of the agents' steps can be mixed without impacting the overall result.
This fact essentially comes down to the commutativity of addition and subtraction operations: i.e., ``50 + 25 - 10 = 50 - 10 + 25``.

Note that the queue of orders could be generalized to a "queue of tasks" (transfer some amount from account A to account B,
close account C, etc.).


Queues in Redis
***************

The Python in-memory queues are very useful for a single Python program, but we ultimately want to share queues across
multiple Python programs/containers.

The Redis DB we have been using can also be used to provide a queue data structure for clients running in different
containers. The basic idea is:

  * Use a Redis list data structure to hold the items in the queue.
  * Use the Redis list operations ``rpush``, ``lpop``, ``llen``, etc. to create a queue data structure.

For example:

  * ``rpush`` will add an element to the end of the list.
  * ``lpop`` will return an element from the front of the list, and return nothing if the list is empty.
  * ``llen`` will return the number of elements in the list.


Fortunately, we don't have to implement the queue ourselves, but know that if we needed to we could without too much effort.


Using the hotqueue library
**************************

We will leverage a small, open source Python library called ``hotqueue`` which has already implemented the a Queue
data structure in Redis using the approach outlined above. Besides not having to write it ourselves, the use of ``hotqueue``
will afford us a few additional features which we will look at later.

Here are the basics of the ``hotqueue`` library:

  * Hotqueue is not part of the Python standard library; you can install it with ``pip install hotqueue``
  * Creating a new queue data structure or connecting to an existing queue data structure is accomplished by creating
    a ``HotQueue`` object.
  * Constructing a ``HotQueue`` object takes very similar parameters to that of the ``StrictRedis`` but also takes a
    ``name`` attribute. The ``HotQueue`` object ultimately provides a connection to the Redis server.
  * Once constructed, a ``HotQueue`` object has ``.put()`` and ``.get()`` methods that act just like the corresponding
    methods of an in-memory Python queue.


A Hotqueue Example
^^^^^^^^^^^^^^^^^^

We will work this example out on the k8s cluster. You will need a Redis pod running on the cluster and you will also
need the python debug pod you created last lecture.

If you prefer, you can create a new deployment that uses the ``jstubbs/redis-client`` image with the required libraries
already installed installed using the following code --

.. code-block:: yaml

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: redis-client-debug-deployment
      labels:
        app: redis-client-debug
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: redis-client-debug
      template:
        metadata:
          labels:
            app: redis-client-debug
        spec:
          containers:
            - name: py39
              image: jstubbs/redis-client
              command: ['sleep', '999999999']

With your debug pod running, first, exec into it and install ``redis`` and ``hotqueue``.
You can optionally also install ``ipython`` which is a nicer Python REPL (Read, Evaluate, Print Loop).

.. note::

  The ``jstubbs/redis-client`` image has these libraries already installed.


.. code-block:: bash

  $ kubectl get pods -o wide
    NAME                                    READY   STATUS    RESTARTS   AGE    IP             NODE   NOMINATED NODE   READINESS GATES
    hello                                   1/1     Running   199        8d     10.244.5.214   c04    <none>           <none>
    hello-deployment-55f4459bf-npdrm        1/1     Running   79         3d7h   10.244.5.5     c04    <none>           <none>
    hello-pvc-deployment-6dbbfdc4b4-whjwb   1/1     Running   31         31h    10.244.3.143   c01    <none>           <none>
    helloflask-848c4fb54f-9j4fd             1/1     Running   0          30h    10.244.3.188   c01    <none>           <none>
    helloflask-848c4fb54f-gpqhb             1/1     Running   0          30h    10.244.5.75    c04    <none>           <none>
    jstubbs-test-redis-64cbc6b8cf-f6qrl     1/1     Running   0          3m5s   10.244.3.237   c01    <none>           <none>
    py-debug-deployment-5cc8cdd65f-tr9gq    1/1     Running   0          31h    10.244.3.177   c01    <none>           <none>

  $ kubectl exec -it py-debug-deployment-5cc8cdd65f-tr9gq -- /bin/bash

  $ pip install redis hotqueue ipython

Start the python (or ipython) shell and create the ``hotQueue.Queue`` object. You can use the Redis IP directly, or use
the Redis service IP if you creates one.

.. code-block:: python

    >>> from hotqueue import HotQueue
    >>> q = HotQueue("queue", host="<Redis_IP>", port=6379, db=1)

Note how similar the ``HotQueue()`` instantiation is to the ``StrictRedis`` instantiation. In the example above we named
the queue ``queue`` (not too creative), but it could have been anything.

.. note::

  In the definition above, we have set ``db=1`` to ensure we don't interfering with the main data of your Flask app.

Now we can add elements to the queue using the `.put()`; just like with in-memory Python queues, we can put any Python
object into the queue:

.. code-block:: python

  >>> q.put(1)
  >>> q.put('abc')
  >>> q.put(['1', 2, {'key': 'value'}, '4'])

We can check the number of items in queue at any time using the `len` built in:

.. code-block:: python

  >>> len(q)
  3

And we can remove an item with the `.get()` method; remember - the queue follows a FIFO principle:

.. code-block:: python

  >>> q.get()
  1
  >>> len(q)
  2
  >>> q.get()
  'abc'
  >>> len(q)
  1


Under the hood, the ``hotqueue.Queue`` is just a Redis object, which we can verify using a redis client:

.. code-block:: python

    >>> import redis
    >>> rd = redis.StrictRedis(host="<Redis IP>", port=6379, db=1)
    >>> rd.keys()
    [b'hotqueue:queue']

Note that the queue is just a single key in the Redis server ``(db=1)``.

And just like with other Redis data structures, we can connect to our queue from additional Python clients and see
the same data.


**Exercise.** In a second SSH shell, scale your Python debug deployment to 2 replicas, install redis, hotqueue, and
ipython in the new replica, start iPython and connect to the same queue. Prove that you can use get and put to
"communicate" between your two Python programs.

