Development Environment
=======================

Development of the software systems for our final projects should be performed
on the ISP server. The containers built and run on ISP02 will be ephemeral - only
lasting long enough to test and debug new features. They should never be seen by
end users. After going through this module, students should be able to:

* Set up the files and folders necessary to work on the final project
* Describe each component and why they are important to the overall system
* Describe the general development cycle from a new idea all the way to production
* Write targets for the major steps into a Makefile



File Organization
-----------------

Our final software systems should have three main components:

1. A Flask API front end for accessing data and jobs functionality
2. A Redis database for storing job and queue information
3. A Worker back end which runs the analysis job


An example file organization scheme for developing this API may look like:

.. code-block:: text

    my-api/
    ├── data
    │   └── dump.rdb
    ├── docker
    │   ├── Dockerfile.api
    │   └── Dockerfile.wrk
    ├── Makefile
    ├── README.md
    └── src
        ├── api.py
        ├── jobs.py
        └── worker.py


In this example, the ``data/`` subfolder is mounted inside a Redis container. All
the data is captured in regular intervals in `dump.rdb` for testing purposes only
(real end users will not see this copy of the data).

The `docker/` subfolder contains a Dockerfile for each service. We don't need a
Dockerfile for the redis container because we will use the stock ``redis:6``.

The ``Makefile`` will be an essential part of our development cycle (revisited
below).

The ``src/`` folder contains the source Python scripts that are injected into the
API and worker containers. This is where the majority of the development will
occur.

.. tip::

   Make sure to put your folders under version control and commit regularly as
   you work. Don't save it all until the end.


QUESTIONS
~~~~~~~~~

* Is the data set I'm working with part of this repository? Why or why not?
* Should we commit ``dump.rdb`` to GitHub? Why or why not?
* I am not using a ``requirements.txt`` file above. Should I?
* I am using two Dockerfiles - one for the worker and one for the API. Should I
  be doing this or should I just have one Dockerfile?


Docker
------

We previously talked at great length about why it is a good idea to containerize
an app / service that you develop. One of the main reasons was for portability
to other machines. All the development / testing done in this development environment
will directly translate to our deployment environment (Kubernetes), as we will see
in the next module.

The development cycle for each of the three containerized components generally
follows the form:

1. Edit some source code (e.g. add a new Flask route)
2. Delete any running container with the old source code
3. Re-build the image with ``docker build``
4. Start up a new container with ``docker run``
5. Test the new code / functionality
6. Repeat

This 6-step cycle is great for iterating on the API and Worker containers
independently, or at the same time. However, watch out for potential error sources.
For example if you take down the Redis container, a worker container that is in
the middle of watching the queue may also go down and will need to be restarted
(once a new Redis container is up).


Makefile
--------

We previously introduced Makefiles as a useful automation tool for testing your
services. Now that our development cycle is becoming longer and more complicated,
it is worth revisiting. Here, we will set up a Makefile to help
with the 6-step cycle above. Using certain keywords (called "targets") we will
create shortcuts to cleaning up running containers, re-building docker images, and
running new containers.

Targets are listed in a file called ``Makefile`` in this format:

.. code-block:: text

   target: prerequisite(s)
           recipe

Targets are short keywords, and recipes are shell commands. For example, a
simple target might look like:

.. code-block:: text

   ps-me:
           docker ps -a | grep wallen

Put this text in a file called ``Makefile`` in your current directory, and then
you simply need to type:

.. code-block:: console

    [isp02]$ make ps-me

And that will list all the docker containers with the username 'wallen' either
in the image name or the container name. Makefiles can be further abstracted with
variables to make them a little bit more flexible. Consider the following Makefile:

.. code-block:: text

   NAME ?= wallen

   all: ps-me im-me

   im-me:
           docker images | grep ${NAME}

   ps-me:
           docker ps -a | grep ${NAME}

Here we have added a variable ``NAME`` at the top so we can easily customize the
targets below. We have also added two new targets: ``im-me`` which lists images,
and ``all`` which does not contain any recipes, but does contain two prerequisites -
the other two targets. So these two are equivalent:

.. code-block:: console

   # make all targets
   [isp02]$ make all

   # or make them one-by-one
   [isp02]$ make ps-me
   [isp02]$ make im-me

   # Try this out:
   [isp02]$ NAME="redis" make all


EXERCISE
~~~~~~~~

Write a Makefile that, at a minimum:

1. Builds all necessary images for your app from Dockerfile(s)
2. Starts up new containers / services
3. Removes running containers in your namespace (be careful!)
