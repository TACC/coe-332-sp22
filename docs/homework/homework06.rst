Homework 06
===========

**Due Date: Tuesday, Apr 19, by 11:00am CDT**

Into the Kubernetes-Verse
-------------------------

The objective of this homework is to complete the in-class exercise from the end
of the `Kubernetes section <../unit07/services.html#homework-6-deploying-our-flask-api-to-k8s>`_.
You must deploy the Flask app and Redis server from
`Homework 05 <./homework05.html>`_
in Kubernetes.


PART 1
~~~~~~

Complete the in-class exercise from the end of the
`Kubernetes section <../unit07/services.html#homework-6-deploying-our-flask-api-to-k8s>`_.
Successful completion of this exercise requires:

* A persistent volume claim for your Redis data
* A deployment for your Redis database
* A service for your Redis database
* A deployment for your Flask API
* A service for your Flask API



PART 2
~~~~~~

The Dockerfile and app.py from Homework 05 should be copied into this new directory,
and updated if necessary. A new README should be included in this homework repository.
Standard README directions apply. No need to include instruction on building
or pushing the docker image to Docker Hub. This time, focus on giving instructions
for deploying the software system to a Kubernetes cluster using the containerized
Flask app that you have previously built and pushed to Docker Hub in your own namespace.




What to Turn In
---------------

A sample Git repository may contain the following new files after completing
homework 06:

.. code-block:: text
   :emphasize-lines: 7-11

   my-coe332-hws/
   ├── homework01
   │   └── ...
   ├── homework02
   │   └── ...
   ├── ... etc
   ├── homework06
   │   ├── Dockerfile               # your file names may vary
   │   ├── app.py
   │   ├── README.md
   │   └── *.yml                    # five YAML files for Part 1
   └── README.md


Additional Resources
--------------------

* `Homework 06 Lab / Steps <../unit07/services.html#homework-6-deploying-our-flask-api-to-k8s>`_.
