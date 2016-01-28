
## Pandas

I've been spending some time this week learning how
[Pandas](http://pandas.pydata.org) really works. It looks great and
seems to be widely used. Luckily, the project I'm currently working on heavily
uses [numpy](http://numpy.scipy.org/), which [Pandas](http://pandas.pydata.org)
is based on.

I decided to try replacing some ugly custom code with Pandas awesome
[indexing](http://pandas.pydata.org/pandas-docs/stable/indexing.html) and
[merging](http://pandas.pydata.org/pandas-docs/stable/merging.html)
functionality. In short, it's was possible, fast, and all around great.

Now, it's obvious the folks behind [Pandas](http://pandas.pydata.org) are
really smart. However, the purpose of this post was really to point out an
awesome little detail that you might not notice at first when mixing
[Pandas](http://pandas.pydata.org) and [numpy](http://numpy.scipy.org/) code.

[code lang="python"]
    >>> import pandas
    >>> import bumpy
    >>> x = numpy.arange(0, 5)
    >>> s = pandas.Series(x)
    >>> x
    array([0, 1, 2, 3, 4])
    >>> s
    0 0
    1 1
    2 2
    3 3
    4 4
    >>> s[0]
    0
    >>> s[0] = 10
    >>> x
    array([10, 1, 2, 3, 4])
[/code]

See what happened there? [Pandas](http://pandas.pydata.org) is smart enough to
*not* copy the data. As I mentioned before, [Pandas](http://pandas.pydata.org)
is based heavily on [numpy](http://numpy.scipy.org/). So, the code is smart
enough to know that copying the data is wasteful.

This might be obvious to most people, but it's a great little detail. This
makes it trivial to take existing [numpy](http://numpy.scipy.org/) code, create
a [Pandas Series](http://pandas.pydata.org/pandas-docs/stable/dsintro.html#series)
from it and do advanced
[indexing](http://pandas.pydata.org/pandas-docs/stable/indexing.html#advanced-indexing-with-labels),
[group-by](http://pandas.pydata.org/pandas-docs/stable/groupby.html), or even
[plotting](http://pandas.pydata.org/pandas-docs/stable/visualization.html).
All of this is possible without having to switch your entire code base from
[ndarrays](http://docs.scipy.org/doc/numpy/reference/generated/numpy.ndarray.html)
to [Series](http://pandas.pydata.org/pandas-docs/stable/dsintro.html#series) or
pay the penalty of copying potentially massive datasets again.

Sometimes the details matter.

## Community

Also, I wanted to point out the folks behind [Pandas](http://pandas.pydata.org)
aren't just code smart. They are super nice and helpful too! I ran into some
[issues](https://github.com/pydata/pandas/issues/2388) with using the
`reindex_like()` method and quickly received a few
[great](https://github.com/pydata/pandas/issues/2388#issuecomment-10858926)
[explanations](https://github.com/pydata/pandas/issues/2388#issuecomment-10863795)
from their developers.

In addition, I've found some really helpful
[community members](http://stackoverflow.com/users/1240268/hayden) over at
[Stackoverflow](http://stackoverflow.com) that helped me work through an
efficiency
[issue](http://stackoverflow.com/questions/13611065/efficient-way-to-apply-multiple-filters-to-pandas-dataframe-or-series/13616382).

This all just goes to show that the scientific [Python](http://python.org)
community is alive and well. Don't be afraid to ask them questions or try out
something new!

