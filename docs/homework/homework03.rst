Homework 03
===========

**Due Date: Thursday, Feb 17, by 11:00am CST**

2001: A Space Turbidity
-----------------------

Scenario: Your robot has finished collecting its five meteorite samples and has
taken them back to the Mars lab for analysis. In order to analyze the samples,
however, you need clean water. You must check the latest water quality data to
assess whether it is safe to analyze samples, or if the Mars lab should go on a
boil water notice.



PART 1
~~~~~~

For the first part of this homework, download the water quality data set from
`this link <https://raw.githubusercontent.com/wjallen/turbidity/main/turbidity_data.json>`_.
The data is a JSON dictionary with one key, ``'turbidity_data'``, whose value is
a time series list of dictionaries. Each dictionary in the list has the same set
of keys. A sample of the data looks like:

.. code-block:: console

   {
     "turbidity_data": [
      {
        "datetime": "2022-02-01 00:00",
        "sample_volume": 1.19,
        "calibration_constant": 1.022,
        "detector_current": 1.137,
        "analyzed_by": "C. Milligan"
      },
      {
        "datetime": "2022-02-01 01:00",
        "sample_volume": 1.15,
        "calibration_constant": 0.975,
        "detector_current": 1.141,
        "analyzed_by": "C. Milligan"
      },
    ... etc

* **DO NOT** commit this data set to your homework repository
* **DO** provide instructions in your README on how to download this data set

.. note::

   Turbidity is caused by particles suspended or dissolved in water that scatter
   light making the water appear cloudy or murky.
   `Source1 <https://www.pca.state.mn.us/sites/default/files/wq-iw3-21.pdf>`_



PART 2
~~~~~~

Write a Python3 script that reads in the water quality data set, and prints three
key pieces of information to screen: (1) the current water turbidity (taken as the
average of the most recent five data points), (2) whether that turbidity is below
a safe threshold, and (3) the minimum time required for turbidity to fall below
the safe threshold (if it is already below the safe threshold, the script would
report 0 hours). Here are the requirements for this Python3 script:

* The script must have an appropriate ``main()`` function that is only called
  when executing your script directly
* The script must have a minimum of two additional functions:

  * A function to calculate turbidity using equation 1 below
  * A function to calculate minimum time to fall below threshold turbidity using
    equation 2 below

* The turbidity threshold for safe water is a constant, ``1.0`` NTU
* The decay factor per hour is a constant, 2% or ``0.02`` expressed as a decimal
* Each function (except ``main()``) must have a concise docstring containing a
  description of the function, arguments, and return values following the format in the
  `Google Style Guide <https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings>`_
* Each function definition (except ``main()``) must contain type hints
* The second line of the output (see examples below) must come from a log message
  using the ``logging`` module


**Equation 1:** The equation we will use for turbidity is based on readings taken by a
nephelometer (`Source2 <https://www.fondriest.com/environmental-measurements/measurements/measuring-water-quality/turbidity-sensors-meters-and-methods/>`_):

.. code-block:: text

   T = a0 * I90
   T = Turbidity in NTU Units (0 – 40)
   a0 = Calibration constant
   I90 = Ninety degree detector current


**Equation 2:** The equation we will use for minimum time to return below a safe threshold is
expressed as an inequality, and it is a standard exponential decay function:

.. code-block:: text

   Ts > T0(1-d)**b
   Ts = Turbidity threshold for safe water
   T0 = Current turbidity
   d = decay factor per hour, expressed as a decimal
   b = hours elapsed

When executing this script, the output might look similar to one of the following
two code blocks, depending on whether turbidity is above or below the safe threshold:

.. code-block:: text

   Average turbidity based on most recent five measurements = 1.1992 NTU
   Warning: Turbidity is above threshold for safe use
   Minimum time required to return below a safe threshold = 8.99 hours

.. code-block:: text

   Average turbidity based on most recent five measurements = 0.9852 NTU
   Info: Turbidity is below threshold for safe use
   Minimum time required to return below a safe threshold = 0 hours



PART 3
~~~~~~

In a new Python3 script, write unit tests to test your functions described above.
The test script must be appropriately named and prefixed with ``test_``. The test
script must also work with ``pytest``. There must be a minimum of **five** tests
associated with each function, some of which should perform simple sanity checks
that the math is correct, and others should perform more complicated checking
including that types returned and exceptions thrown match what are expected.



PART 4
~~~~~~

The homework must also include a README file. The README should be descriptive,
use proper grammar, and contain enough instructions so anyone else could clone
the repository and figure out what the script does and how to run it. General
guidelines to follow for the README are:

* Descriptive title
* High-level description of the folder contents / project objective. I.e. why
  does this exist and why is it important? (2-3 sentences)
* Instructions on how to download the data set from the original source
* Specific description of the python script (1-2 sentences)
* Instructions to run the code from start to finish, plus how to interpret the
  results (2-3 sentences) (Example output would help a lot to explain how to
  interpret the results)
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
homework 03:

.. code-block:: text
   :emphasize-lines: 6-9

   my-coe332-hws/
   ├── homework01
   │   └── ...
   ├── homework02
   │   └── ...
   ├── homework03
   │   ├── analyze_water.py       # your file names may vary
   │   ├── README.md
   │   └── test_analyze_water.py
   └── README.md

There is no need to email the link to your homework repo again, as we should have
it on file from the first homework. We will re-clone the same repo as before at the
due date / time for evaluation.



Additional Resources
--------------------

* `Water quality data <https://raw.githubusercontent.com/wjallen/turbidity/main/turbidity_data.json>`_
* `Water turbidity equations <https://www.fondriest.com/environmental-measurements/measurements/measuring-water-quality/turbidity-sensors-meters-and-methods>`_
* `Google style guide <https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings>`_
