Containerizing Flask
====================

As we have discussed previously, Docker containers are critical to packaging an
application along with all of its dependencies, isolating it from
other applications and services, and deploying it in a consistent and reproducible
way across different platforms.

Here, we will walk through the process of containerizing a Flask application with Docker, and then
using ``curl`` to interact with it as a containerized microservice. After going through this 
module, students should be able to:

* Assemble the different components needed for a containerized microservice into on directory.
* Establish and document requirements (e.g. dependencies, Python packages) for the project.
* Build and run in the background a containerized Flask microservice.
* Map ports on the ISP server to ports inside a container, and use ``curl`` with the 
  the correct ports to make requests to and generate responses from the microservice.


Organize Your App Directory
---------------------------


First, create a "web" directory, and change directories to it:

.. code-block:: console

   [isp02]$ mkdir web
   [isp02]$ cd web

Then, create a new ``app.py`` (or copy an existing one) into this folder. It
should have the following contents:

.. code-block:: python3
   :linenos:

   from flask import Flask

   app = Flask(__name__)

   @app.route('/', methods = ['GET'])
   def hello_world():
       return 'Hello, world!\n'

   @app.route('/<name>', methods = ['GET'])
   def hello_name(name):
       return 'Hello, {}!\n'.format(name)

   if __name__ == '__main__':
       app.run(debug=True, host='0.0.0.0')


Specify Requirements
---------------------
The Python package manager ``pip`` can utilize a text file for managing package dependencies of 
your application. 
It is standard practice to to capture the required libraries and packages for a project in 
a file called ``requirements.txt``. For our example here, create a file called
``requirements.txt`` and add the following line:

.. code-block:: console

   Flask==2.0.3

This indicates that our project requires the ``Flask`` package, version number ``2.0.3``.
You can specify your requirements in more lenient ways -- for example, we could have 
put ``Flask>=2.0.3`` to indicate that any version greater than or equal to ``2.0.3`` would work,
or we could have even put ``Flask`` with no version indicating we don't care what version of 
Flask is installed. 

Note:

* Specifying a package, such as ``Flask`` as a dependency instructs pip to install Flask *and all 
  of its dependencies*. Those dependencies could in turn have dependencies, etc., and pip will 
  take care of installing all of those.  
* Specifying the exact version improves the odds that your application will work correctly because
  the packages that get installed will be the versions you specified. Therefore, it is 
  usually best to specify the exact version of the library your application requires. 

Build a Docker Image
--------------------

As we saw in a previous section, we write up the recipe for our application
installation process in a Dockerfile. Create a file called ``Dockerfile`` for our
Flask microservice and add the following lines:

.. code-block:: console

    FROM python:3.9

    RUN mkdir /app
    WORKDIR /app
    COPY requirements.txt /app/requirements.txt
    RUN pip install -r /app/requirements.txt
    COPY . /app

    ENTRYPOINT ["python"]
    CMD ["app.py"]


Here we see usage of the Docker ``ENTRYPOINT`` and ``RUN`` instructions, which
essentially specify a default command (``python app.py``) that should be run
when an instance of this image is instantiated.

Note also that we copied the ``requirements.txt`` file before copying the full 
current working directory. Why did we do that?

The answer has to do with how Docker caches image layers. We could have written the following 
instead: 

.. code-block:: console

    FROM python:3.9

    RUN mkdir /app
    WORKDIR /app
    COPY . /app
    RUN pip install -r /app/requirements.txt
    
    ENTRYPOINT ["python"]
    CMD ["app.py"]

The above is actually shorter; i.e., fewer lines of code in the Dockerfile.

However, with the above approach, Docker is going to re-run the command 
``pip install -r /app/requirements.txt`` every time there is any change to the contents
of the current working directory (i.e., any time we change our app code or any other files).
This is not a big deal with a small ``requirements.txt`` file and only a few packages to install, 
but as the ``requirements.txt`` file gets bigger, the time to install all the packages
can be significant.

As a general rule of thumb, put more expensive (in term of time) operations whose are less likely 
to change at the beginning of your ``Dockerfile`` to maximize the value of the Docker image 
layer cache. 


Save the file and build the image with the following command:

.. code-block:: console

   [isp02]$ docker build -t <username>/flask-helloworld:latest .

.. warning:

   Don't forget to replace ``<username>`` with your Docker Hub username.

Run a Docker Container
----------------------

To create an instance of your image (a "container"), use the following command:

.. code-block:: console

   [isp02]$ docker run --name "give-your-container-a-name" -d -p <your port number>:5000 <username>/flask-helloworld:latest"

For example:

.. code-block:: console

   [isp02]$ docker run --name "charlies-helloworld-flask-app" -d -p 5050:5000 jstubbs/flask-helloworld:latest

The ``-d`` flag detaches your terminal from the running container - i.e. it
runs the container in the background. The ``-p`` flag maps a port on the ISP
server (5050, in the above case) to a port inside the container (5000, in the
above case). In the above example, the Flask app was set up to use the
default port inside the container (5000), and we can access that through our
specified port on ISP (5050).

Check to see that things are up and running with:

.. code-block:: console

   [isp02]$ docker ps -a

The list should have a container with the name you gave it, an ``UP`` status,
and the port mapping that you specified.

If the above is not found in the list of running containers, try to debug with
the following:

.. code-block:: console

   [isp02]$ docker logs "your-container-name"
   -or-
   [isp02]$ docker logs "your-container-number"


Access Your Microservice
------------------------

Now for the payoff - you can use ``curl`` to interact with your Flask microservice by specifying 
the correct port on the ISP server. Following the example above, which was using
port 5050:

.. code-block:: console

   [isp02]$ curl localhost:5050/
   Hello, world!
   [isp02]$ curl localhost:5050/Joe
   Hello, Joe!


Clean Up
--------

Finally, don't forget to stop your running container and remove it.

.. code-block:: console

   [isp02]$ docker ps -a | grep jstubbs
   60be6788d73d   jstubbs/flask-helloworld:latest     "python app.py"   4 minutes ago   Up 4 minutes   0.0.0.0:5050->5000/tcp   charlies-helloworld-flask-app
   [isp02]$ docker stop 60be6788d73d
   60be6788d73d
   [isp02]$ docker rm 60be6788d73d
   60be6788d73d


**EXERCISE**

Containerize your flask meteorite landings server from last week:

  1. Create a Dockerfile for your server.
  2. Build the image from the Dockerfile. 
  3. Run the server locally and test the ``/data`` endpoint using curl. 