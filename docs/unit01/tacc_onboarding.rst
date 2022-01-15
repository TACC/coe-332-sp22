Onboarding to TACC
==================

The Texas Advanced Computing Center (TACC) at UT Austin designs and operates
some of the world's most powerful computing resources. The center's mission is
to enable discoveries that advance science and society through the application
of advanced computing technologies.

We will be using cloud resources at TACC as our development environment. We will
access the cloud resources via our SSH clients and TACC account credentials.
**You will need a TACC username, password, and multifactor token for this class.**

.. attention::

   Everyone please apply for a TACC account now using
   `this link <https://portal.tacc.utexas.edu/web/tup/account-request>`_. If you
   already have a TACC account, you can just use that. Send your TACC username
   to the course instructors via Slack or e-mail as soon as possible (see below).

.. code-block:: console

   To: {wallen, jstubbs, charlie} [at] tacc [dot] utexas [dot] edu
   From: you
   Subject: COE 332 TACC Account
   Body: Please include your name, EID, TACC user name


About TACC
----------

TACC is a Research Center, part of UT Austin, and located at the JJ Pickle
Research Campus.

.. figure:: images/tacc_map.png
    :width: 400px
    :align: center

    A short 7.7 mile walk from main campus!

.. figure:: images/tacc_building.png
    :width: 400px
    :align: center

    One of two TACC buildings located at JJ Pickle.

.. figure:: images/frontera_racks.png
    :width: 400px
    :align: center

    A tall person standing among taller Frontera racks.


**TACC at a Glance**

.. figure:: images/tacc_at_a_glance_1.png
    :width: 350px
    :align: center

.. figure:: images/tacc_at_a_glance_2.png
    :width: 350px
    :align: center

.. figure:: images/tacc_at_a_glance_3.png
    :width: 350px
    :align: center


**Other TACC Activities**

* Portals and gateways
* Web service APIs
* Rich software stacks
* Consulting
* Curation and analysis
* Code optimization
* Training and outreach
* => `Learn more <https://www.tacc.utexas.edu/>`_

.. figure:: images/tacc_portals.png
    :width: 400px
    :align: center

    Snapshot of a few of TACC's portal projects.


**TACC Partnerships**

* NSF: Leadership Class Computing Facility (LCCF)
* NSF: Extreme Science and Engineering Discovery Environment (XSEDE)
* UT Research Cyberinfrastructure (UTRC)
* TX Lonestar Education and Research Network (LEARN)
* Industry, `STAR Program <https://www.tacc.utexas.edu/partnerships/star/partners>`_
* International, The International Collaboratory for Emerging Technologies
* => `Learn more <https://www.tacc.utexas.edu/>`_

.. attention::

   Did you already e-mail your TACC username to the course instructors?

|
|
|
| Which brings us to the question:       **Why are we here teaching this class?**
|
|
|

Engineering Complex Systems in the Cloud
----------------------------------------

The Tapis Framework, developed at TACC, is a great example of a complex
assembly of code with many moving parts, engineered to help researchers interact
with high performance computing systems in streamlined and automated ways. Tapis
empowers its users to:

* Authenticate using TACC (or other) credentials
* Manage, move, share, and publish data sets
* Run scientific code in batch jobs on clusters
* Set up event-driven processes
* `Many other things! <https://tapis-project.org/>`_

The above description of Tapis and the below schematic diagram are both
intentionally left a little bit vague as we will cover more of the specifics of
Tapis later on in the semester.

.. figure:: images/tapis_framework.png
    :width: 600px
    :align: center


.. tip::

   Astute observers may notice that most, if not all, tools, technologies, and
   concepts that form the Tapis ecosystem show up somewhere in the agenda for
   COE 332.


So what can you do with Tapis?

Why would I want to build something similar?

Why should I learn how to use all of these tools and technologies?

Without concrete examples, it can seem rather esoteric. The vignette below
hopefully illustrate how a carefully designed framework can be employed to
tackle a real-world problem.

