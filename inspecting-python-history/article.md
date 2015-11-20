One of the awesome things about a project like [Python](http://python.org) being
open source is that anyone can go look at the code and find a 'paper' trail
detailing the reasons for almost every line of code. I personally don't take
advantage of this enough.

Recently I stumbled across a great opportunity to dive into the
[source](http://hg.python.org/cpython). The source is a great way to to track
down how *and* why of any implementation detail or behavior.

## Discovery phase

First, I spent some time thinking about how
[iterators](http://docs.python.org/2/library/stdtypes.html#iterator-types) and
[count](http://docs.python.org/2/library/itertools.html#itertools.count) work.
I came up with the following little snippet:

[gist 4158116]

This lead me to thinking about the real implementation of pieces of the
[collections module](http://docs.python.org/3.3/library/collections.html),
specifically
[count](http://docs.python.org/2/library/itertools.html#itertools.count) and
[Counter](http://docs.python.org/3.3/library/collections.html#counter-objects).

I was quickly side-tracked by the following snippet in the
[collections module](http://hg.python.org/cpython/file/29627bd5b333/Lib/collections.py#l532):


```python
    def subtract(self, iterable=None, **kwds):
        if iterable is not None:
            self_get = self.get
            if isinstance(iterable, Mapping):
                for elem, count in iterable.items():
                    self[elem] = self_get(elem, 0) - count
            else:
                for elem in iterable:
                    self[elem] = self_get(elem, 0) - 1
        if kwds:
            self.subtract(kwds)
```

The most interesting line is `self_get = self.get`. What is the point of this?

I assume this is performance related because
[attribute lookups](http://www.cafepy.com/article/python_attributes_and_methods/python_attributes_and_methods.html#simple-attribute-access-example) can be quite
expensive. So, avoiding the call to `self.get` in a tight loop could be
beneficial, but how can I fact-check my assumption?

## Use the history

To follow along and look through the history you need to have the
[Hg repository](http://mercurial.selenic.com/) locally:

1. Get source:
    - `hg clone http://hg.python.org/cpython`
2. Update source to version we want to inspect:
    - `hg update 3.3`

Now find what revision the suspicious lines were last touched in:

`hg annotate -u -n Lib/collections/__init__.py`

The above command is the [Hg](http://mercurial.selenic.com/) equivalent of the
'blame' concept from [git](http://git-scm.com/)
and [subversion](http://subversion.apache.org/). This command is essential
when you need to know the author of each line and the revision it was last
updated.

After running the above `hg annotate` command you can see the first of these
lines was last updated in revision **54985**. So, what information did the
author leave behind when this revision was committed:

`hg log -p -r 54985`

This gives us the commit message and changes introduced by this revision:

    summary: Issue 6370: Performance issue with collections.Counter()

The key element here is **Issue 6370**. This refers to an issue in the official
[Python bug tracking system](http://bugs.python.org). From here it's easy,
just search for that issue number on the site. You will find an
[explanation](http://bugs.python.org/issue6370) of the 'bug' and the change
associated with it.

## Mystery solved

Turns out it was performance related after all, replacing `self[elem]` with
something like `self.get(elem, 0)` is faster.

However, calling `get()` is not ideal either because
[calling Python functions is expensive](http://www.doughellmann.com/articles/misc/dict-performance/index.html).

The moral of the story? When in doubt, go to the source and search the
history. The majority of the time the documentation trail is very useful and
accessible with just a little bit of work.

