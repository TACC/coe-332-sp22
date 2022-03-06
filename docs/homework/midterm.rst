Midterm Project
===============

**Due Date: Tuesday, March 22, by 11:00am CST**

O ISS, Where Art Thou?
----------------------

Scenario: You have found an abundance of positional data for the International
Space Station (ISS). It is full of interesting information including ISS position
and velocity data at given times, as well as when the ISS can be seen over
select cities. It is a challenge, however, to sift through the data manually to
find what you are looking for. Your objective is to build a containerized Flask
application for querying and returning interesting information from the ISS data
set.


PART 1
~~~~~~

Write and containerize a Flask application for tracking ISS position and
sightings. The application should load in two data sets (referred to below as
the "positional data" and the "sighting data").

* The ISS positional data set `found here <https://data.nasa.gov/Space-Science/ISS_COORDS_2022-02-13/r6u8-bhhq>`_, under the link 'Public Distribution File'
* One of the 18 sighting data sets, `also found here <https://data.nasa.gov/Space-Science/ISS_COORDS_2022-02-13/r6u8-bhhq>`_ under various names.
  Find the name of the sighting data set that was assigned to you in the spreadsheet
  uploaded to Canvas

The Flask application should have routes to return:

* Information on how to interact with the application
* All Epochs in the positional data
* All information about a specific Epoch in the positional data
* All Countries from the sighting data
* All information about a specific Country in the sighting data
* All Regions associated with a given Country in the sighting data
* All information about a specific Region in the sighting data
* All Cities associated with a given Country and Region in the sighting data
* All information about a specific City in the sighting data

In addition, a special route is needed to load the data from file into memory.
This must be a **POST** endpoint. Refer to the unit on `XML <../unit02/xml.html>`_
for tips on reading XML data into a dictionary.

All of the normal Python3 script best practices apply:

* Write appropriately formatted doc strings
* Use type annotations where appropriate
* Use log messages to report when routes are queried


PART 2
~~~~~~

**Dockerfile**. In addition to the application Python script, you must write and
include a Dockerfile. The Dockerfile should containerize the application and both
data sets (positional data and sighting data), and it should set a default command
to launch the Flask application.

**Pytest**. A unit test file must exist, it must be compatible with Pytest, and
it must also be included in the container. Each function (except ``main()``) in
the Flask application must be adequately tested.

**Makefile**. A Makefile must be written with targets to build a container and to
start the containerized Flask application (at a minimum). Please make sure to
configure the Makefile to use the Flask port you were assigned (in the range 5001-5040).


PART 3
~~~~~~

This Midterm has two required written components:

**README**. Standard README guidelines apply. The README should have a descriptive
title (not "Midterm Project"), a short summary / description of the project,
short descriptions of any very important files that the reader should know about,
instructions to download the data and build the containerized app, instructions
to pull a pre-containerized copy of the app from Docker Hub, instructions to interact
with the application, and guidelines on how to interpret the results.
Be succinct, use Markdown styles, and show example commands / expected output.

**Write Up**. You must also turn in a written document (as a PDF) describing the
project. Where as the README is typically more succinct and targeted towards
developers / users of the application, the written document should be more verbose
and targeted towards a non-user, but technically savvy layperson. For example,
in the README you might instruct a user: "Run the application by performing xyz".
In the Write Up, you would instead describe what users do: "Users of the application
can run it by performing xyz". In other words, write this document as if
you are describing to your fellow engineering students what you did in this class
for your Midterm project.

In the Write Up, include some
narrative about the motivation of the project and why it is an interesting or
important application to have. You must also include a short section on "Ethical
and Professional Responsibilities in Engineering Situations". As a suggestion, you
may consider describing the importance of citing data or the importance of verifying
the quality and accuracy of data that goes into applications such as these. We
strongly encourage you to come up with other Ethical and Professional Responsibilities
that might apply here.

Please make sure to appropriately cite the `data source <https://data.nasa.gov/Space-Science/ISS_COORDS_2022-02-13/r6u8-bhhq>`_
in both the README and the written document.




What to Turn In
---------------

This Midterm project should be pushed into a standalone repo with a descriptive
name (not "coe332-midterm"). It should
not be part into your existing homework repo. A sample Git repository may
contain the following after completing the Midterm:

.. code-block:: text

   descriptive-repo-name/
   ├── Dockerfile
   ├── Makefile
   ├── README.md
   ├── app.py
   └── pytest_app.py

**Do not include the raw XML data as part of your repo.**

Send an email to wallen@tacc.utexas.edu with the written PDF summary of the project
attached plus a link to your new GitHub repository. Please include "Midterm Project"
in the subject line. We will clone all of your repos at the due date / time for evaluation.

Additional Resources
--------------------

* `NASA Data Set <https://data.nasa.gov/Space-Science/ISS_COORDS_2022-02-13/r6u8-bhhq>`_
* `Unit on XML <../unit02/xml.html>`_
* Please find us in the class Slack channel if you have any questions!
