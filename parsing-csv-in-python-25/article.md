I do a lot of parsing odd data file formats in my day job, and I'm relieved
when the file format is some variant of a
[csv file](https://en.wikipedia.org/wiki/Comma-separated_values). I tend to
read those files into
[namedtuples](https://docs.python.org/2.7/library/collections.html#collections.namedtuple) or
[Python dictionaries](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) using the
[csv module](https://docs.python.org/2/library/csv.html). I prefer
[namedtuples](https://docs.python.org/2.7/library/collections.html#collections.namedtuple) because I like being able to enforce a strict data layout in code, as you do with a [namedtuple](https://docs.python.org/2.7/library/collections.html#collections.namedtuple) declaration. This
is [more explicit](https://www.python.org/dev/peps/pep-0020/) than using a
[dict](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) and
spelling out the layout of the
[dict](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) in comments.

I assumed
[namedtuples](https://docs.python.org/2.7/library/collections.html#collections.namedtuple)
are much faster they are less dynamic and lack of overhead when compared to a
[dict](https://docs.python.org/2/tutorial/datastructures.html#dictionaries).
However, assuming is not a good idea when profiling or looking for
optimization. Luckily, I found this
[great article on CSV parsing](http://districtdatalabs.silvrback.com/simple-csv-data-wrangling-with-python) that reports on the performance trade-offs.

The [article](http://districtdatalabs.silvrback.com/simple-csv-data-wrangling-with-python)
is worth reading carefully because there are some nice snippets of code
sprinkled throughout.

I particularly like the clean approach to transform rows of a csv file to a
namedtuple:

```python

from collections import named tuple

fields = ("permalink","company","numEmps", "category","city","state","fundedDate", "raisedAmt","raisedCurrency","round")
FundingRecord = namedtuple('FundingRecord', fields)

def read_funding_data(path):
    with open(path, 'rU') as data:
        data.readline() # Skip the header
        reader = csv.reader(data) # Create a regular tuple reader
        for row in map(FundingRecord._make, reader):
            yield row

if __name__ == "__main__":
    for row in read_funding_data(FUNDING):
        print row
        break
```

The use of [map](https://docs.python.org/2/library/functions.html#map) along
with
[_make](https://docs.python.org/2.7/library/collections.html#collections.somenamedtuple._make)
is very succinct.