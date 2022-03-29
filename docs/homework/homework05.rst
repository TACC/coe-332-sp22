Homework 05
===========

**Due Date: Tuesday, Apr 5, by 11:00am CST**

Back to the Flask
-----------------

A database server (like Redis) is a critical addition to our applications for
data persistence. In this short homework assignment, we will launch a Redis
container, then build a small Flask app to load data into and retrieve data from
the database.


PART 1
~~~~~~

Launch a new containerized instance of a Redis database server using the stock
``redis:6`` image. Be sure to:

* Run the container in the background
* Connect your assigned Redis port on ISP to the default Redis port inside the
  container
* Mount a local folder to the ``/data`` folder inside the container for Redis
  to write its backups to
* Configure the database server (as we did in class) to save to 1 backup file
  every 1 second

The command to do the above plus a description of what it is doing should be
included in the README.


PART 2
~~~~~~

Write a containerized Flask app with one route that handles both a POST and a
GET request.

* The route should be called ``/data``
* A POST request to ``/data`` should load some Meteorite Landings data into your
  Redis database instance
* A GET request to ``/data`` should read the data out of Redis and return it as
  a JSON list
* For bonus points, implement an optional start query parameter that takes an
  integer and returns the Meteorite Landing data starting at that index. Make
  sure to handle the case where start is provided but is not an integer!

Use the Meterorite Landing data found at this link:

https://raw.githubusercontent.com/wjallen/coe332-sample-data/main/ML_Data_Sample.json

Please include docstrings and type annotations in your Python3 code.


PART 3
~~~~~~

A Dockerfile and a README should be included in your homework repository. The
usual Dockerfile and README guidelines apply. Make sure to provide instructions
to launch the Redis database, instructions to pull/build/launch the Flask app,
instructions to perform POST and GET requests to your Flask app, and a
description of the data.



What to Turn In
---------------

A sample Git repository may contain the following new files after completing
homework 05:

.. code-block:: text
   :emphasize-lines: 7-10

   my-coe332-hws/
   ├── homework01
   │   └── ...
   ├── homework02
   │   └── ...
   ├── ... etc
   ├── homework05
   │   ├── Dockerfile               # your file names may vary
   │   ├── app.py
   │   └── README.md
   └── README.md


Additional Resources
--------------------

* `Meteorite Landing Data <https://raw.githubusercontent.com/wjallen/coe332-sample-data/main/ML_Data_Sample.json>`_
* `Persistence in Redis <https://redis.io/topics/persistence>`_
