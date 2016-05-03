[Pandas](http://pandas.pydata.org) is a library for working with messy datasets that are big enough to fit in memory, but it's important to remember the performance overhead.

Luckily Pandas exposes a raw numpy interface so we can bypass some of that overhead when it's not necessary. For example we can use the
[values](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.values.html#pandas.Series.values) attribute on a [Series object](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html#pandas.Series) to speed up this operation by an order of magnitude (your mileage may vary).


```python
    import time
    import bumpy
    import pandas


    def main():
        df = pandas.DataFrame()

        # We're only dealing with a single column in our profiling but assume we're
        # dealing with code where we get passed a big data frame
        df['a'] = numpy.random.random_integers(0, 10, size=1000000)
        df['b'] = numpy.random.random_integers(0, 10, size=1000000)
        df['c'] = numpy.random.random_integers(0, 10, size=1000000)

        s = time.time()
        df['a'] == 5
        print 'Time for DF slice', time.time() - s

        s = time.time()
        df['a'].values == 5
        print 'Time for ndarray slice', time.time() - s

        # This is also applicable if you want to get the union of several columns.
        # Save the lining up of the indexes, etc. until the last step instead of
        # incurring that overhead on each slice.
        s = time.time()
        df_slices = df[(df['a'] == 1) & (df['b'] == 1) & (df['c'] == 1)]
        print 'Time for DF slice', time.time() - s

        s = time.time()
        slice_a = df['a'].values == 1
        slice_b = df['b'].values == 1
        slice_c = df['c'].values == 1
        nd_slices = df[slice_a & slice_b & slice_c]
        print 'Time for ndarray slice', time.time() - s

        # Just double check that both methods returned same data
        pandas.util.testing.assert_frame_equal(df_slices, nd_slices)

    if __name__ == '__main__':
        main()
```


Time for DF slice 0.01664686203

Time for ndarray slice 0.00168609619141


This technique can also be useful to slice by a bunch of columns at once and then take the rows they have in common from the data frame.