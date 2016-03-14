[Cleaner Code Through Partial Function Application](http://chriskiehl.com/article/Cleaner-coding-through-partially-applied-functions/)
is the best tutorial I've ever read on the subject of
[partial functions](https://docs.python.org/2/library/functools.html#functools.partial).

I'm fond of using partials to self-document a line of code instead of using a
comment, much like the author suggests:

[code lang="python"]
    def is_grouped_together(text):
        return re.search("[a-zA-Z]\s\=", text)

    def is_spaced_apart(text):
        return re.search(“[a-zA-Z]\s\=”, text) 

    def and_so_on(text):
        return re.search(“pattern_188364625", text)
[/code]

Of course, the partial function is not necessary. The regex could be
documented with a comment above the regex or by using the
[verbose regex flag](https://docs.python.org/2/library/re.html#re.VERBOSE).
Those approaches are easy to grasp for any programmer.

## Executable Documentation

However, the small *flaw* is the documentation is not directly tied to the
code. The documentation is **not** executed and is subject to human error.
It's easy to change the code and forget to update the comment. Therefore, I
have a healthy distrust for comments. That's why I argued in favor of
*executable documentation* in
[Bugs, can't code without them, so code against them](http://durden.github.io/defensive_coding/?full#1) ([video](https://www.youtube.com/watch?v=HZUY-lo-Esg)).

I focused mainly on
[assert
statements](https://docs.python.org/2/reference/simple_stmts.html#the-assert-statement)
as a mechanism for *executable documentation*. Partials can be thought of in a
similar manner albeit not as effective. The code can work perfectly fine even
if the function is named incorrectly.

## Illusion of global variables

I've been hearing about the horrors of global variables since my first
undergraduate Computer Science course. Although, all techniques have their
uses, including global variables. For example, I have a work project that
consists of **tons** of helper functions to create and modify multi-dimensional
[numpy](http://www.numpy.org) arrays. All these functions take a tuple
argument containing the dimensions of the array to create, modify, etc. This
makes all the functions easily testable. All necessary information is
contained in the function arguments, which is great for
[dependency injection](https://en.wikipedia.org/wiki/Dependency_injection).

Unfortunately, users only manipulate arrays of a single set of dimensions at a
time. They know their single-use dimensions at start time so why should they
have to repeat themselves in **every** function call? Luckily, I dynamically
create a partial function that fills in the dimensions argument for them on
start up.

This is a great middle ground solution. Users can *forget* about the
dimensions argument because all functions are defaulted to the *global*
dimensions. The solution is good from software engineering aspect as well
because I can test all the functions with just simple arguments. There is
the illusion of a global variable without the downsides.

What's your favorite partial function usage?

