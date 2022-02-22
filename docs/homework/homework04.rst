Homework 04
===========

**Due Date: Tuesday, Mar 1, by 11:00am CST**

Once Upon a Time in Containers
------------------------------

Scenario: You've been working on some code to analyze Meteorite Landing data.
You wrote a few functions, you wrote some unit tests, you are satisfied with how
everything is working together, and now you are ready to release your application
to the world.

PART 1
~~~~~~

Gather a recent copy of the ``ml_data_analysis.py`` script and the
``Meteorite_Landings.json`` data. You can start with the copy linked in the beginning
of the
`Advanced Containers guide <../unit04/containers_2.html>`_, or you can use your
own. Either way, for this homework you will need to make a couple small modifications
to the Python3 script:

* Modify the script to take the name of the JSON file as a command line argument
* Have the script print out summary hemisphere data rather than individual
  hemisphere data for each meteor (see sample below). You will likely want to
  keep the functionality of the ``check_hemisphere()`` function the same, and do
  the actual counting of each quadrant for summary in the ``main()`` function.
* Print out some other text and formatting to make the output look a little nicer
  to read (see sample below)
* Update the tests in your ``pytest`` suite (if necessary) to make sure they are
  still passing with all the code changes. You should have a minimum of five tests
  for each of the three functions in ``ml_data_analysis.py``

After the updates above, running your script against a representative data set
might give output similar to the following (it does not have to match this exactly,
it should just be informative and easy to read):

.. code-block:: console

   [isp02]$ ./ml_data_analysis Meteorite_Landings.json
   Summary data following meteorite analysis:

   Average mass of 30 meteor(s):
   83857.3 grams

   Hemisphere summary data:
   There were 21 meteors found in the Northern & Eastern quadrant
   There were 6 meteors found in the Northern & Western quadrant
   There were 0 meteors found in the Southern & Eastern quadrant
   There were 3 meteors found in the Southern & Western quadrant

   Class summary data:
   The L5 class was found 1 times
   The H6 class was found 1 times
   The EH4 class was found 2 times
   The Acapulcoite class was found 1 times
   The L6 class was found 6 times
   ... etc


PART 2
~~~~~~

Write a Dockerfile to containerize your ``ml_data_analysis.py`` script, the test
script (for pytest), and a copy of the example data set. Build an image with
the version tag ``hw04`` and push it to your namespace in Docker Hub.


.. important::

   For this homework, use the version tag ``hw04`` exactly as it
   appears here. We will specifically be looking for that tag. No, do not use 'hw4'
   or 'hw-04' or any other similar spelling.


PART 3
~~~~~~

You've written the code, you've containerized it, now you need to tell other
people how to use it. The problem is that you are not quite sure *how* people will
want to use this code. In the real world, developers and end users will find your
repo, and some of them will want to use your pre-built Docker image to run the
code, while others will want to start from your Dockerfile and build their own
image. The challenge is to write a README that caters to all audiences.


Write a README with the standard sections from previous homeworks: there should
be a descriptive title, there should be a high level description of the project,
there should be concise descriptions of the main files within, and you should
be using Markdown styles and formatting to your advantage.

The section of the README with run instructions will be a little more challenging.
There should be instructions to, at a minimum:

* Pull and use your existing image on Docker Hub
* Build an image from your Dockerfile
* Run the containerized code against the sample data inside the container
* Run the containerized code against user-provided data that they may have found
  on the web
* Run the containerized test suite with pytest

Give example commands and expected outputs in code blocks.

Finally, your README should also have a section to describe the expected input
data. You need to provide enough information to the reader so they know what
their input data should look like. Showing a snippet of the data in a code block
would be a really good idea here. You may also want to mention that there is some
additional Meteorite Landing data available
`at this link <https://raw.githubusercontent.com/wjallen/coe332-sample-data/main/ML_Data_Sample.json>`_.
**DO NOT** embed this data in your container. **DO** provide instructions to
users to download this data and run the containerized scripts against this data.


What to Turn In
---------------

A sample Git repository may contain the following new files after completing
homework 04:

.. code-block:: text
   :emphasize-lines: 8-13

   my-coe332-hws/
   ├── homework01
   │   └── ...
   ├── homework02
   │   └── ...
   ├── homework03
   │   └── ...
   ├── homework04
   │   ├── Dockerfile               # your file names may vary
   │   ├── ml_data_analysis.py
   │   ├── README.md
   │   ├── Meteorite_Landings.json
   │   └── test_ml_data_analysis.py
   └── README.md


Additional Resources
--------------------

* `Extra Meteorite Landing Data <https://raw.githubusercontent.com/wjallen/coe332-sample-data/main/ML_Data_Sample.json>`_
