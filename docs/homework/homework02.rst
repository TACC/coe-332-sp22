Homework 02
===========

**Due Date: Thursday, Feb 10, by 11:00am CST**

Return of the JSON
------------------

Scenario: You are operating a robotic vehicle on Mars and the task for today is
to investigate **five** meteorite landing sites in
`Syrtis Major <https://en.wikipedia.org/wiki/Syrtis_Major_quadrangle>`_.


PART 1
~~~~~~

In the first part of this homework, you will write a Python script to randomly
generate latitude, longitude, and compositions for the five meteorite landing
sites. The requirements are as follows:

* Generate five random pairs of latitudes (between ``16.0 - 18.0`` degrees North)
  and longitudes (between ``82.0 - 84.0`` degrees East) in decimal notation
* For each landing site, also randomly choose a meteorite composition from the
  list ``["stony", "iron", "stony-iron"]``
* Assemble these data into a *dictionary with one key, "sites", whose value
  is a list of dictionaries*
* Use the Python ``json`` library to save the data to a JSON file. For example,
  your data structure may look like:

.. code-block:: console

    {
      "sites": [
        {
          "site_id": 1,
          "latitude": 17.93705170143149,
          "longitude": 83.36448444826725,
          "composition": "stony"
        },
        {
          "site_id": 2,
          "latitude": 16.714833623042153,
          "longitude": 82.84554246756586,
          "composition": "iron"
        },
    ... etc


PART 2
~~~~~~

In the second part of this homework, you will write a **new** Python script that
reads in the meteorite site JSON file and calculates the time required to visit
and take samples from the five sites in order. The requirements are as follows:

* The second Python script should use the ``json`` library to read in the data
  generated in part 1 and store it as a dictionary
* Assume the robot starts at latitude / longitude ``{16.0, 82.0}``
* Assume the robot visits the five sites in the order of the list index
* Assume the max robot speed is ``10 km per hour``
* Assume that Mars is a sphere (radius = ``3389.5 km``) and use the great-circle
  distance algorithm to calculate distance between points (check Slack for a hint
  on this)
* When the robot stops to take a sample of each meteorite, the amount of time it
  stops depends on the composition of the meteorite. Stony meteorites take ``1 hour``
  to sample, iron meteorites take ``2 hours`` to sample, and stony-iron meteorites
  take ``3 hours`` to sample
* The trip is "over" after sampling the last meteorite. Print some descriptive
  info for each leg of the trip, plus summary info for the whole trip. Sample
  output might look similar to:


.. code-block:: console

   leg = 1, time to travel = 11.75 hr, time to sample = 1 hr
   leg = 2, time to travel = 3.43 hr, time to sample = 2 hr
   leg = 3, time to travel = 4.53 hr, time to sample = 1 hr
   leg = 4, time to travel = 6.04 hr, time to sample = 2 hr
   leg = 5, time to travel = 10.43 hr, time to sample = 3 hr
   ===============================
   number of legs = 5, total time elapsed = 45.17 hr



PART 3
~~~~~~

Your homework 02 files must be within a new subdirectory called ``homework02`` in
your COE332 homeworks repository on GitHub. The directory should contain the two
Python scripts and a ``README.md`` file. The README should be descriptive, use
proper grammar, and contain enough instructions so anyone else could clone the
repository and figure out what the scripts do and how to run them. General
guidelines to follow for the README for homework 02 are:

* Descriptive title
* High-level description of the folder contents / project objective. I.e. why
  does this exist and why is it important? (2-3 sentences)
* Specific description of the individual python scripts (1-2 sentences each)
* Instructions to run the code from start to finish, plus how to interpret the
  results (2-3 sentences)
* Try to use markdown styles to your advantage, give the sections headers, use
  code blocks where appropriate, etc.

Remember, the README is your chance to document for yourself and explain to others
why the project is important, what the code is, and how to use it / interpret
the outputs / etc. This is a *software engineering and design* class, so we are
not just checking to see if your code works. We are also evaluating the design of
the overall submission, including how well the project is described in the README.





What to Turn In
---------------

A sample Git repository may contain the following new files after completing
homework 02:

.. code-block:: text
   :emphasize-lines: 6-9

   my-coe332-hws/
   ├── homework01
   │   ├── list_generator.py
   │   ├── README.md
   │   └── word_reader.py
   ├── homework02
   │   ├── calculate_trip.py    # your file names may vary
   │   ├── generate_sites.py
   │   └── README.md
   └── README.md

There is no need to email the link to your homework repo again, as we should have
it on file from the first homework. We will re-clone the same repo as before at the
due date / time for evaluation.




Additional Resources
--------------------

* `JSON guide <https://coe-332-sp22.readthedocs.io/en/main/unit02/json.html>`_
* `Latitude and Longitude as decimals <https://en.wikipedia.org/wiki/Decimal_degrees>`_
* `Great-circle distance formula <https://en.wikipedia.org/wiki/Great-circle_distance>`_
* `Markdown syntax <https://www.markdownguide.org/basic-syntax/>`_
* `Tips on writing a good README <https://www.makeareadme.com/>`_
* Please find us in the class Slack channel if you have any questions!
