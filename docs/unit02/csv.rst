CSV Reference
=============

This reference guide is designed to introduce you to CSV format. CSV (Comma
Separated Value) is probably the most intuitive and most human-readable data
format we will encounter. Many spreadsheet tools (like Excel) can read and write
CSV, and many public data sets we encounter will be stored in CSV. However, there
is no data typing, no support for complex data structures, and it lacks in
versatility.


CSV Basics
----------

CSV is usually used for simple, tabular data and generally cannot be used to
represent some of the more complex data structures we will be exposed to in this
class. However, CSV is a very common data format, and you will often encounter
tabular CSV data that you want to transform into **a list of dictionaries**.

For example, consider the following valid CSV file with one header line followed
by three record lines:

.. code-block:: text

   firstname,lastname,ID
   joe,allen,123
   charlie,day,456
   joe,stubbs,789


The same CSV file could be represented by a **list of dictionaries** where the
headers are used as the keys, and the records are used as the values:

.. code-block:: json

   [
     {
       "firstname": "joe",
       "lastname": "allen",
       "id": 123
     },
     {
       "firstname": "charlie",
       "lastname": "dey",
       "id": 456
     },
     {
       "firstname": "joe",
       "lastname": "stubbs",
       "id": 789
     }
   ]

As a slight modification of the above, you will often see that list of
dictionaries stored as a **value** and assigned to a **key** with a descriptive
name. We will see this exact type of data structure many times this semester:

.. code-block:: json

   {
     "instructors": [
       {
         "firstname": "joe",
         "lastname": "allen",
         "id": 123
       },
       {
         "firstname": "charlie",
         "lastname": "dey",
         "id": 456
       },
       {
         "firstname": "joe",
         "lastname": "stubbs",
         "id": 789
       }
     ]
   }

The latest specification for CSV provides the following guidelines that "seem to
be followed by most implementations" of CSV:

1. Each record is located on a separate line with a line break
2. The last record in the file may or may not have a line break
3. There maybe an optional header line appearing as the first line of the file
   with the same format as normal record lines
4. Within the header and each record, there may be one or more fields, separated
   by commas.  Each line should contain the same number of fields throughout the
   file
5. Each field may or may not be enclosed in double quotes
6. Fields containing line breaks, double quotes, and commas should be enclosed
   in double-quotes
7. If double-quotes are used to enclose fields, then a double-quote appearing
   inside a field must be escaped by preceding it with another double quote

Source: https://datatracker.ietf.org/doc/html/rfc4180

Keep an eye out for similar formats, too. For example ``.tsv`` files generally
are the exact same thing as ``.csv`` files except tab-delimited rather than
comma-delimited.

.. note::

   Check out the list of meteorite landing sites we worked with in the JSON
   section, but now in CSV format
   `here <https://raw.githubusercontent.com/tacc/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.csv>`_.


Read CSV from File
------------------

Imagine you have a CSV file with headers, e.g.:

.. code-block:: text

   name,id,recclass,mass (g),reclat,reclong,GeoLocation
   Ruiz,10001,L5,21,50.775,6.08333,"(50.775, 6.08333)"
   Beeler,10002,H6,720,56.18333,10.23333,"(56.18333, 10.23333)"
   Brock,10003,EH4,107000,54.21667,-113,"(54.21667, -113.0)"
   Hillebrand,10004,Acapulcoite,1914,16.88333,-99.9,"(16.88333, -99.9)"
   Mitchell,10005,L6,780,-33.16667,-64.95,"(-33.16667, -64.95)"
   ... etc

To read it into a dictionary object, use the ``csv`` module that is part of the
Python3 standard library:

.. code-block:: python3
   :linenos:

   import csv

   data = {}
   data['meteorite_landings'] = []

   with open('Meteorite_Landings.csv', 'r') as f:
       reader = csv.DictReader(f)
       for row in reader:
           data['meteorite_landings'].append(dict(row))

In the above case, each row of the CSV file is iterated one by one. The keys from
the header line are assigned values from the subsequent lines, then they are appended
to a list of dictionaries in the ``data`` data structure.


Write CSV to File
-----------------

Given the same data structure as in the previous example (a list of dictionaries
where each dictionary has identical keys), to write that object to a CSV file,
use the following:

.. code-block:: python3
   :linenos:

   import csv
   import json

   data = {}

   with open('Meteorite_Landings.json', 'r') as f:
       data = json.load(f)

   with open('Meteorite_Landings.csv', 'w') as o:
       csv_dict_writer = csv.DictWriter(o, data['meteorite_landings'][0].keys())
       csv_dict_writer.writeheader()
       csv_dict_writer.writerows(data['meteorite_landings'])



Additional Resources
--------------------

* `CSV Basics <https://en.wikipedia.org/wiki/Comma-separated_values>`_
* `CSV Module in the Python Standard Library <https://docs.python.org/3.6/library/csv.html?highlight=csv#module-csv>`_
