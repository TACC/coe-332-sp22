Orchestration Overview
=======================

In this module, we introduce the basic notion of container orchestration. After going through this 
module, the student should be able to:

* Explain the challenges associated with deploying and managing distributed systems that container 
  orchestration systems are designed to help with.
* Describe at a high level a few of the most common container orchestration systems. 


A typical distributed system is comprised of multiple components. You have already developed a simple HTTP application
that includes a Python program (using the flask library) and a database. In the subsequent weeks, we will add additional
components.

Container orchestration involves deploying and managing containerized applications on one or more computers. There are
a number of challenges involved in deploying and managing a distributed application, including:

 * Container execution and lifecycle management -- To run our application, we must not only start the containers initially
   but also manage the entire lifecycle of the container, including handling situations where containers are stopped and/or
   crash. We need to ensure the correct container image is used each time.
 * Configuration -- Most nontrivial applications involve some configuration. We must be able to provide configuration to
   our application's components.
 * Networking -- The components in our distributed application will need to communicate with each other, and that
   communication will take place over a network.
 * Storage -- Some components, such as databases, will require access to persistent storage to save data to disc so that
   they can be restarted without information loss.

The above list is just an initial set of concerns when deploying distributed, containerized applications. As the size
and number of components grows, some systems may encounter additional challenges, such as:

 * Scaling -- We may need to start up additional containers for one or more components to handle spikes in load on the
   system, and shut down these additional containers to save resources when the usage spike subsides.
 * CPU and Memory management -- The computers we run on have a fixed amount of CPU and memory, and in some cases, it can
   be important to ensure that no one container uses too many resources.
 * Software version updates -- How do we go from version 1 to version 2 of our software? We may have to update several
   components (or all of them) at once. What if there are multiple containers running for a given component? As we are
   performing the upgrade, is the system offline or can users still use it?


Orchestration Systems
^^^^^^^^^^^^^^^^^^^^^

Orchestration systems are built to help with one ore more of the above challenges.
Below we briefly cover some of the more popular, open-source container orchestration systems. This is by no means an
exhaustive list.

Docker Compose
--------------
You have already seen a basic container orchestration system -- Docker Compose. The Docker Compose system allows users
to describe their application in a ``docker-compose.yml`` file, including the ``services``, ``networks``,
``volumes`` and other aspects of the deployment. Docker Compose:

 * Runs Docker containers on a single computer, the machine where docker-compose is installed and run.
 * Utilizes the Docker daemon to start and stop containers defined in the ``docker-compose.yml`` file.
 * Also capable of creating volumes, networks, port bindings, and other objects available in the Docker API.

Docker compose is a great utility for single-machine deployments and, in particular, is a great system for "local
development environments" where a developer has code running on her or his laptop.

Docker Swarm
------------
Docker Swarm provides a container orchestration system to deploy applications across multiple machines running the
Docker daemon. Docker Swarm
works by creating a cluster (also known as a "swarm") of computers and coordinating the starting and stopping of
containers across the cluster. Docker Swarm:

 * Runs Docker containers across a cluster of machines (a "swarm"), each running Docker.
 * Coordinates container execution across the cluster.
 * Similar API to Docker Compose: capable of creating networks spanning multiple computers as well as port-bindings,
   volumes, etc.

Mesos
-----
Apache Mesos is a general-purpose cluster management system for deploying both containerized and non-containerized
applications across multiple computers. Mesos by itself is quite low-level and requires the use of *frameworks* to
deploy actual applications. For example, Marathon is a popular Mesos framework for deploying containerized applications,
while the Mesos Hydra framework can be used for deploying MPI-powered applications, such as those used in traditional
HPC applications.

Kubernetes
----------
Kubernetes (often abbreviated as "k8s") is a container orchestration system supporting Docker as well other container
runtimes that conform to the Container Runtime Interface (CRI) such as containerd and cri-o. While Kubernetes focuses
entirely on containerized applications (unlike Mesos) and is not as similar to Docker Compose as Docker Swarm is,
it provides a number of powerful features for modern, distributed systems management. Additionally, Kubernetes is
available as a service on a large number of commercial cloud providers, including Amazon, Digital Ocean, Google, IBM,
Microsoft, and many more, and TACC provides multiple Kubernetes clusters in support of various research projects.

 * Supports running containerized applications across a cluster of machines for container runtimes conforming to the
   Container Runtime Interface (CRI), including Docker.
 * Provides powerful features for managing distributed applications.
 * Available as a service from TACC and a number of commercial cloud providers.

Additional Resources
^^^^^^^^^^^^^^^^^^^^

 * `Docker Compose Reference <https://docs.docker.com/compose/>`_
 * `Docker Swarm <https://docs.docker.com/engine/swarm/>`_
 * `Apache Mesos Documentation <http://mesos.apache.org/documentation/latest/>`_
 * `Marathon <https://github.com/mesosphere/marathon>`_
 * `Mesos Hydra <https://github.com/mesosphere-backup/mesos-hydra>`_
 * `Kubernetes Documentation <https://kubernetes.io/docs/home/>`_
