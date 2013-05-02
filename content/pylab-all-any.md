Title: IPython with PyLab Breaks Built-ins like any() and all()
Category: Tips
Date: 2013-01-23

Ipython's PyLab breaks some uses of any(), all(), and possibly other built-in
functions.  Here's a normal IPython shell:

    :::python
    >ipython
    In [1]: all([x for x in [0,1,2]])
    Out[1]: False

    In [2]: all(x for x in [0,1,2])
    Out[2]: False

    In [3]: all
    Out[3]: <function all>

And here's IPython with the pylab flag:

    :::python
    >ipython --pylab
    In [1]: all([x for x in [0,1,2]])
    Out[1]: False

    In [2]: all(x for x in [0,1,2])
    Out[2]: True

    In [3]: all
    Out[3]: <function numpy.core.fromnumeric.all>

Why is `all(x for x in [0,1,2])` true? Pylab replaces Python's all() with Numpy's
all(), and Numpy's all() doesn't know what to do with a generator expression,
so it turns it into a numpy array of size one containing a reference to the
generator object. Put another way, it basically runs this code:

    generator = (x for x in [0,1,2])
    np.all([generator])

The generator never runs, but Numpy's all does look at the truthiness of the
generator object. Since generator is truthy, `np.all(x for x in [0,1,2])`
returns True.

According to one of the IPython developers, this is
"[expected behavior](https://github.com/ipython/ipython/issues/2598)". Yay?
A bug was filed and closed for [Python](http://bugs.python.org/issue11221) and
[PyLab](https://github.com/matplotlib/matplotlib/issues/194)
