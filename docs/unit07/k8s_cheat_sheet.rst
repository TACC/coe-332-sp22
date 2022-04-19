k8s Cheat Sheet
===============

This all-in-one k8s cheat sheet can be used for quick reference.

k8s Resource Types
------------------
Here are the primary k8s resource types we have covered in this class:

  * ``Pods`` -- Pods are the simplest unit of compute in k8s and represent a generalization of the Docker container. Pods
    can contain more than one container, and every container within a pod is scheduled together, on the same machine,
    with a single IP address and shared file system. Pod definitions include a ``name``, a (Docker) ``image``, a ``command`` to run,
    as well as an ``ImagePullPolicy``, ``volumeMounts`` and a set of ``ports`` to expose. Pods can be thought of as
    disposable and temporary.
  * ``Deployments`` -- Deployments are the k8s resource type to use for deploying *long-running* application components,
    such as APIs, databases, and long-running worker programs. Deployments are made up of one or more matching pods, and the
    matching is done using ``labels`` and ``labelSelectors``.
  * ``PersistentVolumeClaims`` (PVCs) -- PVCs create a named storage request against a storage class available on the k8s
    cluster. PVCs are used to fill volumes with permenant storage so that data can be saved across different pod executions
    for the same stateful application (e.g., a database).
  * ``Services`` -- Services are used to expose an entire application component to other pods. Services get their own IP
    address which persists beyond the life of the individual pods making up the application.



kubectl Commands
----------------

Here we collect some of the most commonly used ``kubectl`` commands for quick reference.

+------------------------------------+-----------------------------+------------------------------------------+
| Command                            | Description                 |   Example                                |
+====================================+=============================+==========================================+
| kubectl get <resource_type>        | List all objects of a       | kubectl get pods                         |
|                                    | given resource type.        |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl get <type> <name>          | Get one object of a         | kubectl get pods hello-pod               |
|                                    | given type by name.         |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl get <type> <name> -o wide  | Show additional             | kubectl get pods hello-pod -o wide       |
|                                    | details of an object        |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl describe <type> <name>     | Get full details of an      | kubectl describe pods hello-pod          |
|                                    | object by name.             |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl logs <name>                | Get the logs of a running   | kubectl logs hello-pod                   |
|                                    | pod by name.                |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl logs -f <name>             | Tail the logs of a running  | kubectl logs -f hello-pod                |
|                                    | pod by name.                |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl logs --since <time> <name> | Get the logs of a running   | kubectl logs --since 1m -f hello-pod     |
|                                    | pod since a given time.     |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl exec -it <name> \--  <cmd> | Run a command, <cmd>, in a  | kubectl exec -it hello-pod \-- /bin/bash |
|                                    | running pod.                |                                          |
+------------------------------------+-----------------------------+------------------------------------------+
| kubectl apply -f <file>            | Create or update an object  | kubectl apply -f hello-pod.yml           |
|                                    | description using a file.   |                                          |
+------------------------------------+-----------------------------+------------------------------------------+