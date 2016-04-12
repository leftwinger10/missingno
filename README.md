# missingno

Messy datasets? Missing values? `missingno` provides a small toolset of flexible and easy-to-use missing data
visualizations and utilities that allows you to get a quick visual summary of the completeness (or lack thereof) of
your dataset. It's built using `matplotlib`, so it's fast, and takes any `pandas.DataFrame` input that you throw at
it, so it's flexible. Just `pip install missingno` to get started.

# Quickstart

All examples use the [NYPD Motor Vehicle Collisions Dataset](https://data.cityofnewyork.us/Public-Safety/NYPD-Motor-Vehicle-Collisions/h9gi-nx95).

## Matrix

First up, the `missingno` nullity matrix is a data-dense display which lets you quickly visually pick out patterns in
 data completion:

    >>> import missingno as msno
    >>> msno.matrix(data.sample(250))

![alt text][two_hundred_fifty]

[two_hundred_fifty]: http://i.imgur.com/DdepYwr.png

At a glance, date, time, the distribution of injuries, and the contribution factor of the first vehicle appear to be
completely populated, while geographic information seems mostly complete, but spottier.

The sparkline at right summarizes the general shape of the data completeness and points out the maximum and minimum
rows.

## Heatmap

The missingno correlation heatmap lets you measure how strongly the presence of one variable positively or negatively
affect the presence of another:

    >>> msno.heatmap(data)

![alt text][heatmap]

[heatmap]: http://i.imgur.com/QeeW9Hz.png

Hmm. It seems that reports which are filed with an `OFF STREET NAME` variable are less likely to have complete
geographic data.

**Caution**: The heatmap will *not* work with variables which have a variance of zero (that is, they
are always filled or always empty). This is due to the fact that for such entries, correlation is meaningless. These
variables are silently removed from the analysis&mdash;in this case for instance the datetime and injury
number columns, which are completely filled, are not included.

The heatmap works great for picking out data completeness relationships between variable pairs, but its visual power
is limited when it comes to larger relationships.


## Dendrogram

The dendrogram allows you to more fully correlate variable completion, revealing trends deeper than the pairwise
ones visible in the correlation heatmap:

    >>> msno.dendrogram(dat)

![alt text][dendrogram]

[dendrogram]: http://i.imgur.com/6ZBC4af.png

## Sorting and filtering

`missingno` also provides utility functions for filtering records in your dataset based on completion. These are
useful in particular for filtering through and drilling down into particularly large datasets whose data missingness
issues might otherwise be very hard to visualize or understand.

Let's first apply a `nullity_filter()` to the data. The `filter` parameter controls which result set we
want: either `filter=top` or `filter=bottom`. The `n` parameter controls the maximum number of columns that you want:
 so for example `n=5` makes sure we get *at most* five results. Finally, `p` controls the percentage cutoff. If
 `filter=bottom`, then `p=0.9`  makes sure that our columns are *at most*  90% complete; if `filter=top` we get
 columns which are *at least* 90% complete.

For example, the following query filtered down to only at most 15 columns which are not completely filled.

    >>> filtered_data = msno.nullity_filter(dat, filter='bottom', n=15, p=0.999) # or filter='top'
    >>> msno.matrix(filtered_data.sample(250))

![alt text][matrix_filtered]

[matrix_filtered]: http://i.imgur.com/UF6hmL8.png

`nullity_sort()` simply reshuffles your rows by completeness, in either `ascending` or `descending` order. Since it
doesn't affect the underlying data it's mainly useful for `matrix` visualization:


    >>> sorted_data = msno.nullity_sort(data, sort='descending') # or sort='ascending'
    >>> msno.matrix(sorted_data.sample(250))

![alt text][matrix_sorted]

[matrix_sorted]: http://i.imgur.com/qL6zNQj.png

One final note. These methods also work inline within the visualization methods themselves. For instance, the
following is perfectly valid:

    >>> msno.matrix(data.sample(250), filter='top', n=5, p=0.9, sort='ascending')

# Going further

`readthedocs` documentation for `missingno` is currently a work in progress. In the meantime to see some of the
lesser configuration parameters available [take a look at the source code](https://github.com/ResidentMario/missingno/blob/master/missingno/missingno.py).

If you're interested in learning more about working with missing data in Python check out [my tutorial on the
subject](http://nbviewer.jupyter.org/github/ResidentMario/python-missing-data/blob/master/missing-data.ipynb).

For more on this module's ideation check out [this post on my personal blog](http://www.residentmar.io/2016/03/28/missingno.html).



# Contributing

Bugs? Thoughts? Feature requests? [Throw them at the bug tracker and I'll take a look](https://github.com/ResidentMario/missingno/issues).

As always I'm very interested in hearing feedback: you can also reach out to me at `aleksey@residentmar.io`.