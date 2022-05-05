Docker Compose, Revisited
=========================

Let's do a bit of rehash: docker-compose is a tool for defining and running
multi-container Docker applications. With docker-compose, you use a YAML file to
configure your application's services. Then, with a single command, you create
and start all the services from your configuration.

Here, we will revisit our containerized Flask and Redis services, fire them up
with docker-compose, and then have our two services talk to each other.

When working with multiple containers, it can be difficult to manage the
starting configuration along with the variables and links. Docker-compose is one
orchestration tool for defining and running multi-container Docker applications.


Why Docker-Compose?
-------------------

*  Orchestration!
*  Launch multiple containers with complex configurations at once
*  Define the structure of your app, not the commands needed to run it!


Using Docker-Compose
--------------------

Using docker-compose is a three-step process:

* Define images with Dockerfiles
* Define the services in a docker-compose.yml as containers with all of your
  options (e.g. image, port mapping, links, etc.)
* Run ``docker-compose up`` and it starts and runs your entire app

Three step process to use ... a bit more to actually build.


Orchestrating Redis
-------------------

The first thing to do is create a new Docker build context for our app - this is
the collection of files and folders that goes in to the image(s) we build. Create
a folder called ``redis-docker`` and directories called ``config`` and ``data``
within:

.. code-block:: console

   [isp02]$ mkdir redis-docker/
   [isp02]$ mkdir redis-docker/config
   [isp02]$ mkdir redis-docker/data


Next copy `this redis config file <https://raw.githubusercontent.com/TACC/coe-332-sp21/main/docs/week09/redis.conf>`_
into your config directory. We are going to put this into our Redis container,
and it will allow us to customize the behavior of our database server, if
desired.

.. code-block:: console

   [isp02]$ cd redis-docker
   [isp02]$ wget -O config/redis.conf https://raw.githubusercontent.com/TACC/coe-332-sp21/main/docs/week09/redis.conf
   [isp02]$ ls config/
   redis.conf


Now in your top directory, create a new file called ``docker-compose.yml``.
Populate the file with the following contents, being careful to preserve
indentation, and replacing the username / port placeholders with your own:

.. code-block:: yaml

    ---
    version: '3'
    services:
        redis:
            image: redis:latest
            container_name: <your username>-redis
            ports:
                - <your redis port>:6379
            volumes:
                - ./config/redis.conf:/redis.conf
            command: [ "redis-server", "/redis.conf" ]



Start the Redis Service
-----------------------

Bring up your Redis container with the following command:

.. code-block:: console

   [isp02]$ docker-compose -p <your username> up -d

Take note of the following options:

* ``docker-compose up`` looks for docker-compose.yml and starts the services
  described within
* ``-p <your username>`` gives the project a unique name, which will help avoid
  collisions with other student's containers
* ``-d`` puts it in daemon mode (runs in the background)

Check to see if your Redis database is up and the port is forwarding as you
expect with the following:

.. code-block:: console

   [isp02]$ docker ps
   CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS           PORTS                    NAMES
   aa1b2b6908a9   redis:5.0.0      "docker-entrypoint.s…"   58 seconds ago   Up 55 seconds    0.0.0.0:6080->6379/tcp   charlie-redis

   [isp02]$ docker logs aa1b2b6908a9
   1:C 31 Mar 2021 20:14:45.615 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
   1:C 31 Mar 2021 20:14:45.615 # Redis version=6.2.1, bits=64, commit=00000000, modified=0, pid=1, just started
   1:C 31 Mar 2021 20:14:45.615 # Configuration loaded
   1:M 31 Mar 2021 20:14:45.618 * monotonic clock: POSIX clock_gettime
                   _._
              _.-``__ ''-._
         _.-``    `.  `_.  ''-._           Redis 6.2.1 (00000000/0) 64 bit
     .-`` .-```.  ```\/    _.,_ ''-._
    (    '      ,       .-`  | `,    )     Running in standalone mode
    |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6080
    |    `-._   `._    /     _.-'    |     PID: 1
     `-._    `-._  `-./  _.-'    _.-'
    |`-._`-._    `-.__.-'    _.-'_.-'|
    |    `-._`-._        _.-'_.-'    |           http://redis.io
     `-._    `-._`-.__.-'_.-'    _.-'
    |`-._`-._    `-.__.-'    _.-'_.-'|
    |    `-._`-._        _.-'_.-'    |
     `-._    `-._`-.__.-'_.-'    _.-'
         `-._    `-.__.-'    _.-'
             `-._        _.-'
                 `-.__.-'

   1:M 31 Mar 2021 20:14:45.623 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
   1:M 31 Mar 2021 20:14:45.623 # Server initialized
   1:M 31 Mar 2021 20:14:45.623 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
   1:M 31 Mar 2021 20:14:45.625 * Ready to accept connections


Boom! We have Redis running!

But Charlie! *docker-compose is about defining and running multi-container
Docker applications!*


Add the Flask Service
---------------------

First let's take down the existing service:

.. code-block:: console

   [isp02]$ docker-compose -p <your username> down
   Stopping charlie-redis ... done
   Removing charlie-redis ... done
   Removing network charlie_default

.. note::

   It is assumed you are still in the same directory as your docker-compose.yml
   file, if not otherwise specified.


Next, add the following new lines to your docker-compose.yml file:

.. code-block:: yaml
   :emphasize-lines: 12-18

    ---
    version: '3'
    services:
        redis:
            image: redis:latest
            container_name: <your username>-redis
            ports:
                - <your redis port>:6379
            volumes:
                - ./config/redis.conf:/redis.conf
            command: [ "redis-server", "/redis.conf" ]
        web:
           build: .
           container_name: <your-username>-web
           ports:
              - <your flask port>:5000
           volumes:
              - ./data/data_file.json:/datafile.json


With these lines, you are adding a new service called 'web'. Take care to replace
the placeholders with your assigned Redis port and Flask port numbers. Note Redis
and Flask use default ports 6379 and 5000, respectively, inside the containers
unless otherwise specified.

Also new to this service, we are using the ``build`` key to build a new Docker
image based on the files / Dockerfile in this (``.``) directory. We need to pull
in our web assets (wherever they are located - it may be different for each
person) and Dockerfile from our previous exercises to this current directory.

.. code-block:: console

   [isp02]$ mkdir web
   [isp02]$ cp ~/coe-332/web1/app.py ./web/
   [isp02]$ cp ~/coe-332/web1/requirements.txt ./
   [isp02]$ cp ~/coe-332/web1/data_file.json ./data/
   [isp02]$ cp ~/coe-332/web1/Dockerfile ./

   # Now your directory structure should look like:
   [isp02]$ tree .
   .
   ├── config
   │   └── redis.conf
   ├── data
   │   └── data_file.json
   ├── docker-compose.yml
   ├── Dockerfile
   └── web
       ├── app.py
       └── requirements.txt


This time when you start services, two containers will be created, one of which
is built from the current directory.

.. code-block:: console

   [isp02]$ docker-compose -p charlie up -d
   Creating network "charlie_default" with the default driver
   Creating charlie-redis ... done
   Creating charlie-web   ... done



Modify Python Redis Client
--------------------------

When you do ``docker-compose up``, behind the scenes Docker creates a custom
bridge network for each of your services to talk to one another. They can reach
each other using the name of the service as the 'host', e.g.:

.. code-block:: python3

   >>> rd = redis.StrictRedis(host='redis', port=6379, db=0)



Exercise
~~~~~~~~

Connect your Flask container and your Redis container together using
docker-compose, and curl the various endpoints to make sure it works.

.. note::

   Be sure to change your Redis connection in your Flask App!
