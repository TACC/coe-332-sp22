Class Introduction
==================

Welcome to **COE 332: Software Engineering & Design!**

Course Time: Every T/TH 11:00am - 12:30pm on Zoom

The objective of this course is to introduce students to advanced computing
concepts in software engineering, software systems design, cloud computing and
distributed systems, and computational engineering. Through a series of
assignments spanning the course of the semester, students will build a
cloud-based, computational system to analyze a time series dataset and provide a
web-accessible interface to their system.

.. note::

   Zoom recordings should be automatically uploaded to Canvas following each lecture.


About the Instructors
---------------------

**Joe Allen**

Joe Allen joined the TACC Life Sciences Computing Group in 2015 where he works
to advance computational biology and bioinformatics research at UT system
academic and health institutions. His research experience spans a range of
disciplines from computer-aided drug design to wet lab biochemistry. He is also
interested in exploring new ways high performance computing resources can be
used to answer challenging biological questions. Before joining TACC, Joe earned
a B.S. in Chemistry from the University of Jamestown (2006), and a Ph.D. in
Biochemistry from Virginia Tech (2011).

**Charlie Dey**

Charlie is the Director of Training and Professional Development with the User
Services group at TACC with a background in web development and scientific
computing. Charlie's responsibilities at TACC include organizing, developing
content, and building curriculums for TACC's academic course selection taught in
conjunction with several departments at the University of Texas at Austin, as
well as for TACC's professional development and educational training. Prior to
joining TACC, he worked as a Senior Application Developer for the Carle
Foundation, and as a computer science instructor at Parkland College in
Champaign, IL. He was also a member of a specialized application development
team at the University of Illinois and has also been a contracted research
consultant for NASA Ames Research Center, studying computational immunology and
bioinformatics. Charlie holds a Bachelor's Degree concentrating in Computer
Science and Biology from Eastern Illinois University, and certifications in 3D
programing and visualization.

**Brandi Kuritz**

Brand joined TACC in 2018 as an intern and established CIC's support
infrastructure while learning software engineering and computer science
fundamentals. She is now employed full time as a software developer and is
currently working on development for TACC APIs and supporting utilities.

**Joe Stubbs**

Joe leads the Cloud and Interactive Computing (CIC) group, which focuses on
building cloud native applications and infrastructure for computational science.
CIC develops, deploys and maintains multiple national-scale clouds as part of
ongoing projects funded by the National Science Foundation. Additionally, the
CIC group contributes to and deploys multiple cloud platforms-as-a-service for
computational science including the Agave science-as-a-service platform, TACC's
custom JupyterHub, and Abaco: Functions-as-a-service via the Actor model and
Linux containers. These platforms are leveraged by numerous cyberinfrastructure
projects used by tens of thousands of investigators across various domains of
science and engineering. Prior to joining the University of Texas, Joe received
a B.S. in Mathematics from the University of Texas, Austin and a Ph.D. in
Mathematics from the University of Michigan. His recent interests include
distributed systems, container technologies and interactive scientific
computing.



About the Syllabus
------------------

**Grades for the course will be based on the following:**

* 30% Homework - Approximately 8 assignments, assigned on Tuesdays and due the
  following Tuesday. The lowest score will be dropped.
* 30% Midterm - We will have a written midterm.
* 40% Project - Students will form groups and will submit a final class project
  consisting of a distributed, web-accessible, cloud system to analyze a time
  series dataset. The project will draw from and build upon work done throughout
  the semester in homework assignments. ​More details will be given in the
  upcoming weeks.

**Approximate Schedule and Key Dates (subject to change):**

* Week 1: Intro, Linux, Python Review
* Week 2: JSON, Unit Testing
* Week 3: Version Control, Intro to Containers
* Week 4: Advanced Containers, YAML, Docker Compose
* Week 5: HTTP, REST, Intro to Flask
* Week 6: Advanced Flask, Integration Testing
* Week 7: Databases, Persistence in REST
* Week 8: Review, Midterm
* Spring Break
* Week 9: Virtualization: Container Orchestration and Kubernetes
* Week 10: Virtualization: Container Orchestration and Kubernetes Cont
* Week 11: Continuous Integration
* Week 12: Asynchronous Programming
* Week 13: Queues
* Week 14: Special Topics
* Week 15: Special Topics - Final Week of Class
* Last Day of Class: Final Project Due


Office Hours
------------

Office hours will be held every T/TH for 1 hour immediately following the class.
Office hours will also be available by appointment. No in-person office visits
are available this semester. Instead, we will meet by Zoom (*ad hoc*) and/or in
the dedicated class Slack space:

https://tacc-learn.slack.com/

Within that space, you will find two channels dedicated to this class:
`#coe332-general` and `#coe332-officehours`. The general channel will be used
for general discussion, posting links and materials, and making general
announcements to the class. The office hours channel will be used to interact
with course instructors and receive help. **Students are expected be signed in
and checking slack at least twice a week.**

.. attention::

   Everyone please click on the Slack link above and request access


Key Prerequisites
-----------------

This course assumes familiarity with the Python programming language and strong
working knowledge of basic, high-level language programming concepts including:
data structures, loops and flow control, and functions. We also assume a basic,
working knowledge of the Linux command line.

We will briefly review programming concepts in `Linux <linux_essentials.html>`_
and `Python <python_refresher.html>`_ during the first week of class, the first
homework assignment will be based on these topics, and we will make every effort
to help students who are less familiar with these concepts in Python.
Ultimately, each student is expected to and responsible for mastering this
material. This is not an introductory programming class and we will not have
time to give a comprehensive treatment of all of these topics.

You will need an SSH client and way to edit / run Python code to be successful
in this class. There are many programs available, and it does not matter much
which you  choose as long as you are comfortable using them.

**SSH Client:**

* `PuTTY <https://www.putty.org/>`_ (Windows)
* `MobaXterm <https://mobaxterm.mobatek.net/>`_ (Windows)
* `Windows Subsystem for Linux <https://docs.microsoft.com/en-us/windows/wsl/install-win10>`_ (Windows)
* Terminal (Mac, Linux)
* `iTerm <https://iterm2.com/>`_ (Mac)

**Python IDE**

* Terminal + VIM or Emacs or Nano (Mac, Linux)
* `VSCode <https://code.visualstudio.com/>`_ (Windows, Mac, Linux)
* `Atom <https://atom.io/>`_ (Windows, Mac, Linux)
* `PyCharm <https://www.jetbrains.com/pycharm/>`_ (Windows, Mac, Linux)



Additional Help
---------------

Our main goal for this class is your success. Please contact us if you need
extra help.

Joe Allen - wallen [at] tacc [dot] utexas [dot] edu

Charlie Dey - charlie [at] tacc [dot] utexas [dot] edu

Brandi Kuritz - bkuritz [at] tacc [dot] utexas [dot] edu

Joe Stubbs - jstubbs [at] tacc [dot] utexas [dot] edu
