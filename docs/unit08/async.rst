Deploying to k8s
================

In this lecture, we will bring everything together and deploy an updated version of our Flask API system to k8s
that includes a Jobs endpoint and a worker deployment. But first, we need to finish the worker.
At the end of this module, the student should be able to:

  * Complete the basic worker program functionality that can consume messages from the queue and 
    run an associated computation, storing metadata about the job in Redis.
  * Convert their Python worker program to a daemon that is always running in the background. 
  * Containerize the worker program and create a deployment in Kubernetes to deploy the worker to the
    class cluster. 

Daemonizing the Worker
----------------------
In a Unix-like operating system, a *daemon* is a type of program that runs unobtrusively in the background, rather
than under the direct control of a user. The daemon waits to be activated by an occurrence of a specific event or
condition.

In summary: A daemon is a long-running background process that answers requests or responds to events.

Recall the high-level architecture of our Jobs API:

  * Our Flask API will play the role of producer.
  * One or more "worker" programs will play the role of consumer.
  * Workers will receive messages about new jobs to execute and performing the analysis steps.
  * Workers will oversee the execution of the analysis steps and update the database with the results.

Therefore, our worker program is an example of a daemon that will simply run in the background, waiting for new messages
to arrive and executing the corresponding jobs.

We have actually already seen how to turn our Python code into a worker daemon. Let us recall that here:

  * We create a new file, ``worker.py``, where we put all code related to processing a job.
  * The ``worker.py`` will import a queue object from a ``jobs.py`` module
  * The ``worker.py`` file includes a function that can take a message from the queue and start processing a job.
  * The worker will use the queue object's ``worker`` decorator to turn this function into a consumer.
  * By adding a call to the function at the bottom of ``worker.py``, the worker can be run as a daemon.

Here is a skeleton of the worker.py module --

.. code-block:: python

  # worker.py skeleton
  from jobs import q

  @q.worker
  def do_work(item):
      # do something with item...

  do_work()

To execute our worker, we simply issue the command ``python worker.py`` from the command line. Let's step through what
happens, just to make sure this is clear.

  1. When ``python worker.py`` is called from the command line, the python interpreter reads each line of the ``worker.py``
     file and executes any statements it finds in order, from top to bottom.
  2. The first line it encounters is the import statement. This imports the definition of ``q`` from the ``jobs.py`` file
     (not included above).
  3. Next it hits the decorator and the definition of the function, ``do_work(item)``. It checks the syntax of this
     definition.
  4. Finally, it executes the ``do_work()`` function at the bottom. Since this function is decorated with the ``q.worker``
     decorator, it runs indefinitely, consuming messages from the Redis ``q`` queue.

Containerizing the Worker
-------------------------
There are multiple ways to containerize the worker, but the simplest approach is to add the ``worker.py`` code to the
same image with the flask API code, and use different commands when running the web server vs running the worker.

For example, the Dockerfile could look like:

.. code-block:: bash

  # Image: jstubbs/animals-service
  FROM python:3.9

  ADD requirements.txt /requirements.txt
  RUN pip install -r requirements.txt
  COPY source /app
  WORKDIR /app

  ENTRYPOINT ["python"]
  COMMAND ["app.py"]

When running the flask application, the entrypoint and command are already correct. For running the worker, we simply
update the command to be "worker.py" instead of "app.py".

**Exercise**. Update your Dockerfile to include an entrypoint and a command that can be used for running both the flask
web application and the worker program. Build the new version of your image and push it to Docker Hub.

Deploying to k8s
----------------
We're now ready to deploy our complete system to k8s. You should already have deployments and services in k8s for the
Flask API and the Redis database, and you should also already have a PVC for Redis to persist state to a volume.

What's left is to add a deployment for the worker pods. Do we need to add a service or PVC for the workers? Why or why not?

**Exercise**. Create a deployment for your worker pods. Put 2 replicas and be sure to set the command correctly.
See above. A deployment skeleton is included below for you to use if you like. Think through the values of each section;
some properties/stanzas may not be needed for the worker.

.. code-block:: yaml

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: <...>
      labels:
        app: <...>
    spec:
      replicas: <...>
      selector:
        matchLabels:
          app: <...>
      template:
        metadata:
          labels:
            app: <...>
        spec:
          containers:
            - name: <...>
              imagePullPolicy: Always
              image: <...>
              command: <...>
              env:
              - <...>
              ports:
              - <...>


Code Repository
----------------
It is good to keep your code and deployment files organized in a single repository. Consider using a layout similar to
the following:

.. code-block:: bash

  deploy/
    api/
      deployment.yml
      service.yml
    db/
      deployment.yml
      pvc.yml
      service.yml
    worker/
      deployment.yml
  Dockerfile
  source/
    api.py
    jobs.py
    worker.py

