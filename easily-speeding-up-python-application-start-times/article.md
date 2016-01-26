Typically Python applications are comprised of several Python source (.py)
files. The end-user of the application runs these Python source
files by supplying them to the python command. This process instructs the
Python interpreter to translate each .py file into
[Python byte code](https://docs.python.org/2/glossary.html#term-bytecode) that
is stored in another file typically with the .pyc extension.

This looks like a one step process to the end-user, but it's a complicated set
of steps that includes parsing the text into an internal representation that
can be more easily compiled into byte code, which is what the Python
interpreter can execute.

This parsing and compilation step can hinder the startup times of your
application, especially if you have a lot of Python modules that are imported
at startup time. This is a well-known problem, but did you know that the
Python source provides a more efficient way? I didn't, until a recent trip
[spelunking](https://en.wikipedia.org/wiki/Caving) in the Python source.

The script is called
[compileall.py](https://docs.python.org/2/library/compileall.html), and it's
located in the Lib directory of the Python
[source distribution](https://www.python.org/downloads/). The
script is nothing novel, but it's a good learning tool to distinguish between
the process of *building* and *running* Python code. It's also very flexible
because it can be called as a
[module from the command-line](https://docs.python.org/2/library/compileall.html#command-line-use)
and [inside another Python script](https://docs.python.org/2/library/compileall.html#public-functions).

compileall.py also allows you to specify a list of directories to translate
recursively. It can even translate all the modules on sys.path for you! This
is much easier than running your application manually to create all the .pyc
files and potentially missing some if you don't execute all the imports when
testing.

This means you can use compileall.py to create .pyc files for distribution in a
simple 'build' script. Then, you can provide slightly faster startup times
because end-users don't have to waste time in the parsing and compiling steps.

## Not so fast

There are some things to be careful of with this distribution choice. Your
end-users must be running the **exact** same version of Python that used
compileall.py. So, the use-case of this distribution method is relatively
small since it can only target a specific version.

There are also all sorts of other caveats when you're using a bunch of
third-party libraries. This distribution style is best suited for small
scripts or applications of a few files without a lot of depenencies outside of
the Python standard library.

However, distributing files this way can be handy if you want your application
to start up as fast as possible and you know you distribution environment very
well. It's also a neat way to provide a **slightly** obfuscated version of
your code to prevent end-users from editing things too easily.