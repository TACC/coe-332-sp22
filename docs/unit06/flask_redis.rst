Flask and Redis
===============

Up to this point, we have been interacting with a shared Redis database instance
directly on the class ISP server. Next, we will each containerize an instance of
Redis, figure out how to interact with it by forwarding the port, and connect it
to our Flask app. After going through this module, students should be able to:

  * Start a Redis container, connecting the appropriate inside port to a port on isp02.
  * Mount a host volume on isp02 inside the Redis container to save data between restarts of Redis itself.
  * Connect to the container from within a Flask application.


Start a Redis Container
-----------------------

Docker Hub has a wealth of official, public images. It is a good idea to pull
the existing Redis image rather than build it ourself, because it has all of the
functionality we need as a base image.

Pull the official Redis image for version 6:

.. code-block:: console

   [isp02]$ docker pull redis:6   
    6: Pulling from library/redis
    ae13dd578326: Pull complete 
    e6f25d21ebb3: Pull complete 
    601cc6106ba1: Pull complete 
    5b8be2fd806e: Pull complete 
    950c3791111a: Pull complete 
    567b7ad78092: Pull complete 
    Digest: sha256:81b50efa808d72f7965d4deecea18e42cab2fec25fafca447eb4bda615b9c8e4
    Status: Downloaded newer image for redis:6
    docker.io/library/redis:6

Start the Redis server in a container:

.. code-block:: console

   [isp02]$ docker run -p <your-redis-port>:6379 --name=<your_name>-redis redis:6
   1:C 31 Mar 2021 16:48:11.939 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
   ...
   1:M 31 Mar 2021 16:48:11.972 * Ready to accept connections


The above command will start the Redis container in the foreground which can be
helpful for debugging, seeing logs, etc. However, you will have the need to
start it in the background (detached or daemon mode). You can do that by adding the 
``-d`` flag:

.. code-block:: console

   [isp02]$ docker run -d -p <your-redis-port>:6379 --name=<your_name>-redis redis:6
   3a28cb265d5e09747c64a87f904f8184bd8105270b8a765e1e82f0fe0db82a9e

At this point, Redis is running and available from the isp02 host, but let's explore the persistence 
of the data in Redis.

We can open a Python shell, connect to our Redis container, and add some data:

.. code-block:: python3

    >>> import redis
    >>> rd = redis.Redis(host='127.0.0.1', port=<your_redis_port>, db=0)

    # create some data
    >>> rd.set('k', 'v')
    True

    # check that we can read the data
    >>> rd.get('k')
    b'v'

If we exit our Python shell and then go into a new Python shell and connect to Redis, what do we see?

.. code-block:: python3

    >>> import redis
    >>> rd = redis.Redis(host='127.0.0.1', port=<your_redis_port>, db=0)

    # previous data is still there
    >>> rd.get('k')
    b'v'

Great! Redis has persisted our data across Python sessions. But what happens if we shut down the
Redis container itself?

Let's find out by killing our Redis container and starting a new one.

.. code-block:: console

   # shut down the existing redis container using the name you gave it 
   [isp02]$ docker rm -f <your_name>-redis 

   # start a new redis container
   [isp02]$ docker run -d -p <your-redis-port>:6379 --name=<your_name>-redis redis:6


Now go back into the Python shell and connect to Redis:

.. code-block:: python3

    >>> import redis
    >>> rd = redis.Redis(host='127.0.0.1', port=<your_redis_port>, db=0)

    # previous data is gone!
    >>> rd.get('k')

    # no keys at all!
    >>> rd.keys()
    []
    
Oops! All the data that was in Redis is gone. The problem is we are not permanently persisting 
the Redis data across different Redis containers. But wasn't that the whole point of using a 
database? Are we just back to where we started?

Actually, we only need two small changes to the way we are running the Redis container to make the 
Redis data persist across container executions. 

Container Bind Mounts
---------------------
A container bind mount (or just "mount" for short) is a way of replacing a file or directory in a 
container image with a file or directory on the host file system in a running container.

Bind mounts are specified with the ``-v`` flag to the ``docker run`` statement. The full syntax 
is 

