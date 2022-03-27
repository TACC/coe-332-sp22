Services
========

Services are the k8s resource one uses to expose HTTP APIs, databases and other components that communicate
on a network to other k8s pods and, ultimately, to the outside world. To
understand services we need to first discuss how k8s networking works.

k8s Networking Overview
-----------------------

.. note::

  We will be covering just the basics of k8s networking, enough for you to become proficient with the main concepts
  involved in deploying your application. Many details and advanced concepts will be omitted.

k8s creates internal networks and attaches pod containers to them to facilitate communication between pods. For a number
of reasons, including security, these networks are not reachable from outside k8s.

Recall that we can learn the private network IP address for a specific pod with the following command:

.. code-block:: bash

  $ kubectl get pods <pod_name> -o wide

For example:

.. code-block:: bash

  $ kubectl get pods hello-deployment-9794b4889-mk6qw -o wide
    NAME                               READY   STATUS    RESTARTS   AGE   IP             NODE   NOMINATED NODE   READINESS GATES
    hello-deployment-65c5bbdc69-lk26j   1/1     Running   0          15m   172.16.178.5   kube-3.novalocal   <none>           <none>


This tells us k8s assigned an IP address of ``172.16.178.5`` to our hello-deployment pod.

k8s assigns every pod an IP address on this private network. Pods that are on the same network can communicate with other
pods using their IP address.

Ports
-----
To communicate with a program running on a network, we use ports. We saw how our flask program used port 5000 to
communicate HTTP requests from clients. We can expose ports in our k8s deployments by defining a ``ports`` stanza in
our ``template.spec.containers`` object. Let's try that now.

Create a file called ``hello-flask-deployment.yml`` and copy the following contents

.. code-block:: yaml

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: helloflask
      labels:
        app: helloflask
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: helloflask
      template:
        metadata:
          labels:
            app: helloflask
        spec:
          containers:
            - name: helloflask
              imagePullPolicy: Always
              image: jstubbs/hello-flask
              env:
              - name: FLASK_APP
                value: "app.py"
              ports:
              - name: http
                containerPort: 5000

Much of this will look familiar. We are creating a deployment that matches the pod description given in the ``template.spec``
stanza. The pod description uses an image, ``jstubbs/hello-flask``. This image runs a very simple flask server that
responds with simple text messages to a few endpoints.

The ``ports`` attribute is a list of k8s port descriptions. Each port in the list includes:

  * ``name`` -- the name of the port, in this case, ``http``. This could be anything we want really.
  * ``containerPort`` -- the port inside the container to expose, in this case ``5000``. This needs to match the port
    that the containerized program (in this case, flask server) is binding to.

Let's create the helloflask deployment using ``kubectl apply``

.. code-block:: bash

  $ kubectl apply -f hello-flask-deployment.yml
    deployment.apps/hello-flask-deployment configured

With our deployment created, we should see a new pod.

*Exercise.* Determine the IP address of the new pod for the hello-flask-deployment.

*Solution.*

.. code-block:: bash

  $ kubectl get pods
    NAME                                    READY   STATUS    RESTARTS   AGE
    hello-deployment-9794b4889-w4jlq        1/1     Running   0          56m
    hello-pvc-deployment-6dbbfdc4b4-sxk78   1/1     Running   231        9d
    helloflask-86d4c7d8db-2rkg5             1/1     Running   0          5m10s


  $ kubectl get pods helloflask-86d4c7d8db-2rkg5 -o wide
    NAME                                READY   STATUS    RESTARTS   AGE   IP             NODE               NOMINATED NODE   READINESS GATES
    hello-deployment-65c5bbdc69-lk26j   1/1     Running   0          18m   172.16.178.5   kube-3.novalocal   <none>           <none>


  # Therefore, the IP address is 172.16.178.5

We found the IP address for our flask container, but if we try to communicate with it from the k8s API node, 
we will either find that it hangs indefinitely or possible gives an error:

.. code-block:: bash

  $ curl 172.16.178.5:5000
    curl: (7) Failed connect to 172.16.178.5:5000; Network is unreachable

This is because the 172.16.*.* private k8s network is not available from the outside, not even from the API node.
However, it *is* available from other pods in the namespace.


A Debug Deployment
------------------

For exploring and debugging k8s deployments, it can be helpful to have a basic container on the network. We can
create a deployment for this purpose.

For example, let's create a deployment using the official python 3.9 image. We can run a sleep command inside the
container as the primary command, and then, once the container pod is running, we can use ``exec`` to launch a shell
inside the container.


EXERCISE
^^^^^^^^

1. Create a new "debug" deployment using the following definition:

.. code-block:: yaml

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: py-debug-deployment
      labels:
        app: py-app
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: py-app
      template:
        metadata:
          labels:
            app: py-app
        spec:
          containers:
            - name: py39
              image: python:3.9
              command: ['sleep', '999999999']

(Hint: paste the content into a new file called ``deployment-python-debug.yml``  and then use the ``kubectl apply``
command).

2. Exec into the running pod for this deployment. (Hint: find the pod name and then use the ``kubectl exec``
command, running the shell (``/bin/bash``) command in it).

