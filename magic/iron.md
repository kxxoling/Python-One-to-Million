Creating your own objects in Python inevitably means implementing one or more of Python's protocol methods-the magic methods whose names start and end with double underscores. These protocols are roughly the equivalent of interfaces in Python.

The lookup rules for the magic methods are slightly different from those of other attributes. Normal attribute lookup order [1] is instance -> class -> base classes. Magic method lookup goes directly to the class, skipping the instance. This means that you can't override these methods by attaching a function directly to the instance (or through __getattr__); overriding has to happen at the class level. To provide these methods for classes themselves, they need to be implemented on the classes' class, that is, its metaclass. [2]

This appendix is a reference to all the common magic methods. [3]

Object creation

The object creation methods are called when a class is instantiated, as shown in table B.1.

Table B.1 Object creation

Method  Description

__new__(cls, ...)   The object constructor. Responsible for creating new instances. It receives the class as the first argument, followed by all arguments passed in the instantiation call. To customize instance creation when inheriting from the built-in immutable types or .NET objects, you should override __new__ instead of __init__.(a) This method returns the new instance, but there is no requirement for it to be an instance of the class provided. If the instance returned is not an instance of cls, then __init__ will not be called.

__init__(self, ...) The object initializer. Called after __new__ when a new instance is created. Like other instance methods, it receives the instance as the first argument. It also receives all the arguments passed in the instantiation call. Returning anything other than None from this method will cause a TypeError to be raised.

a. Initializing immutable values is done in __new__, because otherwise it would be possible to change an immutable value by calling __init__ with a different value.

Comparison

The rich comparison methods are called during comparison operations involving the ==, !=, <, <=, >, and >= operators. They are also called by operations that implicitly involve comparisons, such as sorting a list. All these methods should return a Boolean. They can also return the NotImplemented singleton to indicate that the operation is not implemented for the two values being compared.

In Python there is no comparison fallback. To support all the comparison operators you need to implement all the comparison methods, shown in table B.2.

Classes of immutable objects that implement the comparison methods should also implement __hash__. Objects that compare equal should have the same hash.

Table B.2 Comparison methods

Method  Description

__eq__(self, other) Called for equality operations (==). Python defaults to identity comparison for equality where __eq__ is not defined.

__ne__(self, other) Called for inequality operations (!=). Python defaults to identity comparison for inequality where __ne__ is not defined.

__lt__(self, other) Called for less-than operations (<).

__le__(self, other) Called for less-than or equals operations (<=).

__gt__(self, other) Called for greater-than operations (>).

__ge__(self, other) Called for greater-than or equals operations (>=).

Miscellaneous

Table B.3 provides a selection of important methods that don't fit into any other category.

Table B.3 Miscellaneous methods

Method  Description

__nonzero__(self)   Called to implement truth value testing and the built-in function bool. This method should return a Boolean. If a class doesn't implement __nonzero__ but does implement __len__, then that will be called instead. A nonzero return value from __len__ indicates that an instance is True. If a class defines neither __len__ nor __nonzero__, all its instances are considered True.

__subclasses__(self)    Available on classes only. Returns a list of all subclasses of the class.

__call__(self, ...) Called when an object is called as a function.

__hash__(self)  Called when an object is used as a dictionary key, for set membership, and by the built-in function hash. Should return a 32-bit integer or a long (new in Python 2.5). Objects that compare equal should have the same hash. Mutable objects (which should not be used as dictionary keys) should implement __hash__ to raise a TypeError. It is easiest to implement __hash__ by using the hash function on all the components of the object that play a part in comparison (either combining them with exclusive or or hashing a tuple of them).

__del__(self)   Called when a Python object is about to be destroyed. __del__ can resurrect an object by creating a new reference to the object. A lot of the standard Python documentation for __del__ does not apply, because IronPython uses .NET garbage collection rather than reference counting.

__dir__(self)   Return a list of strings representing all the members available on an object. This method is called when dir is called on an object and is used to customize the list of available members on dynamic objects (for example those that provide attribute access through __getattr__). It is actually new in Python 2.6, but is available in IronPython 2 as it corresponds to a method available on dynamic .NET objects which is used by the DLR.

