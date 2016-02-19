# The fine art of error reporting

All programs encounter errors at some point in time.  These errors can range
from very obvious to the dreaded
[bugfoot](http://www.urbandictionary.com/define.php?term=bugfoot&defid=4960803).
So, as a developer when you encounter an error condition you have an important
decision to make.  You have to decide how **much** information to expose and
who is the target audience for your error reporting [1].  This post will
describe things to take into account when making this decision as well as some
technical details to do this in a clean manner.

## One problem, two parts

Error reporting has two distinct sub-problems:

1. What to log
2. How to log

Each of these are difficult problems.  However, discussing 'what to log' is a
bit difficult to discuss in an abstract way.  So, this post will mostly discuss
the technical details of logging with a bit of 'what to log' dogma trickled in
for good measure.

## Two sets of requirements

The amount of error information you want to see largely depends on whether you
are a user or a developer.  Typically, users prefer to see a concise error
message that tells them what happened at a high-level and how to proceed.
However, if you are a developer you probably want as much information and
detail as possible.  This duality between users and developers creates a
problem when reporting application errors.

Luckily, [Python](http://python.org) has a built-in
[logging](http://docs.python.org/2/library/logging.html) module that makes
satisfying both types of people fairly straight-forward.  You can report all
sorts of developer-only information to a 'debug stream' and more user-friendly
error information into another 'stream' for warnings, errors, etc.

## Logging for users

From a technical point of view, logging for users should probably be limited to 
the `info`, `warning`, and `error` 'streams'[2].  In addition, you'll most likely
want to use plain language that 'typical' users can be expected to understand.

The core components of a *good* log message for a user should contain the
following:

- What happened
- What to do next
- Was anything (data) lost

I've found that a typical log message meant for users only includes 'What
happened.'  However, I'd argue that this is the least important information to
convey to the user.  It's very important to tell users how to recover and if
anything was lost.  This information should help users to re-create the data if
anything was lost and hopefully move ahead in their work.

This information can also serve as a call to action.  A well-written
error message can gradually lead users to submit bug report, re-open
the application, or suggest alternate methods of accomplishing their task.

Logging for users also has another difficult problem.  Each application tends
to have its' own terminology and can expect different amounts of technical
expertise from users.  So, developers should take this into account when
writing error messages with users as the audience.

In short, it's difficult to properly construct messages that are both
informative and lack unnecessary technical jargon that users do not (and
probably shouldn't be required to) understand.

## Logging for developers

Logging for developers has some of the same issues as logging for users.  It's
still difficult to decide on **what** and **how** to say something.  So, I'll
focus on some technical details about **how** to log errors, etc.  This side of
the topic is a bit easier to discuss without concrete examples.

First and foremost, developers tend to want as much information as possible to
aid in debugging errors.  There are several interesting tidbits in the
[logging](http://docs.python.org/2/library/logging.html) module that can make
logging [Exceptions](http://docs.python.org/2/tutorial/errors.html#exceptions)
extremely easy:

1. [logging.exception](http://docs.python.org/2/library/logging.html#logging.Logger.exception)
2. [exc_info](http://docs.python.org/2/library/logging.html#logging.Logger.debug)

First off,
[logging.exception](http://docs.python.org/2/library/logging.html#logging.Logger.exception)
does exactly what it sounds like, logs exceptions.  So, when inside of an
exception handler piece of code just drop a call to `logging.exception` in
along with a message.  This will automatically include detailed exception
information (including the
[traceback](http://docs.python.org/2/library/traceback.html#traceback-examples))
into your error message.  This is very handy because you don't have to format
the traceback information yourself.

The downside is that `logging.exception` goes to the `error` 'stream' when
logging.  Users will typically see whatever information shows up in `error`,
which can be a real problem if your users aren't the type to enjoy long nasty
traceback prints.  However, there's a solution to this dilemma!

This is where `exc_info` and `logging.debug` come in to save the day.  You can
pass `exc_info=True` to any `logging.debug` calls that are within an exception
block and have the same effect as `logging.exception` except in the debug
'stream.'

Below are some examples along with what you would see printed:

[code lang="python"]
    def test():
        x = []
        try:               
            idx = x[0]
        except Exception as err:
            log.warning('%s' % (err))

    >>> test()
    list index out of range

    def test():
        x = []
        try:               
            idx = x[0]
        except Exception as err:
            log.warning('%s' % (err))
            log.debug('bad news', exc_info=True)
    >>> test()
    list index out of range
[/code]

Now let's see what happens when we run our snippet after
[setting](http://docs.python.org/2/library/logging.html#logging.Logger.setLevel)
the log level set to `DEBUG`.

[code lang="python"]
    >>> test()
    list index out of range
    bad news
    Traceback (most recent call last):
    File "<ipython-input-21-69b02ac65835>", line 4, in test
        idx = x[0]
    IndexError: list index out of range
[/code]

## Solution: balance and care

We've seen that error reporting typically has to serve two distinct groups of
people, users and developers.  Each group has very different requirements for a
good log message.  We, as developers, must handle these different requirements
with care by paying attention not only to **what** we log but **how** we log
it.

We can solve the technical portion of error reporting by using the
[logging](http://docs.python.org/2/library/logging.html) module with a
combination of `logging.error`, `logging.warning`, `logging.exception`, and
`logging.debug`.

Unfortunately, this doesn't solve the more difficult problem of **what** to
log, but  hopefully this post gives you some things to consider in the future
when making some of these difficult decisions in your applications.

[1] You might need to consider the security implications of how much
information you expose as well.  One common way to gather information about an
application is to try to break it.  An attacker could use your own error/debug
information against you if you expose too many implementation details or hints.

[2] To guarantee a good argument amongst your team ask everyone to decide
**what** goes into which stream (`error`, `warning`, etc.).  You'll quickly
discover everyone has a different opinion and almost everyone is right **and**
wrong.
