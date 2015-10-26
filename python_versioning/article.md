# Python spelunking

## Motivation

I recently spent a little time reviewing a [Python](http://python.org) bug
report and determining if I was running with a fixed version of the
[Python](http://python.org) interpreter.  I think this is a useful exercise
for someone who is overly curious and not a core developer of the language.

This post is a rundown of my thought process while trying to figure this out.
Previously, I thought answering the question 'Does my Python interpreter have
this bug fix?' would be easy, turns out it can be pretty involved.  In fact,
the process was complicated enough that I'm not 100 percent confident of my
results.  So, if you know how to answer this question in an easier way, or if I
did something wrong feel free to drop me a
[comment](https://gist.github.com/durden/5690738).

## Step 1: Is this a bug?

First, I stumbled onto a weird warning when trying to
[zip files](http://docs.python.org/release/2.5.4/lib/module-zipfile.html) in an
[older version](http://docs.python.org/release/2.5.4/) of
[CPython](http://en.wikipedia.org/wiki/CPython).  The warning looked like this:

[code]
DeprecationWarning: struct integer overflow masking is deprecated
[/code]

I also noticed my zip file was corrupted.  So, what's a curious developer to
do?  Obviously, researching this error was the only option.  This  research
lead me to a [bug report](http://bugs.python.org/issue1622) for
[CPython](http://en.wikipedia.org/wiki/CPython).  Luckily, the issue was marked
as closed and that
[revision 64688](http://hg.python.org/cpython/rev/acfad8640e21/) contained the
fix.

Awesome, the magic of open source and documentation saves the day, but
wait I'm still seeing the error!  Am I doing something wrong or does my version
of the interpreter not have this fix?

## Versioning rabbit hole

Have you ever wondered what the information printed when you run the
[Python](http://python.org)
[interpreter](http://docs.python.org/tutorial/interpreter.html) __really__
means?  Let's break down the following for a typical Windows machine since it
has a bit more information.

[code]
Python 2.5.4 (r254:67916, Dec 23 2008, 15:10:54) [MSC v.1310 32 bit (Intel)] on
win 32
...
[/code]

Ok, so I have Python 2.5.4, but does this include the commit in
[revision 64688](http://hg.python.org/cpython/rev/acfad8640e21/)?  I could try
using this 'release' number and dig through a bunch of
[release notes](http://www.python.org/getit/releases/2.5.4/NEWS.txt), but that
seems difficult.  I just want to know what specific
[subversion](http://subversion.apache.org/) version number my interpreter is
running to verify I have the fixed revision. [1]

Luckily, the raw version information, **r254:67916**, is available in the
interpreter output.  However, I'm still a bit confused, why are there two
numbers?

Well, this version information actually comes from
[sys.subversion](http://docs.python.org/release/2.5.4/lib/module-sys.html).
So, **r254** refers to the
[subversion branch](http://svnbook.red-bean.com/en/1.1/ch04.html) or
[tag](http://svnbook.red-bean.com/en/1.1/ch04s06.html) and **67916** is the
version number.

### Branch or tag?

I'm not aware of a good way to distinguish if the string **r254** is a branch
or a tag.  So, we just have to manually look it up in the
[old repository](http://svn.python.org/view/python/). [2]

At this point we have jump into the
[Python subversion repository](http://svn.python.org/view/python/) and find a
branch or tag with the name **r254**.  After some searching, you'll find
**r254** refers to [this tag](http://svn.python.org/view/python/tags/r254/).

### Tag traversal

Remember, the bug fix I was looking for was in
[revision 64688](http://hg.python.org/cpython/rev/acfad8640e21/).  I am running
[revision 67916](http://hg.python.org/cpython/rev/a0a6d9909312/), which is
newer.  However, that doesn't mean that my branch/tag has this commit.  Again,
this is because [subversion](http://subversion.apache.org/) revision numbers
are linear and branching can make things complicated.

So, let's look into the revision I have a bit closer now that we have the
correct tag.  We could checkout this code locally and manually step through the
commits.  However, it's a bit faster and easier to use the web interface in
this situation.  Here's the quick process I used to dig into tag:

1. Go to the exact location for the SVN [r254 tag](http://svn.python.org/view/python/tags/r254/).
2. Remember that my Python installation was using revision 67916.  So, we need
   to see the state of this repository at this commit and tag.  You can do this
   in the web interface by typing the revision in the sticky revision box and
   hitting set, or going [here](http://svn.python.org/view/python/branches/release25-maint/?pathrev=67916).
3. Next, remember from the
   [diff](http://hg.python.org/cpython/rev/acfad8640e21/) that the
   `Lib/zipfile.py` file was where the bug fix was committed.  So, we need to
   look specifically at the commit logs for this file, which can be found
   [here](http://svn.python.org/view/python/branches/release25-maint/Lib/zipfile.py?view=log&pathrev=67916).

## Verdict

It looks like the last commit to the `Lib/zipfile.py` file I was running was
revision 60117 from January 19 2008.  Therefore, it appears that I do **NOT**
have the commit for the bug fix.

## Review

My version of Python has a bug in the
[zipfile module](http://docs.python.org/release/2.5.4/lib/module-zipfile.html),
which results in this error:

[code]
DeprecationWarning: struct integer overflow masking is deprecated
[/code]

I can tell that this was a [bug](http://bugs.python.org/issue1622) and fixed a
[long time ago](http://hg.python.org/cpython/rev/acfad8640e21/).
Unfortunately, my actual
[revision](http://svn.python.org/view/python/branches/release25-maint/?pathrev=67916)
doesn't have this fix.  Now it seems safe to assume that is why I'm getting the
original error, but I can verify this this too!

I can upgrade the Python installation to a later revision of the 2.5 series and
verify that the bug is indeed fixed by the magic of open source!  I'll leave
that work as an exercise for the reader.  Unfortunately, in my original
production scenario upgrading the interpreter was not an option.

### Side notes

- A nice thing to remember is that a version number with a `+` means the code
you're running with was modified, similar to 'M' in subversion.
[PEP 385](http://www.python.org/dev/peps/pep-0385/)

- Be sure to look through the
[sys module](http://docs.python.org/release/2.5.4/lib/module-sys.html) for more
interesting [metadata](http://en.wikipedia.org/wiki/Metadata) and version
information.

[1] This is an old version of Python, which still used
[subversion](http://subversion.apache.org/) as the
[RCS](http://en.wikipedia.org/wiki/Revision_Control_System).  Python has since
moved active development to [Mercurial](http://mercurial.selenic.com/).

[2] It's important to know the branch or tag because just knowing the
[revision 64688](http://hg.python.org/cpython/rev/acfad8640e21/) is not enough.
For example, if we are on revision 65689 that doesn't necessarily mean we have
the previous revision 64688.  This is because
[subversion](http://subversion.apache.org/) revision numbers are linear when
branching.

[3] All the links in this article reference Python version 2.5.4 since this was
the version I was seeing the problems in.