Containers and Iteration

Python has two protocols for objects that support subscription (indexing): the sequence protocol and the mapping protocol. The sequence protocol [4] is used for sequences (like lists, tuples, and strings) that are indexed with an integer. The mapping protocol [5] is used for types (like dictionaries) that map keys to values. Both sequences and mapping types are typically iterable, with sequences iterating over their values and mapping types iterating over their keys.

Both the mapping and sequence protocols use __getitem__, __setitem__, and __delitem__ to fetch, set, and delete items. The mapping and sequence protocol methods are shown in table B.4.

Table B.4 Mapping and sequence protocol methods

Method  Description

__getitem__(self, index)    Called when an item or slice is fetched. If an item is indexed using slice syntax, then the index will be a slice (a) object. Classes may raise a TypeError if the index is an inappropriate type. A class implementing the sequence protocol should raise an IndexError if the index is outside the bounds of the sequence. A class implementing the mapping protocol should raise a KeyError instead. The same rules about slicing and exceptions also apply to __setitem__ and __delitem__. Note that these methods should also accept negative indices to mean indexing from the end (where 0>N>=-len(S)).

__setitem__(self, index, value) Called when an item or slice is set.

__delitem__(self, index)    Called when an item or slice is deleted.

__len__(self)   Called by the built-in function len and returns the number of items in the container. May also be called when creating an iterator from an object.

__contains__(self, item)    Called by the in operator (x in y). Should return a Boolean indicating whether the container contains the item.

__iter__(self)  Called when iterating over an object or by the built-in function iter or when iterating over an object. It should return an iterator object. Iterator objects have an __iter__ method that returns self and a next method that returns the members of the iterator sequentially. Once the iterator is exhausted, subsequent calls to next should raise StopIteration.

__reversed__(self)  Containers may implement this method to provide an optimized reverse iterator for instances. If this method is available, it is called by the built-in function reversed.

__missing__(self, key)  New in Python 2.5. This method is called on subclasses of the built-in dictionary (dict) type when a key that doesn't exist is requested. This method is useful for providing default values.

__length_hint__(self)   Iterators can implement this as an optimization so that Python can preallocate space for them. IronPython doesn't use it (yet, anyway), but if you see this method, now you know what it is for.

a. The start, stop, and step attributes correspond to the [start:stop:step] components of the slice (or None if the component is omitted).

Generator expressions and conditional expressions

Generator expressions are a form of iteration that we haven't covered elsewhere. We've shown how list comprehensions allow for an extremely concise form of iteration and filter in a single expression:

result = [x for x in iterable if some_condition(x)]

This is comparable to LINQ over objects, which was introduced in C# 3.0 (.NET 3.5). There is an alternate form of list comprehensions called generator expressions, which arrived in Python 2.4. Instead of square brackets, they use parentheses to surround the expression. The major difference is that instead of being evaluated immediately, they are evaluated lazily. A generator expression returns a generator object (the same kind of object returned by a generator function). Like other iterators, this can be consumed by iterating over it, or you can consume individual items by calling the next method.

Because they are lazily evaluated, we can combine them:

from os import listdir

python_files = (f for f in listdir('.') if f.endswith('.py'))

first_lines = ((f, open(f).readline()) for f in python_files))

names_to_first_line = dict(first_lines)

None of the generator expressions are actually executed until the final dict call. A nice side effect is that they look nicer when used in places that take any iterable:

total = sum(val for val in some_list if val > 0)

Both list comprehensions and generator expressions can take advantage of another feature new to Python 2.5: conditional expressions (also known as ternary expressions).

Conditional expressions have the basic syntax:

X if Y else Z

This evaluates Y, and if it is True it returns X-otherwise, it returns Z. We can use this in list comprehensions and generator expressions:

generator = (f(a) if test(a) else g(a) for a in some_list)

This generator expression tests each member of some_list, yielding f(a) if test(a) returns True and g(a) if test(a) returns False.

