Working with JSON
=================

In this hands-on module, we will learn how to work with the JSON data format.
JSON (JavaScript Object Notation) is a powerful, flexible, and lightweight data
format that we see a lot throughout this course, especially when working with
web apps and REST APIs.

After going through this module, students should be able to:

* Identify and write valid JSON
* Read JSON into an object in a Python3 script
* Loop over and work with elements in a JSON object
* Write JSON to file from a Python3 script


JSON Basics
-----------

Analogous to Python3 dictionaries, JSON is typically composed of key:value pairs.
The universality of this data structure makes it ideal for exchanging
information between programs written in different languages and web apps. A
simple, valid JSON  object may look like this:

.. code-block:: json

   {
     "key1": "value1",
     "key2": "value2"
   }

Although less common in this course, simple arrays of information (analogous to
Python3 lists) are also valid JSON:

.. code-block:: JSON

   [
     "thing1", "thing2", "thing3"
   ]

JSON offers a lot of flexibility on the placement of white space and newline
characters. Types can also be mixed together, forming complex data structures:

.. code-block:: JSON

   {
     "department": "COE",
     "number": 332,
     "name": "Software Engineering and Design",
     "inperson": true,
     "finalgroups": null,
     "instructors": ["Joe", "Charlie", "Joe"],
     "prerequisites": [
       {"course": "COE 322", "instructor": "Victor"},
       {"course": "SDS 322", "instructor": "Victor"}
     ]
   }

On the class server, navigate to your home directory and make a new folder for
this module:

.. code-block:: console

   [local]$ ssh username@isp02.tacc.utexas.edu
   (enter password)
   (enter token)
   [isp02]$ cd coe-332/
   [isp02]$ mkdir working-with-json && cd working-with-json


Download this sample JSON files into that folder using the ``wget`` command, or
click `this link <https://raw.githubusercontent.com/TACC/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.json>`_
and cut and paste the contents into a file called ``Meteorite_Landings.json``:

.. code-block:: console

   [isp02]$ wget https://raw.githubusercontent.com/TACC/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.json



.. note::

   The Meteorite Landing data is adapted from a data set provided by The
   Meteoritical Society here: https://data.nasa.gov/Space-Science/Meteorite-Landings/gh4g-9sfh


EXERCISE
~~~~~~~~

Plug this file (or some of the above samples) into an online JSON validator
(e.g. `JSONLint <https://jsonlint.com/>`_). Try making manual changes to some of
the entries to see what breaks the JSON format.



Read JSON into a Python3 Script
-------------------------------

The ``json`` Python3 library is part of the Python3 Standard Library, meaning it
can be imported without having to be installed by pip. Start editing a new
Python3 script using your method of choice:

.. code-block:: console

    [isp02]$ vim json_ex.py


.. warning::

   Do not name your Python3 script "json.py". If you ``import json`` when there
   is a script called "json.py" in the same folder, it will import that instead
   of the actual ``json`` library.

The code you need to read in the JSON file of state names and abbreviations into
a Python3 object is:

.. code-block:: python3
   :linenos:

   import json

   with open('Meteorite_Landings.json', 'r') as f:
       ml_data = json.load(f)

Only three simple lines! We ``import json`` from the standard library so that we
can work with the ``json`` class. We use the safe ``with open...`` statement to
open the file we downloaded read-only into a filehandle called ``f``. Finally,
we use the ``load()`` method of the ``json`` class to load the contents of the
JSON file into our new ``ml_data`` object.

EXERCISE
~~~~~~~~

Try out some of these calls to the ``type()`` function on the new ``ml_data``
object that you loaded. Also ``print()`` each of these as necessary to be sure
you know what each is. Be able to explain the output of each call to ``type()``
and ``print()``.

.. code-block:: python3
   :linenos:

   import json

   with open('Meteorite_Landings.json', 'r') as f:
       ml_data = json.load(f)

   type(ml_data)
   type(ml_data['meteorite_landings'])
   type(ml_data['meteorite_landings'][0])
   type(ml_data['meteorite_landings'][0]['name'])

   print(ml_data)
   print(ml_data['meteorite_landings'])
   print(ml_data['meteorite_landings'][0])
   print(ml_data['meteorite_landings'][0]['name'])

.. tip::

   Consider doing this in the Python3 interpreter's interactive mode instead of
   in a script.

