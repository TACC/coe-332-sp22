Storing Images in Redis
=======================

As part of the final project, your worker may create an image of a plot. If it 
is created inside the Kubernetes worker pod, you'll need a convenient way to 
retrieve that image back out of the worker container and into whatever container
you curled from.

The easiest way to retrieve the image is for the worker to add the image back
to the Redis db, and for the user to query the database with a flask route and
retrieve the image. This would be the general workflow:


1. The user submits a curl request from, e.g., the py-debug pod to the flask api
2. The flask api creates a new job entry in the redis db, and adds the UUID to the queue
3. The worker picks up the job, and creates a plot
4. The worker saves the plot in the redis db under the job entry
5. The user curls a new route to download the image from the db



Initiate a Job
--------------

Imagine that when a user submits a job, an entry is created in the jobs db of the 
following form:

.. code-block:: console

   [isp02]$ curl localhost:5000/submit -X POST -H "Content-Type: application/json" -d '{"start", "2001", "end": "2021"}'
   Job 161207aa-9fe7-4caa-95b8-27f5bcbb16e7 successfully submitted


.. code-block:: console

   [isp02]$ curl localhost:5000/jobs
   {
     "161207aa-9fe7-4caa-95b8-27f5bcbb16e7": {
       "status": "submitted",
       "start": "2001",
       "end": "2021"
     }
   }

Add an Image to a Redis DB
--------------------------

The worker picks up the job, performs the analysis based on the 'start' and
'end' dates, and generates a plot. An example of using ``matplotlib`` to write
and save a plot to file might look like:


.. code-block:: python3
   :linenos:

   import matplotlib.pyplot as plt

   x_values_to_plot = []
   y_values_to_plot = []

   for key in raw_data.keys():       # raw_data.keys() is a client to the raw data stored in redis
       if (int(start) <= key['date'] <= int(end)):
           x_values_to_plot.append(key['interesting_property_1'])
           y_values_to_plot.append(key['interesting_property_2'])

   plt.scatter(x_values_to_plot, y_values_to_plot)
   plt.savefig('/output_image.png')
    

.. warning::

   The code above should be considered pseudo code and not copy/pasted directly.
   Depending on how your databases are set up, client names will probably be 
   different, you may need to decode values, and you may need to cast type on
   values.


Now that an image has been generated, consider the following code that will open up
the image and add it to the Redis db:

.. code-block:: python3
   :linenos:

    with open('/output_image.png', 'rb') as f:
        img = f.read()

    rd.hset(jobid, 'image', img) 
    rd.hset(jobid, 'status', 'finished')


.. note::

   If anyone has a way to get the png file out of the Matplotlib object without
   saving to file, please share!


Retrieve the Image with a Flask Route
-------------------------------------

Now that the image has been added back to the jobs database, you can expect this
type of data structure to exist:


.. code-block:: console

   {
     "161207aa-9fe7-4caa-95b8-27f5bcbb16e7": {
       "status": "finished",
       "start": "2001",
       "end": "2021",
       "image": <binary image data>
     }
   }

It would not be a good idea to show that binary image data with the rest of the
text output when querying a ``/jobs`` route - it would look like a bunch of
random characters. Rather, write a new route to download just the image given the
job ID:

.. code-block:: python3
   :linenos:

   from flask import Flask, request, send_file

   @app.route('/download/<jobid>', methods=['GET'])
   def download(jobid):
       path = f'/app/{jobid}.png'
       with open(path, 'wb') as f:
           f.write(rd.hget(jobid, 'image'))
       return send_file(path, mimetype='image/png', as_attachment=True)


Flask has a method called 'send_file' which can return a local file, in this
case meaning a file that is saved inside the Flask container. So first, open
a file handle to save the image file inside the Flask container, then return
the image as ``mimetype='image/png'``.

The setup above will print the binary code to the console, so the user should 
redirect the output to file like:

.. code-block:: console

   [isp02]$ curl localhost:5000/download/161207aa-9fe7-4caa-95b8-27f5bcbb16e7  > output.png
   [isp02]$ ls
   output.png


.. note::

   If anyone has a way to download the image to file automatically without 
   redirecting to file, please share!