Advanced tools for working with iterators and generators can be found in the standard library module itertools; see http://docs.python.org/library/itertools.html.

Conversion to string

There are several ways that objects can be converted to strings, both implicitly and explicitly. Table B.5 lists the protocol methods that do this.

Table B.5 String conversion methods

Method  Description

__repr__(self)  Called by the built-in repr function and when an object is displayed on the console. This method should return a string. Many of the built-in types have string representation that can be used to reconstruct the object if passed in to eval.

__str__(self)   Called by the built-in str function (type) or when an object is printed. This method should return a string. If __str__ is not defined on an object but __repr__ is, then str will call this instead (so if you define only one, choose __repr__).

__unicode__(self)   Normally called by the built-in unicode function (type) and should return a Unicode string. In IronPython the unicode type is the str type, so __str__ will be called instead.

Attribute access

In Python you can completely customize attribute access on objects for fetching, setting, and deleting. Table B.6 shows the attribute access methods.

Table B.6 Attribute access methods

Method  Description

__getattr__(self, name) If this method is implemented, then it will be called with the attribute name when an attribute that doesn't exist on the object is requested. If fetching the attribute fails, it should raise an AttributeError.

__setattr__(self, name, value)  If this method is implemented, it will be called whenever an attribute is set on an instance. __setattr__ can do â€œrealâ€ attribute setting by delegating to object: object.__setattr__(self, name, value).

__delattr__(self, name) If this method is implemented, it will be called whenever an attribute is deleted.

__getattribute__(self, name)    This method is always called for attribute access (new-style classes only). It is an important part of the descriptor protocol. It should return the attribute or raise an AttributeError. If both this method and __getattr__ are explicitly defined, then __getattr__ will be called only if __getattribute__ calls it.

Notice the asymmetry between __getattr__ and __setattr__. __getattr__ is only called when fetching attributes that don't exist whereas __setattr__ is called for all attribute setting.

Numeric types

Numeric types are expected to implement a whole host of different methods in order to support the full range of numeric operations and conversions between the different built-in types. In addition, other types are free to implement individual methods to support a subset of the operations. For example, strings and lists both implement the addition methods so that you can add strings and lists together. Strings implement the modulo operator for string interpolation.

Arithmetic operations