Once we have a shell running inside our debug deployment pod, we can try to access our flask server. Recall that
the IP and port for the flask server were determined above to be 10.244.7.95:5000 (yours will be different).

If we try to access it using curl from within the debug container, we get:

.. code-block:: bash

    root@py-debug-deployment-5cc8cdd65f-xzhzq: $ curl 172.16.178.5:5000
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
    <title>404 Not Found</title>
    <h1>Not Found</h1>
    <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>

That's a different error from before, and that's good! This time, the error is from flask, and it indicates that flask
doesn't have a route for the root path (``/``).

The ``jstubbs/hello-flask`` image does not define a route for the root path (``/``) but it does define a route for the
path  ``/hello-service``. If we try that path, we should get a response:

.. code-block:: bash

  root@py-debug-deployment-5cc8cdd65f-xzhzq: $ curl 10.244.7.95:5000/hello-service
  Hello world

Great! k8s networking from within the private network is working as expected!


Services
--------

We saw above how pods can use the IP address of other pods to communicate. However, that is not a great solution because
we know the pods making up a deployment come and go. Each time a pod is destroyed and a new one created it gets a new
IP address. Moreover, we can scale the number of replica pods for a deployment up and down to handle more or less load.

How would an application that needs to communicate with a pod know which IP address to use? If there are 3 pods comprising
a deployment, which one should it use? This problem is referred to as the *service discovery problem* in distributed
systems, and k8s has a solution for it.. the ``Service`` abstraction.

A k8s service provides an abstract way of exposing an application running as a collection of pods on a single IP address
and port. Let's define a service for our hello-flask deployment.


Copy and paste the following code into a file called ``hello-flask-service.yml``:

.. code-block:: yaml

    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: hello-service
    spec:
      type: ClusterIP
      selector:
        app: helloflask
      ports:
      - name: helloflask
        port: 5000
        targetPort: 5000

Let's look at the ``spec`` description for this service.

  * ``type`` -- There are different types of k8s services. Here we are creating a ``ClusterIP`` service. This creates an
    IP address on the private k8s network for the service. We may see other types of k8s services later.
  * ``selector`` -- This tells k8s what pod containers to match for the service. Here we are using a label,
    ``app: helloflask``, which means k8s will link all pods with this label to our service. Note that it is important that
    this label match the label applied to our pods in the deployment, so that k8s links the service up to the correct
    pods.
  * ``ports`` - This is a list of ports to expose in the service.
  * ``ports.port`` -- This is the port to expose on the service's IP. This is the port clients will use when communicating
    via the service's IP address.
  * ``ports.targetPort`` -- This is the port on the pods to target. This needs to match the port specified in the pod
    description (and the port the containerized program is binding to).

We create this service using the ``kubectl apply`` command, as usual:

.. code-block:: bash

  $ kubectl apply -f hello-flask-service.yml
    service/hello-service configured

We can list the services:

.. code-block:: bash

    $ kubectl get services
    NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
    hello-service   ClusterIP   10.108.58.137    <none>        5000/TCP                        4s

We see k8s created a new service with IP ``10.108.58.137``. We should be able to use this IP address (and port 5000) to
communicate with our flask server. Let's try it. Remember that we have to be on the k8s private network, so we need to
exec into our debug deployment pod first.

.. code-block:: bash

  $ kubectl exec -it py-debug-deployment-5cc8cdd65f-xzhzq -- /bin/bash

  # from inside the container ---
  root@py-debug-deployment-5cc8cdd65f-xzhzq:/ $ curl 10.108.58.137:5000/hello-service
  Hello world

It worked! Now, if we remove our hello-flask pod, k8s will start a new one with a new IP address, but our service will
automatically route requests to the new pod. Let's try it.

.. code-block:: bash

  # remove the pod ---
  $ kubectl delete pods helloflask-86d4c7d8db-2rkg5
    pod "helloflask-86d4c7d8db-2rkg5" deleted

  # see that a new one was created ---
  $ kubectl get pods
    NAME                                    READY   STATUS    RESTARTS   AGE
    hello-deployment-9794b4889-w4jlq        1/1     Running   2          175m
    hello-pvc-deployment-6dbbfdc4b4-sxk78   1/1     Running   233        9d
    helloflask-86d4c7d8db-vbn4g             1/1     Running   0          62s

  # it has a new IP ---
  $ kubectl get pods helloflask-86d4c7d8db-vbn4g -o wide
    NAME                          READY   STATUS    RESTARTS   AGE    IP            NODE   NOMINATED NODE   READINESS GATES
    helloflask-86d4c7d8db-vbn4g   1/1     Running   0          112s   10.244.7.96   c05    <none>           <none>
  # Yep, 10.244.7.96 -- that's different; the first pod had IP 10.244.7.95

  # but back in the debug deployment pod, check that we can still use the service IP --
  root@py-debug-deployment-5cc8cdd65f-xzhzq:/ $ curl 10.108.58.137:5000/hello-service
  Hello world


Note that k8s is doing something non-trivial here. Each pod could be running on one of any number of worker computers in
the TACC k8s cluster. When the first pod was deleted and k8s created the second one, it is quite possible it started it
on a different machine. So k8s had to take care of rerouting requests from the service to the new machine.

