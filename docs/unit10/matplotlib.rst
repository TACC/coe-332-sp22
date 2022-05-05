Plotting with Matplotlib
========================


What is Matplotlib
------------------


It’s a graphing library for Python. It has a nice collection of tools that you can use to
create anything from simple graphs, to scatter plots, to 3D graphs. It is used heavily in 
the scientific Python community for data visualization.


 Let's jump in
 -------------

 Let's plot a simple sin wave

.. code-block:: python3
    :linenos:

    import matplotlib.pyplot as plt
    import numpy as np

    x = np.linspace(0, 2*np.pi, 50)
    plt.plot(x, np.sin(x))
    plt.show() # we can't do this on our VM server
    plt.savefig('my_sinwave.png')


we can keep plotting! Let's plot 2 graphs on the same axis

.. code-block:: python3
    :linenos:

    plt.plot(x, np.sin(x), np.sin(2*x))
    plt.show()
    plt.savefig('my_sinwavex2.png')

why stop now?
Let's make the plot easier to read

.. code-block:: python3
    :linenos:

    plt.plot(x, np.sin(x), 'r-o', x, np.sin(2*x), 'g--'
    plt.show()
    plt.savefig('my_sinwavex2a.png')

other color combinations:

Colors:

* Blue – ‘b’

* Green – ‘g’

* Red – ‘r’

* Cyan – ‘c’

* Magenta – ‘m’

* Yellow – ‘y’

* Black – ‘k’ (‘b’ is taken by blue so the last letter is used)

* White  – ‘w’


Lines and markers:

* Lines:

	* Solid Line – '-'

	* Dashed – '–'

	* Dotted – '.'

	* Dash-dotted – '-:'

* Often Used Markers:

	* Point – '.'

	* Pixel – ','

	* Circle – 'o'

	* Square – 's'

	* Triangle – '^'



Subplots
--------

using the subplot() function, we can plot two graphs at the same time within the same "canvas".
Think of the subplots as "tables", each subplot is set with the number of rows, the number of columns, 
and the active area, the active areas are numbered left to right, then up to down.

.. code-block:: python3
    :linenos:

    plt.subplot(2, 1, 1) # (row, column, active area)
    plt.plot(x, np.sin(x))
    plt.subplot(2, 1, 2) # switch the active area
    plt.plot(x, np.sin(2*x))
    plt.show()
    plt.savefig('my_sinwavex2b.png')


Scatter plots
-------------

.. code-block:: python3
    :linenos:

    y = np.sin(x)
    plt.scatter(x,y)
    plt.show()
    plt.savefig('my_scattersin.png')

Let's mix things up, using random numbers and add a colormap to a scatter plot

.. code-block:: python3
    :linenos:

    x = np.random.rand(1000)
    y = np.random.rand(1000)
    size = np.random.rand(1000) * 50
    color = np.random.rand(1000)
    plt.scatter(x, y, size, color)
    plt.colorbar()
    plt.show()
    plt.savefig('my_scatterrandom.png')

We brought in two new parameters, size and color, which will vary the diameter and the 
color of our points. Then adding the colorbar() gives us a nice color legend to the side.


Histograms
----------

A histogram is one of the simplest types of graphs to plot in Matplotlib. All you need to do is pass the hist() 
function an array of data. The second argument specifies the amount of bins to use. Bins are intervals of values 
that our data will fall into. The more bins, the more bars.

.. code-block:: python3
    :linenos:

    plt.hist(x, 50)
    plt.show()
    plt.savefig('my_histrandom.png')


Adding Labels and Legends
-------------------------

.. code-block:: python3
    :linenos:

    x = np.linspace(0, 2 * np.pi, 50)
    plt.plot(x, np.sin(x), 'r-x', label='Sin(x)')
    plt.plot(x, np.cos(x), 'g-^', label='Cos(x)')
    plt.legend() # Display the legend.
    plt.xlabel('Rads') # Add a label to the x-axis.
    plt.ylabel('Amplitude') # Add a label to the y-axis.
    plt.title('Sin and Cos Waves') # Add a graph title.
    plt.show()
    plt.savefig('my_labels_legends')


Redis and plots
---------------

you can "save" your plots to Redis, however the maximum size for a key/value is 512 mb
and the sum of all your data (including files) must fit into main memory on the Redis server.

.. code-block:: python3
    :linenos:

    import redis
    rd = redis.StrictRedis(host='172.17.0.1', port=6379, db=0)

    # read the raw file bytes into a python object
    file_bytes = open('/tmp/myfile.png', 'rb').read()

    # set the file bytes as a key in Redis
    rd.set('key', file_bytes)




