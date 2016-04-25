1. GIL
2. Limited stack
3. No tail call optimization
    - Function is called by 'return' statement
    - Don't need to deal with the stack in a tail call b/c we won't ever return
      to the enclosing function
    - Reasoning for not having this is there would be no traceback in stack
      frames, etc. when doing this

      def func_a(...):
        ...
        func_b(...)
        ...
        return func_c(...)
4. Needs more syntatic sugar
    def __init__(self, thing, other, third):
        self.thing = thing
        self.other = other
        self.third = third

    -- replace with --

    def __init__(self, self.thing, self.other, self.third):
        pass
5. Inconsistent names
    - PEP8 says to use CamelCase for classes
        - list, tuple, set don't follow this
    - PEP8 says lowercase_with_underscores
        - s.startswith, s.isdigit don't follow this
6. Poor regex support
    - Regex are just a stand-alone module, not baked into the 'core'
    - Workaround to make it look like it is:
        from re import compile as re
        re('__[a-z]').match(source)
    - This isn't ideal b/c compile() is meant to be used to compile an re once
      and use it, not keep compiling everywhere.
7. Built-in functions
    - len(obj) -> obj.length
    - isinstance(obj, Type) -> obj.is_instance_of(Type)
    - hasattr(obj, 'attr') -> obj.has_attribute('attr')
    - callable(obj) -> obj.is_callable
    - any([True, False]) -> [True, False].any
    - map(str.lower, ['A', 'B']) -> ['A', 'B'].map(str.lower)
    - functools.partial(print, file=stderr) -> print.partial(file=stderr)
8. If statement
    - if statement doesn't return a value, but ternary statement does.
    - not orthogonal
    - Support something like this (multi-line ternary statements):
        result = if condition:
            consequence
        else:
            alternative

        result = if condition: consequence else: alternative
9. For statement
    - Again, not orthogonal between for loop and list comprehension
    - Support something like this so for loop and comprehension could be a
      single construct and return a value:

      result = for each in items:
          each.something()

      result = for each in items: each.something
10. Poor destructuring assigment
    - Tuple unpacking is limited, maybe support more complicated things on the
      left side of an assigment
    - Similar to coffeescript:
        {'name': name, 'age': age} = people[0]
        {name, age} = people[0]
11. No real lambdas or blocks
    - Different ways to adopt a syntax similar to coffeescript for anonymous
      functions, decorators, etc.
    - Speaker tries to make the point that with true lambda functions we would
      not need decorators and context managers
    - Personally this syntax is a bit ugly

- Talk by Vladimir Keleshev
- http://www.youtube.com/watch?v=CpjUoYcaUu8
