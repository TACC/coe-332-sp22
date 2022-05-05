Dynamic Redis IPs
=================

Updating the Flask API to use the Redis Service IP
--------------------------------------------------

In your flask code, you have a line that looks something like this:

.. code-block:: python

  rd=redis.StrictRedis(host='redis', port=6379, db=0)

Recall that the ``host='<some_host>'`` argument instructs the Redis client to use a particular network address
(an IP address or a domain) to connect to Redis. We know from the lab that, in our k8s deployment, the Redis database
will be available from the Redis service IP. We need to make sure that our flask API uses this API.

Option 1: Hard Code the Service IP Directly in the Python Code
---------------------------------------------------------------

This is the simplest approach. If our Redis service IP were ``10.108.118.36`` we would simply replace the above with:

.. code-block:: python

  rd=redis.StrictRedis(host='10.108.118.3', port=6379, db=0)

This works, but the problem is that we have to change the code every time the Redis service IP changes. It's true that
we use services precisely because their IPs don't change, but as we move from our test to our prod environment (recall
the discussion on environments from earlier), the Redis service IP will change. Once our code in the test environment
has been tested, we want to be able to deploy it to prod exactly as is, without making any changes.


Option 2: Pass the IP as an Environment Variable
-------------------------------------------------

The better approach is to pass the Redis IP as an environment variable to our service. Environment variables are
variables that get set in the shell and are available for programs. In python, the ``os.environ`` dictionary
contains a key for every variable. So, we can use the following instead:

.. code-block:: python

  import os

  redis_ip = os.environ.get('REDIS_IP')
  if not redis_ip:
      raise Exception()
  rd=redis.StrictRedis(host=redis_ip, port=6379, db=0)

This way, if we set an environment variable called ``REDIS_IP`` to our Redis service IP before starting our API, the
flask code will automatically pick it up and use it.

In homework 5, you saw how to set environment variables in a k8s pod. We'll revisit this idea when discussing
continuous integration.


