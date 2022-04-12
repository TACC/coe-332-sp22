Final Project
=============

**Due Date for Part 1: Tuesday, April 12, by 11:00am CDT**


**Due Date for Parts 2-4: Thursday, May 12, by 9:00am CDT**


REST APIs: Endgame
------------------

You've previously created a REST API interface to ISS positional data. Expanding
on that idea, the final project will build an interface to a new data set of your
choosing, and it will add a few more interesting features. The final application
will be deployed to the cloud (Kubernetes) and made publicly accessible.


.. warning::

   As long as this warning message is here, the content below is subject to change.
   We will notify the class any time a substantial change is made.




PART 1: Pitch
~~~~~~~~~~~~~

Form groups of **two or three** students to work collaboratively on the final
project. You may choose your own groups, or you may ask the instructors to assign
you to a group. Please let us know what groups you are in, or if you would like
to be assigned to a group ASAP.

The first part of the final project is to identify an interesting data set that
you want to work on. The data set should be Engineering focused, broadly defined.
The data set should also be amenable to CRUD operations and some sort of analysis
(see below). There are several links at the bottom of this page to Engineering-focused
data sets, but you may look elsewhere too. Once your group has identified a potential
data set to work on, write up a ~1 page summary of the proposed title of your
project, list of group members, and a description of the data. Then schedule a
~10 minute meeting with at least one of the instructors in order to "pitch"
your project. We want to know what the source is of the data, see what the data
looks like, and hear what is your proposal for working on the data.



PART 2: Code Repository
~~~~~~~~~~~~~~~~~~~~~~~

The final project will involve building a REST API front-end to a (preferably)
time series data set that allows for basic CRUD - Create, Read, Update, Delete -
operations and also allows users to submit analysis jobs. At a minimum, the application
should support on the back-end an analysis job to create a plot of the data. It
must be hosted on the Kubernetes cluster. Extra credit may be given if the application
supports additional types of analysis jobs, deploys over multiple environments
(test and prod), or has an innovative user interface. The application will consist of:

* A front-end REST API with endpoints for each CRUD operation and for submitting
  a job to plot data and retrieving the resulting plot
* At least two back-end workers to perform the analysis / plotting job(s)
* A Redis database and queue for linking front-end and back-end processes

The project must also include a well-written README following all the guidelines
given in previous class assignments. This README should emphasize two sections:
instructions for deploying and testing the applicaiton, and instructions for
using the application.

Other files including Kubernetes configuration files, Dockerfile(s), Makefile(s),
and functional test file(s) will be expected (see rubric).



PART 3: Write Up
~~~~~~~~~~~~~~~~

We are looking for a 10-11 page written document (as a PDF) describing the project.
The written document should be verbose and targeted towards a non-user, but technically
savvy layperson (e.g. one of your fellow engineering students who is not taking this
class). Be sure to include narrative about the motivation of the project, why it is
interesting or important to have the application you developed, a detailed description
of the data (include citation), and you must include a short section on “Ethical and
Professional Responsibilities in Engineering Situations".

**NEW REQUIREMENT**: The write up must contain at least one design diagram showing
the different parts of your software system and how they are connected. There
should be supporting text to describe the diagram. We will cover some materials
on creating design diagrams in future lectures.


PART 4: Video Demo
~~~~~~~~~~~~~~~~~~

Prepare a < 10 minute video demo of the application. Use zoom to screen share
and record your narration of the process. At a minimum, we want to see you deploy
the application to Kubernetes, curl various routes to demonstrate CRUD operations,
curl the appropriate routes to submit a an analysis job and retrieve and display
the results, and highlight anything else you think is interesting or unique about
your application.


What to Turn In
---------------

This Final project should be pushed into a standalone repo with a descriptive
name. It should not be part of your existing homework repo. A sample Git
repository may contain the following after completing the Final:

.. code-block:: text

    repo-name/
    ├── docker
    │   ├── docker-compose.yml
    │   ├── Dockerfile.api
    │   └── Dockerfile.wrk
    ├── kubernetes
    │   └── prod
    │       ├── app-prod-api-deployment.yml
    │       ├── app-prod-api-service.yml
    │       ├── app-prod-db-deployment.yml
    │       ├── app-prod-db-pvc.yml
    │       ├── app-prod-db-service.yml
    │       └── app-prod-wrk-deployment.yml
    ├── Makefile
    ├── README.md
    ├── requirements.txt
    ├── src
    │   ├── flask_api.py
    │   └── worker.py
    └── test
        └── test_flask.py



Send an email to wallen@tacc.utexas.edu with the written PDF summary of the project
attached plus a link to your new GitHub repository plus a link to download the
zoom recording. Please include "Final Project" in the subject line. We will clone
all of your repos at the due date / time for evaluation.

Additional Resources
--------------------

* `NASA Machine Learning Data Sets <https://data.nasa.gov/browse>`_
* `NASA Earth Data Sets <https://search.earthdata.nasa.gov/search>`_
* `Other NASA Data Sets <https://pds.nasa.gov/datasearch/data-search/>`_
* `Kaggle <https://www.kaggle.com/datasets>`_
* `US Govt Data <https://data.gov/>`_
* `Data World Engineering Data Sets <https://data.world/datasets/engineering>`_
* `List of a bunch of differente data sources <https://libguides.gatech.edu/c.php?g=53976&p=348392>`_
* Please find us in the class Slack channel if you have any questions!