.. code-block:: console

    [isp02]$ docker run -v <host_path>:<container_path>:<mode> ...*additional docker run args*...

where: 
  * ``<host_path>`` and ``<container_path>`` are absolute paths in the host (respectively, container)
    file system and 
  * ``<mode>`` can take the value of ``ro`` for a read-only mount and ``rw`` for a read-write mount.

Note that ``mode`` is optional and defaults to read-write.

It is important to keep the following in mind when using bind mounts:

  * If the container image originally contained a file or directory at the ``<container_path>`` 
    these will be replaced entirely by the contents of ``<host_path>``.
  * If the container image did not container contents at ``<container_path>`` the mount will still 
    succeed and simply create a new file/directory at the path.
  * If the ``<mode>`` is read-write (the default), any changes made by the running container will be 
    reflected on the host file system. Note that the process running in the container still must have
    permission to write to the path. 
  * If ``<host_path>`` does not exist on the host, Docker will create a **directory** at the path 
    and mount it into the container. **This may or may not be what you want.** 


Persisting Data Across Redis Containers
----------------------------------------

We can use bind mounts to persist Redis data across container executions: the key point is that Redis 
can be started in a mode so that it periodically writes all of its data to the host.

From the Redis documentation, we see that we need to set the ``--save`` flag when starting Redis so 
that it writes its dataset to the file system periodically. The full syntax is:

.. code-block:: console

  --save <frequency> <number_of_backups>

where ``<frequency>`` is an integer, in seconds. We'll instruct Redis to write its data to the file 
system every second, and we'll keep just one backup. 

With ``save``, we can instruct Redis to write the data to the file system, but we still need to 
save the files across container executions. That's where the bind mount comes in. But how do we know 
which directory to mount into Redis? Fortunately, the Redis documentation tells us what we need to know:
Redis writes data to the ``/data`` directory in the container. 

Putting all of this together, we can update the way we run our Redis container as follows:


.. code-block:: console

   [isp02]$ docker run -d -p <your-redis-port>:6379 -v </path/on/host>:/data --name=<your_name>-redis redis:6 --save 1 1 

.. tip::

  You can use the ``($pwd)`` shortcut for the present working directory. 

For example, I might use:

.. code-block:: console

  [isp02]$ docker run -d -p 6379:6379 -v $(pwd)/data:/data:rw --name=joe-redis redis:6 --save 1 1 

Now, Redis should periodically write all of its state to the ``data`` directory. You should see a 
file called ``dump.rdb`` in the directory because we are using the default persistence mechanism 
for Redis. This will suffice for our purposes, but Redis has other options for persistence which 
you can read about `here <https://redis.io/topics/persistence>`_ if interested. 


Exercise 1
~~~~~~~~~~

Test out persistence of your Redis data across Redis container restarts by starting a new Redis container 
using the method above, saving some data to it in a Python shell, shutting down the Redis container 
and starting a new one, and verifying back in the Python shell that the original data is still there. 


Using Redis in Flask
--------------------
Using Redis in our Flask apps is identical to using it in the Python shells that we have been using 
to explore with. We simply create a Python Redis client object using the ``redis.Redis()`` constructor.
Since we might want to use Redis from different parts of the code, we'll create a function for 
generating the client:

.. code-block:: python3

     def get_redis_client():
         return redis.Redis(host='127.0.0.1', port=<your_port>, db=0)


Exercise 2
~~~~~~~~~~

In the last module, we wrote program to read the Meteorite Landings data (i.e., the 
``Meteorite_Landings.json`` file from Unit 4) into Redis. In this exercise, let's turn this program 
into a Flask API with one route that handles both a POST and a GET. 

  * Use ``/data`` as the URL path for the one route.
  * A POST request to ``/data`` should load the Meteorite Landings data into Redis.
  * A GET request to ``/data`` should read the data out of Redis and return it as a JSON list. 

For bonus points, implement an optional ``start`` query parameter that takes an integer and returns the 
Meteorite landing data starting at the ``start`` index. Make sure to handle the case where ``start`` 
is provided but is not an integer! 