Work with JSON Data
-------------------

As we have seen, the JSON object we loaded contains meteorite landing data
including names, ids, classes, masses, latitudes, and longitudes. Let's write a
few functions to help us explore the data.

First, write a function to calculate the average mass of all meteorites in the
data set. Call that function, and have it print the average mass to screen.

.. code-block:: python3
   :linenos:
   :emphasize-lines: 3-7,12

   import json

   def compute_average_mass(a_list_of_dicts, a_key_string):
       total_mass = 0.
       for i in range(len(a_list_of_dicts)):
           total_mass += float(a_list_of_dicts[i][a_key_string])
       return (total_mass / len(a_list_of_dicts))

   with open('Meteorite_Landings.json', 'r') as f:
       ml_data = json.load(f)

   print(compute_average_mass(ml_data['meteorite_landings'], 'mass (g)'))




Next, write a function to check where on the globe the meteorite landing site is
located. We need to check whether it is Northern or Southern hemisphere, and
whether it is Western or Eastern hemisphere.

.. code-block:: python3
   :linenos:
   :emphasize-lines: 9-19,26-27

   import json

   def compute_average_mass(a_list_of_dicts, a_key_string):
       total_mass = 0.
       for i in range(len(a_list_of_dicts)):
           total_mass += float(a_list_of_dicts[i][a_key_string])
       return (total_mass / len(a_list_of_dicts))

   def check_hemisphere(latitude: float, longitude: float) -> str:    # type hints
       location = ''
       if (latitude > 0):
           location = 'Northern'
       else:
           location = 'Southern'
       if (longitude > 0):
           location = f'{location} & Eastern'
       else:
           location = f'{location} & Western'
       return(location)

   with open('Meteorite_Landings.json', 'r') as f:
       ml_data = json.load(f)

   print(compute_average_mass(ml_data['meteorite_landings'], 'mass (g)'))

   for row in ml_data['meteorite_landings']:
       print(check_hemisphere(float(row['reclat']), float(row['reclong'])))


.. note::

   Type hints in function definitions indicate what types are expected as input
   and output of a function, but no checking actually happens at runtime. Think
   of them as documentation or annotations.

.. tip::

   Check out Python3 ternary operators to make your if/else conditionals shorter,
   but perhaps a little less intuitive to read.

   .. code-block:: python3

      def check_hemisphere(lat, lon):
          location = 'Northern' if (lat > 0) else 'Southern'
          location = f'{location} & Eastern' if (lon > 0) else f'{location} & Western'
          return(location)


EXERCISE
~~~~~~~~

Write a third function to count how many of each 'class' of meteorite there is
in the list. The output should look something like:

.. code-block:: console

   type, number
   H, 1
   H4, 2
   L6, 6
   ...etc



Write JSON to File
------------------

Finally, in a new script, we will create an object that we can write to a new
JSON file.

.. code-block:: python3
   :linenos:

   import json

   data = {}
   data['class'] = 'COE332'
   data['title'] = 'Software Engineering and Design'
   data['subjects'] = []
   data['subjects'].append( {'unit': 1, 'topic': ['linux', 'python3', 'git']} )
   data['subjects'].append( {'unit': 2, 'topic': ['json', 'csv', 'xml', 'yaml']} )

   with open('class.json', 'w') as out:
       json.dump(data, out, indent=2)

Notice that most of the code in the script above was simply assembling a normal
Python3 dictionary. The ``json.dump()`` method only requires two arguments - the
object that should be written to file, and the filehandle. The ``indent=2``
argument is optional, but it makes the output file looks a little nicer and
easier to read.

Inspect the output file and paste the contents into an online JSON validator.

EXERCISE
~~~~~~~~

Write a new Python3 script to read in ``Meteorite_Landings.json``, convert the
ids, masses, latitudes, and longitudes to floats, then save it as a new JSON
file called ``Meteorite_Landings_updated.json``. Compare them side by side to
make sure you can see and understand the difference.


Additional Resources
--------------------

* `Reference for the JSON library <https://docs.python.org/3.6/library/json.html>`_
* `Validate JSON with JSONLint <https://jsonlint.com/>`_
* `Meteorite Landings Data <https://data.nasa.gov/Space-Science/Meteorite-Landings/gh4g-9sfh>`_
