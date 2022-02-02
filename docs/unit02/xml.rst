XML Reference
=============

This reference guide is designed to introduce you to XML syntax. XML (Extensible
Markup Language) was designed as a way to store, transmit, and reconstruct
arbitrary data sets, particularly over the internet. You'll sometimes see it
as standalone documents, or as streaming data without a definite endpoint. For
this class, we will focus on the standalone documents / datasets.



XML Basics
----------

Much like JSON, XML can be used to store arbitrary data sets in a dictionary-esque
format of key:value pairs. However, there are a few key differences between XML
and JSON:

1. XML is typeless, essentially everything is a string
2. XML documents support comments
3. XML documents require exactly one "root" element, so the data structure of
   a **dictionary with one key, whose value is a list of dictionaries** needs a slight
   modification in order to be valid JSON (as we will see below)

A valid XML document takes the form:

.. code-block:: xml

   <data>
       <key1>value1</key1>
       <key2>value2</key2>
   </data>

Keys always come as a pair of start-tag (``<key1>``) and end-tag (``</key1>``)
markups surrounding a plain text value (``value1``). In the above example, the
root-level key ``data`` was required to make this valid XML. Without it, the
keys ``key1`` and ``key2`` would be at the same root level, which is invalid.
The same data shown above in JSON format would appear as:

.. code-block:: json

   {
     "data": {
       "key1": "value1",
       "key2": "value2"
     }
   }


Things get a little tricky with our favorite data structure, a **dictionary with
one key, whose value is a list of dictionaries**. This is because of the way
XML represents lists of dictionaries. Consider the following snippet of JSON data:

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

If we try to translate this directly to XML, it would take the form:

.. code-block:: xml

    <instructors>
            <firstname>joe</firstname>
            <lastname>allen</lastname>
            <id>123</id>
    </instructors>
    <instructors>
            <firstname>charlie</firstname>
            <lastname>dey</lastname>
            <id>456</id>
    </instructors>
    <instructors>
            <firstname>joe</firstname>
            <lastname>stubbs</lastname>
            <id>789</id>
    </instructors>

The ``instructors`` key appears multiple times at the root level, once for each
element in the list. In XML, you cannot have multiple roots, even if it is the
same root repeated more than once. You need exactly one root only. A simple
trick to fix this is to create a new dictionary with one key, e.g. "data", whose
value is the other dictionary. Doing so would slightly change the XML to a valid
format:

.. code-block:: xml

   <data>
        <instructors>
                <firstname>joe</firstname>
                <lastname>allen</lastname>
                <id>123</id>
        </instructors>
        <instructors>
                <firstname>charlie</firstname>
                <lastname>dey</lastname>
                <id>456</id>
        </instructors>
        <instructors>
                <firstname>joe</firstname>
                <lastname>stubbs</lastname>
                <id>789</id>
        </instructors>
   </data>








.. note::

   Check out the list of meteorite landing sites we worked with in the JSON
   section, but now in XML format
   `here <https://raw.githubusercontent.com/tacc/coe-332-sp22/main/docs/unit02/sample-data/Meteorite_Landings.xml>`_.


Read XML from File
------------------

Here we will focus on the "document object model" for parsing XML, which means
we will read in one XML document and parse the entire thing as a whole. (This
works for reasonably small files that can fit in memory).

Note that the Python3 standard library has an XML module, but it does not have
a method for transforming XML objects to dictionaries. Since most of what we do
in this class uses JSON and dictionaries, let's instead use the ``xmltodict``
Python module which works directly in dictionary space.

.. warning::

   Install the ``xmltodict`` library before proceeding:

   .. code-block:: console

      [isp02]$ pip3 install --user xmltodict


You can read in an XML file (e.g. the Meteorite Landings data linked above) and
store it as a dictionary as follows:

.. code-block:: python3

   import xmltodict

   with open('Meteorite_Landings.xml', 'r') as f:
       data = xmltodict.parse(f.read())

Then to access the data within that dictionary, remember to include an extra key
for the root-level, which we added in to make valid XML. For example, you could
call out the first meteorite in the list with the following:

.. code-block:: python3

   print(data['data']['meteorite_landings'][0])


.. note::

   The original Meteorite Landings data had the key ``mass (g)``. The open and
   close parentheses ``()`` are invalid characters when it comes to XML tags. The
   data linked above was modified to use the key ``mass_g`` instead. Don't be
   surprised when working with datasets if you have to make manual modifications
   to the data in order to make it valid in a particular format.


Write XML to File
-----------------

As mentioned above, a dictionary must have exactly one "root" element in order
to write valid XML. The following example below assembles a dictionary with
multiple keys at the root level ("class", "title", "subjects"). In fact the
following code will yield an error:


.. code-block:: python3
   :linenos:

   import xmltodict

   data = {}
   data['class'] = 'COE332'
   data['title'] = 'Software Engineering and Design'
   data['subjects'] = []
   data['subjects'].append( {'unit': 1, 'topic': ['linux', 'python3', 'git']} )
   data['subjects'].append( {'unit': 2, 'topic': ['json', 'csv', 'xml', 'yaml']} )

   with open('class.xml', 'w') as o:
       o.write(xmltodict.unparse(data, pretty=True))

Error:

.. code-block:: text

   ValueError: Document must have exactly one root.

To get this to work, you need to modify the above script to create a new
dictionary, e.g. "root", with exactly one key, e.g. "data", whose value is the
entire ``data`` dictionary:

.. code-block:: python3
   :linenos:
   :emphasize-lines: 10-11,14

   import xmltodict

   data = {}
   data['class'] = 'COE332'
   data['title'] = 'Software Engineering and Design'
   data['subjects'] = []
   data['subjects'].append( {'unit': 1, 'topic': ['linux', 'python3', 'git']} )
   data['subjects'].append( {'unit': 2, 'topic': ['json', 'csv', 'xml', 'yaml']} )

   root = {}
   root['data'] = data

   with open('class.xml', 'w') as o:
       o.write(xmltodict.unparse(root, pretty=True))

Output:

.. code-block:: xml

   <data>
        <class>COE332</class>
        <title>Software Engineering and Design</title>
        <subjects>
                <unit>1</unit>
                <topic>linux</topic>
                <topic>python3</topic>
                <topic>git</topic>
        </subjects>
        <subjects>
                <unit>2</unit>
                <topic>json</topic>
                <topic>csv</topic>
                <topic>xml</topic>
                <topic>yaml</topic>
        </subjects>
   </data>


Additional Resources
--------------------

* `The xmltodict Library <https://github.com/martinblech/xmltodict>`_
* `XML Linter <https://xmllint.com/>`_