Table B.7 shows the methods that implement the binary arithmetic operations (+, -, *, /, //, true division, %, divmod(), pow(), **, <<, >>, &, ^, |). If one of those methods does not support the operation with the supplied arguments, it should return NotImplemented.

Table B.7 Binary arithmetic operations



Method  Description

__add__(self, other)    Called for addition operations (+).

__sub__(self, other)    Called for subtraction operations (-).

__mul__(self, other)    Called for multiplication (*).

__floordiv__(self, other)   Called for floor division (//).

__div__(self, other)    Called for division (/).

__truediv__(self, other)    Called for division when true division is on.

__mod__(self, other)    Called for the modulo operator (%).

__divmod__(self, other) Called by the built-in divmod function. This method should be the equivalent to using __floordiv__ and __mod__.

__pow__(self, other[, modulo])  Called for power operations (**). Can also be called by the built-in pow function. This method must accept an optional third argument if it is to support the three-argument form of the pow function.

__lshift__(self, other) Called for left-shift operations (<<).

__rshift__(self, other) Called for right-shift operations (>>).

__and__(self, other)    Called for binary and operations (&).

__xor__(self, other)    Called for binary exclusive or operations (^).

__or__(self, other) Called for binary or operations (|).

These methods all have an additional two forms. If they are on the right-hand side of a binary operation, then the right-hand version of the operator method will be called. This has the same name as the normal operator but with an r prepended. For example, the right-hand addition operator method is __radd__.

All of the operators (except for the divmod function) also have an in-place equivalent. The in-place operators are +=, -=, /=, //=, *=, %=, **=, <<=, >>=, &=, ^=, and |=. The in-place operator methods have the same name as the standard operator methods with an i prepended. For example, the in-place addition operator method is __iadd__. Because in-place operations rebind the name being operated on, mutable values that implement in-place operators should return self from the in-place operator methods (immutable values can return a new value as normal). If an in-place operator is used on an object that implements the standard operator method but not the in-place operator method, then the standard operator method will be used instead.

Table B.8 shows the unary arithmetic operations.

Table B.8 Unary arithmetic operations



Method  Description

__neg__(self)   Called for the unary negation operator (-)

__pos__(self)   Called for the unary plus operatory (+)

__abs__(self)   Called by the built-in function abs; returns the absolute value

__invert__(self)    Called by the invert operator (~)

Conversion between numeric types

The methods in table B.9 are used to convert objects between numeric types, usually by built-in functions.

Table B.9 Type conversion



Method  Description

__complex__(self)   Called by the built-in function complex. Should return a complex number.

__int__(self)   Called by the built-in function int. Should return an int.

__long__(self)  Called by the built-in function long. Should return a long.

__float__(self) Called by the built-in function float. Should return a float.

__oct__(self)   Called by the built-in function oct. Should return a string (the octal representation).

__hex__(self)   Called by the built-in function hex. Should return a string (the hex representation).

__index__(self) Called whenever Python needs an integer object (such as in slicing). Must return an integer (int or long). New in version 2.5.

Context managers and the with statement

The Python with statement (new in Python 2.5) [6] supports the concept of a runtime context. This allows you to perform operations on an object, with a context manager to handle closing or disposing of the object once the operations are complete. This is useful for committing database transactions (or rolling them back in case of an error), synchronizing threads, or working with files.

The context management protocol involves implementing a pair of methods, __enter__ and __exit__. Table B.10 describes these methods.

Table B.10 The context management protocol

Method  Description

__enter__(self) Enter the runtime context of the context manager. The object(s) returned by this method are bound to the names in the as clause of the with statement (if any).

__exit__(self, exc_type, exc_val, exc_tb)

Exit the runtime context associated with the context manager. If the with statement exits normally, then exc_type, exc_val, and exc_tb will all be None. If an exception occurs, then they will have the same values as would be returned by sys.exc_info().

Context managers should not re-raise exceptions; instead they should return a Boolean indicating whether or not they have handled the exception (a return value of False will cause the exception to be re-raised after __exit__ has completed).

with as RAII

The Python with statement is analogous to the C# using statement and is a form of RAII (Resource Acquisition Is Initialization). with is an improvement on RAII in C++ because it can tell if an exit is exceptional or not.

Python defines these methods on files, so that you can use the following pattern:

with open(filename) as handle:

    data = handle.read()

    ...

When the with statement is executed, [7] __enter__ is called and the file handle returned is bound to the name handle. When the code inside the with block is exited (or an exception is raised), __exit__ is called, which closes the file.

The standard library module contextlib [8] is extremely useful for working with the context management protocol. contextlib.closing is a convenient decorator for creating context managers that automatically close a resource. When closing is insufficient, contextlib.contextmanager is the simplest way to create a context manager. When working with decorators in general, functools [9] is another extremely useful module. For example, functools.wrap preserves the name (__name__) and docstrings of decorated functions.

The descriptor protocol

The descriptor protocol is an aspect of Python that is regarded as deep black magic, perhaps even more so than metaclasses. Like metaclasses, the rules are very simple once you understand them; it is just not very often that you need to explicitly implement them. The descriptor protocol is how properties, class methods, and static methods are implemented.

When an object is fetched from a class or instance dictionary, the __getattribute__ method checks for these methods and invokes them appropriately to fetch the object. This allows you to customize what happens when an object is accessed as a class or instance attribute. Table B.11 shows the descriptor protocol methods.

Table B.11 Descriptor protocol methods

Method  Description

__get__(self, instance, owner)  Called to get attributes from an instance or a class (owner). If fetched from a class, instance will be None.

__set__(self, instance, value)  Called to set the attribute on an instance of the owner class to a new value.

__delete__(self, instance)  Called to delete an attribute on an instance of the owner class.

Python has a built-in property descriptor that allows you to invoke code when an attribute is requested on an instance. It doesn't have an equivalent classproperty allowing you to create static properties as you can in C#. Using the descriptor protocol we can implement classproperty in a few lines of code:

class classproperty(object):

    def __init__(self, function):

        self.function = function

    def __get__(self, instance, owner):

        return self.function(owner)

classproperty can be used as a decorator. When it decorates a method (which should receive the class as its only argument), it replaces the method in the class with a descriptor. The original method is stored as a function object, the function attribute on the descriptor.

When the descriptor is fetched from the class, its __get__ method is invoked, and the stored function is called with the class passed in. Despite its mystery-shrouded reputation, the descriptor protocol can be used very simply to add new language features.

Magic attributes

As well as magic methods, many Python objects have magic attributes. These are attributes whose names start and end with double underscores and that have a special meaning to the Python interpreter. Table B.12 lists the most common of these attributes along with their description.

Table B.12 Python magic attributes

Method  Description

__doc__ Most Python objects have a __doc__ attribute that holds the docstring for the object. If there is no docstring, this attribute will be None. Docstrings are displayed by the help function and used by automated documentation tools. Docstrings can be assigned directly or created with a string literal as the first entry (before any code) in a module, class, function, or method.

__dict__    __dict__ is a read-only attribute that holds all members of an object. It forms the object namespace. It exists for all objects except for those that use __slots__.

__slots__   When __slots__ is set as a class variable (a list of strings), it reserves space in the class for the named members. Instances of classes with __slots__ will not have a __dict__ dictionary, and attempting to set an attribute not listed in __slots__ will raise an AttributeError. It is intended as a memory optimization, saving the space required for a dictionary per instance. In IronPython classes that define __slots__ are heavily optimised; attribute lookup is approximately 4x faster. __slots__ has several caveats about its use. See the Python documentation for more details. (a)

___class__  A reference to the class for all class instances (everything in Python is an instance of a class).

__bases__   A class attribute referencing a tuple of the base classes for the class.

__name__    An attribute (string) on modules, functions, and classes. It is assignable, allowing decorated functions to retain the same name as the function they wrap (very useful for tracebacks from decorated functions).

__all__ An optional attribute (list of strings) for modules. If available, this lists all the names that will be exported by the module when from module import * is executed. Defining __all__ can be a useful way of defining the public API of a module.

__file__    A module attribute (string) containing the path the module was loaded from on the filesystem. This attribute does not exist for built-in modules.

__module__  A class, function, and method attribute (string) with the name of the module the object was defined in.

__metaclass__   If this is defined as a class attribute, then the callable assigned will be called to create the class instead of type. It can also be used as a module-level attribute, and all old-style classes (without an explicit alternative metaclass or inheriting from a class with a metaclass) will use the callable assigned as their metaclass. A quick way to convert all old-style classes in a module into new-style classes is to add __metaclass__ = type at the start of the module. (It doesn't affect new-style classes, as they inherit type as a metaclass from object.)

__debug__   A global variable (Boolean) indicating whether the interpreter is being run with optimizations on (-O or -OO command-line switches).

See http://docs.python.org/reference/datamodel.html#slots.

Functions and modules

There are also a handful of modules and one built-in function whose names start and end with double underscores. Table B.13 lists the magic modules and functions.

Table B.13 Magic functions and modules

Method  Description

__builtin__ The Python module that contains all the built-in functions and exceptions. You can import this and patch it to affect the global scope of all modules. You can effectively create new built-ins, or point existing names to different objects at runtime.

__main__    This module is the main script (as a module) that the interpreter is executing.

__init__    Python packages consist of directories containing an __init__.py file. As well as marking the directory as a package, this module acts as the top-level namespace for the package.

__import__  A built-in function that provides programmatic access to the import machinery. The function signature is __import__(name[, globals[, locals[, fromlist[, level]]]]). See the Python documentation on built-in functions (a) for more details.




This is appendix B from IronPython in Action. My thanks to Manning Publications for permission to reproduce it here.

It is a reference to all the common methods and attributes used for Python protocols: the Python magic methods.

This is a reference for IronPython 2.0, which is the equivalent of Python 2.5.

http://www.ironpythoninaction.com/magic-methods.html