**Real-Time Quantitative MRI**

*Problem:* Quantitative analysis of MR images is typically performed after the
patient has left the scanner. Corrupted or poor quality images can result in
patient call backs, delaying disease intervention.

*Importance:* Real-time analytics of MRI scans can enable same-session quality
control, reducing patient call backs, and it can enable precision medicine.

*Approach:* Faculty and staff from UTHealth - Houston and TACC used the Tapis
framework to help develop an automated platform for real-time MRI.

*Result:* Scan data can now be automatically processed on high performance
computing resources in real-time with no human intervention.

.. figure:: images/real_time_mri_1.png
    :width: 400px
    :align: center

    Diagram of computer systems and APIs employed.

.. figure:: images/real_time_mri_2.png
    :width: 400px
    :align: center

    Sample platform workflow for combining two images into one enhanced image.

.. figure:: images/real_time_mri_3.png
    :width: 400px
    :align: center

    Final image shows enhanced MS lesions.

Source: https://dx.doi.org/10.1109/JBHI.2017.2771299


.. attention::

   If you already e-mailed your TACC account to the instructors, please go ahead
   and try the exercise below.


Engineering Complex Systems on ... Mars?
----------------------------------------

On April 19, 2021, the helicopter *Ingenuity* (part of NASA's Mars 2020 mission
along with the rover *Perseverance*) completed the first ever "powered
controlled extraterrestrial flight by an aircraft". As of January 2022, it has
made \~18 flights recording pictures, sound, position, and other data during
flight.

.. figure:: images/ingenuity.png
    :width: 500px
    :align: center

    Source:  https://en.wikipedia.org/wiki/File:Anatomy_of_the_Mars_Helicopter.png

How do *Perseverance* and *Ingenuity* communicate to carry out missions and
return that sensor data? The source code for the helicopter / rover themselves are
not public (as far as I can tell), but NASA JPL credited a
`long list <https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/personalizing-your-profile#list-of-qualifying-repositories-for-mars-2020-helicopter-contributor-badge>`_
of open source code repositories on GitHub that the helicopter project depends on.


Included in the list are a lot of libraries and tools that we will be using this
semester to build our cloud systems including: Linux, curl, pycurl, yaml, flask,
click, pytest, jinja, requests, urllib3, werkzeug, (and many others).
Looking at the packages, it seems pretty likely that the rover communicates with
the helicopter through something similar to a REST API! We will all be building
similar systems this semester.


Read more here: https://github.com/readme/featured/nasa-ingenuity-helicopter




Bringing it All Together
------------------------

Hopefully these examples start to show you what kind of software projects we
will be working on this semester. Each week will be introducing a new concept,
tool, or technology that will slowly be building to a larger overall framework
with many moving parts.


For Next Time
-------------

Using your SSH client, please try to log in to the class server **before the
next class period**:

.. code-block:: console
   :emphasize-lines: 1,2,3,24

   [local]$ ssh username@isp02.tacc.utexas.edu
   Password:
   TACC_Token:
   Last login: Fri Jan 14 09:34:04 2022 from cpe-24-27-53-74.austin.res.rr.com
   ------------------------------------------------------------------------------
   Welcome to the Texas Advanced Computing Center
      at The University of Texas at Austin

   ** Unauthorized use/access is prohibited. **

   If you log on to this computer system, you acknowledge your awareness
   of and concurrence with the UT Austin Acceptable Use Policy. The
   University will prosecute violators to the full extent of the law.

   TACC Usage Policies:
   http://www.tacc.utexas.edu/user-services/usage-policies/

   TACC Support:
   https://portal.tacc.utexas.edu/tacc-consulting

   ------------------------------------------------------------------------------
   Intel(R) Parallel Studio XE 2020 Update 1 for Linux*
   Copyright (C) 2009-2020 Intel Corporation. All rights reserved.
   [remote]$ hostname -f
   isp02.tacc.utexas.edu      # success!

.. note::

   In the above, replace 'username' with your TACC username.
