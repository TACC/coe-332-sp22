YAML Reference
==============

This reference guide is designed to introduce you to YAML syntax. YAML is
basically JSON with a couple extra features, and meant to be a little more human
readable. We will be using YAML formatted configuration files in the Docker
compose and Kubernetes sections, so it is important to become familiar with the
syntax.


YAML Basics
-----------

YAML syntax is similar to Python dictionaries, and we will usually see them as
key:value pairs. Values can include strings, numbers, booleans, null, lists,
and other dictionaries.

Previously, we saw a simple JSON object in dictionary form like:

.. code-block:: json

   {
     "key1": "value1",
     "key2": "value2"
   }

That same object in YAML looks like:

.. code-block:: yaml

   ---
   key1: value1
   key2: value2
   ...

Notice that YAML documents all start with three hyphens on the top line (``---``),
and end with an optional three dots (``...``) on the last line. Key:value pairs
are separated by colons, but consecutive key:value pairs are NOT separated by
commas.

We also mentioned that JSON supports list-like structures. YAML does too. So the
following valid JSON block:

.. code-block:: json

   [
     "thing1", "thing2", "thing3"
   ]

Appears like this in YAML:

.. code-block:: yaml

   ---
   - thing1
   - thing2
   - thing3
   ...

Elements of the same list all appear with a hyphen ``-`` at the same indent
level.

We previously saw this complex data structure in JSON:

.. code-block:: json

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

The same structure in YAML is:

.. code-block:: yaml

   ---
   department: COE
   number: 332
   name: Software Engineering and Design
   inperson: true
   finalgroups: null         # can also use ~
   instructors:
     - Joe
     - Charlie
     - Joe
   prerequisites:
     - course: COE 322
       instructor: Victor
     - course: SDS 322
       instructor: Victor
   ...

The whole thing can be considered a dictionary. The key ``instructors`` contains
a value that is a list of names, and the key ``prerequisites`` contains a value
that is a list of dictionaries. Booleans appear as ``false`` and ``true``
(lowercase only). Null / empty values appear as ``null`` or ``~``. And, as you
can see above, YAML also supports comments starting with a ``#``.

One glaring thing that is missing from the YAML file is quotation marks. In
general, you don't have to use quotes in YAML. You may use quotes to force a
number to be interpreted as a string (e.g. ``10`` will automatically be
interpreted as an integer, but ``"10"`` will be interpreted as a string).

.. note::

   Check out the list of meteorite landing sites we worked with in the JSON
   section, but now in YAML format
   `here <https://raw.githubusercontent.com/tacc/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.yaml>`_.

There is a lot more to YAML, most of which we will not use in this course. Just
know that YAML files can contain:

* Comments
* Multi-line strings / text blocks
* Multi-word keys
* Complex objects
* Special characters
* Explicitly declared types
* A mechanism to duplicate / inherit values across a document ("anchors")




If we encounter a need for any of these, we can refer to the
`official YAML syntax <https://yaml.org/spec/1.2/spec.html>`_


Read YAML from File
-------------------

.. warning::

   There is no YAML interpreter in the Python 3.6 standard libary, so we need
   to install one with pip3:

   .. code-block:: console

      [isp02]$ pip3 install --user pyyaml


Given the meteorite landing site data in YAML format, which you can download from
`this link <https://raw.githubusercontent.com/tacc/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.yaml>`_,
load it into a Python3 dictionary object using the following:

.. code-block:: python3
   :linenos:

   import yaml

   data = {}

   with open('Meteorite_Landings.yaml', 'r') as f:
       data = yaml.load(f, Loader=yaml.SafeLoader)

Very similar to the JSON module, it only requires a few simple lines then you
have a dictionary object to work with. The ``Loader=yaml.SafeLoader`` parameter
makes it so no arbitrary Python code is executed when loading in the data - this
is typically a good choice for data from untrusted sources.




Write YAML to File
------------------

In a new script create a dictionary object that we can write to a new YAML file.

.. code-block:: python3
   :linenos:

   import yaml

   data = {}
   data['class'] = 'COE332'
   data['title'] = 'Software Engineering and Design'
   data['subjects'] = []
   data['subjects'].append( {'unit': 1, 'topic': ['linux', 'python3', 'git']} )
   data['subjects'].append( {'unit': 2, 'topic': ['json', 'csv', 'xml', 'yaml']} )

   with open('class.json', 'w') as o:
       yaml.dump(data, o)

Notice that most of the code in the script above was simply assembling a normal
Python3 dictionary. The ``json.dump()`` method only requires two arguments - the
object that should be written to file, and the filehandle.

Inspect the output file and paste the contents into an online YAML validator.




Additional Resources
--------------------

* `YAML Spec <https://yaml.org/spec/1.2/spec.html>`_
* `YAML Validator <http://www.yamllint.com/>`_
* `JSON / YAML Converter <https://www.json2yaml.com/>`_
* `PyYAML Docs <https://pyyaml.org/wiki/PyYAMLDocumentation>`_
