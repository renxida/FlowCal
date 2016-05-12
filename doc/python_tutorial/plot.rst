Plotting Flow Cytometry Data
============================

This tutorial focuses on how to plot flow cytometry data using ``FlowCal``, particularly by using the module :mod:`FlowCal.plot`

To start, navigate to the ``examples/FCFiles`` directory included with FlowCal, and open a ``python`` session therein. Then, import ``FlowCal`` as with any other python module.

>>> import FlowCal

Also, import ``pyplot`` from ``matplotlib``

>>> import matplotlib.pyplot as plt

Histograms
----------

Let's load the data from file ``data_001.fcs`` into an ``FCSData`` object called ``s``.

>>> s = FlowCal.io.FCSData('data_001.fcs')

One is often interested in the fluorescence distribution across a population of cells. This is represented in a histogram. Since ``FCSData`` is a numpy array, one could use the standard ``hist`` function included in matplotlib. Alternatively, ``FlowCal`` includes its own histogram function specifically tailored to work with ``FCSData`` objects. For example, one can plot the contents of the ``FL1`` channel with a single call to :func:`FlowCal.plot.hist1d`.

>>> FlowCal.plot.hist1d(s, channel='FL1')
>>> plt.show()

.. image:: https://www.dropbox.com/s/4lbmhv2gk0dmcx0/python_tutorial_plot_2.png?raw=1

:func:`FlowCal.plot.hist1d` behaves mostly like a regular matplotlib plotting function: it will plot in the current figure and axis. The axes labels are populated by default, but one can still use ``plt.xlabel`` and ``plt.ylabel`` to change them.

By default, :func:`FlowCal.plot.hist1d` uses 256 uniformly spaced bins. We can override the default bins using the ``bins`` argument. Let's try using 1024 bins.

>>> FlowCal.plot.hist1d(s, channel='FL1', bins=1024)
>>> plt.show()

.. image:: https://www.dropbox.com/s/7rmt5maedht22d0/python_tutorial_plot_1.png?raw=1

:func:`FlowCal.plot.hist1d` can also use bins that are logarithmically spaced. For example, let's convert the data in the ``FL1`` channel to a.u., and plot it in a semilog histogram.

>>> s_fl1 = FlowCal.transform.to_rfi(s, channels='FL1')
>>> FlowCal.plot.hist1d(s_fl1, channel='FL1', xscale='log')
>>> plt.show()

.. image:: https://www.dropbox.com/s/80yeslvvwkjbrk3/python_tutorial_plot_3.png?raw=1

Finally, :func:`FlowCal.plot.hist1d` can plot several FCSData objects at the same time. Let's now load 3 FCSData objects, transform the ``FL1`` channel to a.u., and plot them with transparency.

>>> filenames = ['data_{:03d}.fcs'.format(i + 2) for i in range(3)]
>>> d = [FlowCal.io.FCSData(filename) for filename in filenames]
>>> d = [FlowCal.transform.to_rfi(di, channels='FL1') for di in d]
>>> FlowCal.plot.hist1d(d, channel='FL1', alpha=0.7, xscale='log')
>>> plt.legend(filenames)
>>> plt.show()

.. image:: https://www.dropbox.com/s/p2xpq4p9m4o4m99/python_tutorial_plot_4.png?raw=1

Note that all of these plots show bimodal fluorescence distributions.

Density Plots
-------------

It is also common to look at the forward scatter and side scatter values in a 2D histogram, scatter plot, or density diagram. From those, the user can extract size and shape information that would allow him to differentiate between cells and debris. ``FlowCal`` includes the function :func:`FlowCal.plot.density2d` for this purpose.

Let's look at the ``FSC`` and ``SSC`` channels in our sample ``s``.

>>> s_t = FlowCal.transform.to_rfi(s, channels=['FSC', 'SSC'])
>>> FlowCal.plot.density2d(s_t,
...                        channels=['FSC', 'SSC'],
...                        xscale='log',
...                        yscale='log')
>>> plt.show()

.. image:: https://www.dropbox.com/s/rq9id6rmp57hoe1/python_tutorial_plot_5.png?raw=1

The color indicates the number of events in the region, with red indicating a bigger number than yellow and blue, in that order, by default. Similarly to :func:`FlowCal.plot.hist1d`, :func:`FlowCal.plot.density2d` automatically obtains the appropriate bins from the ``FCSData`` object ``s_t``. In addition, :func:`FlowCal.plot.density2d` applies, by default, gaussian smoothing to the density plot.

:func:`FlowCal.plot.density2d` includes two visualization modes: ``mesh`` (seen above), and ``scatter``. The last one is good for distinguishing regions with few events.

>>> FlowCal.plot.density2d(s_t,
...                        channels=['FSC', 'SSC'],
...                        mode='scatter',
...                        xscale='log',
...                        yscale='log')
>>> plt.show()

.. image:: https://www.dropbox.com/s/9okm2e95sthmuam/python_tutorial_plot_6.png?raw=1

Both plots show events concentrated in the same four regions: two, at the left, with events saturating at the lowest detectable value of the ``FSC`` channel, one at the middle-lower portion of the plot, and one at the middle-upper portion. By looking at the shape of the different populations we know that only events in the last region are cells. We will learn how to "gate", or select only one population, in the :doc:`gating tutorial </python_tutorials/gate>`

Combined Histogram and Density Plots
------------------------------------

FlowCal also includes "complex plot" functions, which produce their own figure and a set of axes, and use simple ``matplotlib`` or ``FlowCal`` plotting functions to populate them.

In particular, :func:`FlowCal.plot.density_and_hist` uses :func:`FlowCal.plot.hist1d` and :func:`FlowCal.plot.density2d` to produce a combined density plot/histogram that allow the user to quickly see information about one sample. For example, let's plot the ``FSC`` and ``SSC`` channels in a density plot, and the ``FL1`` channel in a histogram. In the following, ``density_params`` and ``hist_params`` are dictionaries that are directly passed to :func:`FlowCal.plot.hist1d` and :func:`FlowCal.plot.density2d` as keyword arguments.

>>> s_t = FlowCal.transform.to_rfi(s, channels=['FSC', 'SSC', 'FL1'])
>>> FlowCal.plot.density_and_hist(s_t,
...                               density_channels=['FSC', 'SSC'],
...                               density_params={'xscale':'log',
...                                               'yscale':'log',
...                                               'mode':'scatter'},
...                               hist_channels=['FL1'],
...                               hist_params={'xscale':'log'})
>>> plt.tight_layout()
>>> plt.show()

.. image:: https://www.dropbox.com/s/1vq4bfhrj7k2vkz/python_tutorial_plot_7.png?raw=1

:func:`FlowCal.plot.density_and_hist` can also plot data before and after applying gates. We will see this in the :doc:`gating tutorial </python_tutorial/gate>`.

Other Plotting Functions
------------------------
These are not the only functions in :mod:`FlowCal.plot`. For more information, consult the API reference.