k8s can be configured to do this "networking magic" in different ways. While the details are beyond the scope of this
course, keep in mind that the virtual networking that k8s uses does come at a small cost. For most applications,
including long-running web APIs and databases, this cost is negligible and isn't a concern. But for high-performance
applications, and in particular, applications whose performance is bounded by the performance of the underlying network,
the overhead can be significant.


HomeWork 6 -- Deploying Our Flask API to k8s
--------------------------------------------

In this section we will use class time to deploy our Flask API to k8s. This will be a guided, hands-on lab,
and it will also be submitted for a grade as HomeWork 6. Feel free to ask questions as you work through the lab. Any
part of the assignment that you do not complete during class will need to be completed outside of class 
before you submit. 

Our goal is to create a "test" environment for our Flask API application. We will be using names and labels
accordingly. Later in the semester, you will create a "production" environment for your Flask API application as well.
You can use this lab as a guide to do that.

In each step you will create a k8s object described in a separate yml file. Name the files ``<username>-<env>-<app>-<kind>.yml``,
Use "test" for ``<env>`` since we are creating the test environment. For example, my Redis deployment would
be called ``jstubbs-test-redis-deployment.yml`` while my redis service would be called 
``jstubbs-test-redis-service.yml``.

Step 1. We will start by focusing on our Redis container. Our Flask API depends on Redis so it makes sense to start there.
Since Redis writes our application data to disk, we will need a way to save the data independent of the Redis pods.
Create a persistent volume claim for your Redis data. Use the following information when creating your PVC:

  * The name of your PVC should include your TACC username and the word "test", to indicate it is in the test environment.
  * We'll make use of ``labels`` to add additional metadata to our k8s objects that will help us search and filter them. Let's
    add a ``username`` label and an ``env`` label. The value for ``username`` should be your tacc username and the value
    for ``env`` should be ``test``, to indicate that this is the test environment.
  * The ``accessModes`` should include a single entry, ``readWriteOnce``.
  * The ``storageClassName`` should be ``rbd``.
  * Be sure to request 1 GB (``1Gi``) of storage.


Step 2. Create a deployment for the Redis database. Be sure to include the following:

  * The name of your redis deployment should include your TACC username and the word "test", to indicate it is in the test environment.
  * Use the same ``username`` and ``env`` labels for both the deployment and the pod template.
  * Be sure to set ``replicas: 1`` as Redis is a stateful application.
  * For the image, use ``redis:5.0.0``; you do not need to set a command.
  * Add the ``username`` and ``env`` lables to the pod as well. Also add an ``app`` label with value ``<username>-test-redis``.
    This will be important in the next step.
  * Be sure to create a ``volumeMount`` and associate it with a ``volume`` that is filled by the PVC you created in Step 1. For
    the mount path, use ``/data``, as this is where Redis writes its data.


Step 3. Create a service for your Redis database. This will give you a persistent IP address to use to talk to Redis,
regardless of the IPs that may be assigned to individual Redis pods. Be sure to include the following:

  * The name of your redis service should include your TACC username and the word "test", to indicate it is in the test environment.
  * Use the same ``username`` and ``env`` labels for both the deployment and the pod template.
  * The ``type`` of service should be ``ClusterIP``.
  * Define a ``selector`` that will select your Redis pods and only your redis pods. What label should you use? Hint: the
    ``env`` and ``username`` labels won't be unique enough.
  * Make sure ``port`` and ``targetPort`` match the Redis port.

Once you are done with Steps 1 though 3, check your work:

  * Look up the service IP address for your test redis service.
  * Exec into a Python debug container.
  * Install the redis python library.
  * Launch the python shell and import redis
  * Create a Python redis client object using the IP and port of the service, something like:
    ``rd = redis.StrictRedis(host='10.101.101.139', port=6379, db=0)``
  * Create a key and make sure you can get the key.
  * In another shell on isp02, delete the redis pod. Check that k8s creates a new redis pod.
  * Back in your python shell, check that you can still get the key using the same IP. This will show that your service is
    working and that your Redis database is persisting data to the PVC (i.e., the data are surviving pod restarts).

Step 4. Create a deployment for your flask API. If it helps, you can use your Redis deployment as a starting point. Be sure to:

  * The name of your flask service should include your TACC username and the word "test", to indicate it is in the test environment.
  * Use the same ``username`` and ``env`` labels for both the deployment and the pod template.
  * start 2 replicas of your flask API pod.
  * Be sure to expose port 5000.

Step 5. Create a service for your flask API. This will give you a persistent IP address to use to talk to your flask API,
regardless of the IPs that may be assigned to individual flask API pods. Be sure to include the following:

  * The name of your redis service should include your TACC username and the word "test", to indicate it is in the test environment.
  * Use the same ``username`` and ``env`` labels for both the deployment and the pod template.
  * The ``type`` of service should be ``ClusterIP``.
  * Define a ``selector`` that will select your flask API pods and only your flask API pods.
  * Make sure ``port`` and ``targetPort`` match the flask port.


