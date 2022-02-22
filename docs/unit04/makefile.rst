Makefile Automation
===================

The development cycle for Docker images can necessitate some fairly complex and
long commands. It is easy to accidentally inject typos or forget part of the
command. To speed up development in Docker, we will learn some simple automation
techniques using Makefiles. The concepts learned here can be applied to many
other areas, not just Docker containers. After going through this module, students
should be able to:

* Write a Makefile for automating repetitive commands
* Execute specific targets in a Makefile
* Inject variables into a Makefile


Development Cycle
-----------------

We previously talked at great length about why it is a good idea to containerize
an app / service that you develop. As you write code, containerize, and test,
you will be repeating some common commands over and over again. The "development
cycle" in this case may follow the form:

1. Edit some source code (e.g. add a new function to your Python script)
2. Delete any running container with the old source code
3. Re-build the image with ``docker build``
4. Start up a new container with ``docker run``
5. Test the new code / functionality
6. Repeat

This 6-step cycle is great for iterating containers as you develop code. However,
the commands we type out as we develop are potential error sources - it is easy
to accidentally skip a step or inject typos.


Makefile
--------

Makefiles can be a useful automation tool for testing your services.
Many code projects use Makefiles to help with the compile and install process
(e.g. ``make && make install``). Here, we will set up a Makefile to help with the
6-step cycle above. Using certain keywords (called "targets") we will create
shortcuts to cleaning up running containers, re-building docker images, running
new containers, and pushing images to Docker Hub.

Targets are listed in a file called ``Makefile`` in this format:

.. code-block:: makefile

   target: prerequisite(s)
           recipe

Targets are short keywords, and recipes are shell commands. For example, a
simple target might look like:

.. code-block:: makefile

   images:
           docker -a | grep wjallen

Put this text in a file called ``Makefile`` in your current directory, and then
you simply need to type:

.. code-block:: console

    [isp02]$ make images

And that will list all the docker containers with the username 'jwallen' either
in the image name or the container name. Makefiles can be further abstracted with
variables to make them a little bit more flexible. Consider the following Makefile:

.. code-block:: makefile

   NAME ?= wjallen

   all: images ps

   images:
           docker images | grep ${NAME}

   ps:
           docker ps -a | grep ${NAME}

Here we have added a variable ``NAME`` at the top so we can easily customize the
targets below. We have also added two new targets: ``ps`` which lists running
containers, and ``all`` which does not contain any recipes, but does contain two
prerequisites - the other two targets. So these two are equivalent:

.. code-block:: console

   # make all targets
   [isp02]$ make all

   # or make them one-by-one
   [isp02]$ make images
   [isp02]$ make ps

   # Try this out:
   [isp02]$ NAME="bsd" make all




EXERCISE
~~~~~~~~

Write a Makefile that:

1. Builds your Docker image in your name space
2. Runs a test command or test script to confirm your image is working
3. Pushes your image to Docker Hub
