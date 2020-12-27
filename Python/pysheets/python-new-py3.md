::: {.meta}
:::

New in Python3
==============

::: {.contents}
Table of Contents
:::

`print` is a function
---------------------

**New in Python 3.0**

-   PEP [3105](https://www.python.org/dev/peps/pep-3105/) - Make print a
    function

Python 2

``` {.sourceCode .python}
>>> print "print is a statement"
print is a statement
>>> for x in range(3):
...     print x,
...
0 1 2
```

Python 3

``` {.sourceCode .python}
>>> print("print is a function")
print is a function
>>> print()
>>> for x in range(3):
...     print(x, end=' ')
... else:
...     print()
...
0 1 2
```

String is unicode
-----------------

**New in Python 3.0**

-   PEP [3138](https://www.python.org/dev/peps/pep-3138/) - String
    representation in Python 3000
-   PEP [3120](https://www.python.org/dev/peps/pep-3120/) - Using UTF-8
    as the default source encoding
-   PEP [3131](https://www.python.org/dev/peps/pep-3131/) - Supporting
    Non-ASCII Identifiers

Python 2

``` {.sourceCode .python}
>>> s = 'Café'  # byte string
>>> s
'Caf\xc3\xa9'
>>> type(s)
<type 'str'>
>>> u = u'Café' # unicode string
>>> u
u'Caf\xe9'
>>> type(u)
<type 'unicode'>
>>> len([_c for _c in 'Café'])
5
```

Python 3

``` {.sourceCode .python}
>>> s = 'Café'
>>> s
'Café'
>>> type(s)
<class 'str'>
>>> s.encode('utf-8')
b'Caf\xc3\xa9'
>>> s.encode('utf-8').decode('utf-8')
'Café'
>>> len([_c for _c in 'Café'])
4
```

Division Operator
-----------------

**New in Python 3.0**

-   PEP [238](https://www.python.org/dev/peps/pep-0238/) - Changing the
    Division Operator

Python2

``` {.sourceCode .python}
>>> 1 / 2
0
>>> 1 // 2
0
>>> 1. / 2
0.5

# back port "true division" to python2

>>> from __future__ import division
>>> 1 / 2
0.5
>>> 1 // 2
0
```

Python3

``` {.sourceCode .python}
>>> 1 / 2
0.5
>>> 1 // 2
0
```

New dict implementation
-----------------------

**New in Python 3.6**

-   PEP [468](https://www.python.org/dev/peps/pep-0468/) - Preserving
    the order of \*\*kwargs in a function
-   PEP [520](https://www.python.org/dev/peps/pep-0520/) - Preserving
    Class Attribute Definition Order
-   bpo [27350](https://bugs.python.org/issue27350) - More compact
    dictionaries with faster iteration

Before Python 3.5

``` {.sourceCode .python}
>>> import sys
>>> sys.getsizeof({str(i):i for i in range(1000)})
49248

>>> d = {'timmy': 'red', 'barry': 'green', 'guido': 'blue'}
>>> d   # without order-preserving
{'barry': 'green', 'timmy': 'red', 'guido': 'blue'}
```

Python 3.6

-   Memory usage is smaller than Python 3.5
-   Preserve insertion ordered

``` {.sourceCode .python}
>>> import sys
>>> sys.getsizeof({str(i):i for i in range(1000)})
36968

>>> d = {'timmy': 'red', 'barry': 'green', 'guido': 'blue'}
>>> d   # preserve insertion ordered
{'timmy': 'red', 'barry': 'green', 'guido': 'blue'}
```

Keyword-Only Arguments
----------------------

**New in Python 3.0**

-   PEP [3102](https://www.python.org/dev/peps/pep-3102/) - Keyword-Only
    Arguments

``` {.sourceCode .python}
>>> def f(a, b, *, kw):
...     print(a, b, kw)
...
>>> f(1, 2, 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f() takes 2 positional arguments but 3 were given
>>> f(1, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f() missing 1 required keyword-only argument: 'kw'
>>> f(1, 2, kw=3)
1 2 3
```

New Super
---------

**New in Python 3.0**

-   PEP [3135](https://www.python.org/dev/peps/pep-3135/) - New Super

Python 2

``` {.sourceCode .python}
>>> class ParentCls(object):
...     def foo(self):
...         print "call parent"
...
>>> class ChildCls(ParentCls):
...     def foo(self):
...         super(ChildCls, self).foo()
...         print "call child"
...
>>> p = ParentCls()
>>> c = ChildCls()
>>> p.foo()
call parent
>>> c.foo()
call parent
call child
```

Python 3

``` {.sourceCode .python}
>>> class ParentCls(object):
...     def foo(self):
...         print("call parent")
...
>>> class ChildCls(ParentCls):
...     def foo(self):
...         super().foo()
...         print("call child")
...
>>> p = ParentCls()
>>> c = ChildCls()
>>> p.foo()
call parent
>>> c.foo()
call parent
call child
```

Remove `<>`
-----------

**New in Python 3.0**

Python 2

``` {.sourceCode .python}
>>> a = "Python2"
>>> a <> "Python3"
True

# equal to !=
>>> a != "Python3"
True
```

Python 3

``` {.sourceCode .python}
>>> a = "Python3"
>>> a != "Python2"
True
```

BDFL retirement
---------------

**New in Python 3.1**

-   PEP [401](https://www.python.org/dev/peps/pep-0401/) - BDFL
    Retirement

``` {.sourceCode .python}
>>> from __future__ import barry_as_FLUFL
>>> 1 != 2
  File "<stdin>", line 1
    1 != 2
       ^
SyntaxError: with Barry as BDFL, use '<>' instead of '!='
>>> 1 <> 2
True
```

Not allow `from module import *` inside function
------------------------------------------------

**New in Python 3.0**

``` {.sourceCode .python}
>>> def f():
...     from os import *
...
  File "<stdin>", line 1
SyntaxError: import * only allowed at module level
```

Add `nonlocal` keyword
----------------------

**New in Python 3.0**

PEP [3104](https://www.python.org/dev/peps/pep-3104/) - Access to Names
in Outer Scopes

::: {.note}
::: {.admonition-title}
Note
:::

`nonlocal` allow assigning directly to a variable in an outer (but
non-global) scope
:::

``` {.sourceCode .python}
>>> def outf():
...     o = "out"
...     def inf():
...         nonlocal o
...         o = "change out"
...     inf()
...     print(o)
...
>>> outf()
change out
```

Extended iterable unpacking
---------------------------

**New in Python 3.0**

-   PEP [3132](https://www.python.org/dev/peps/pep-3132/) - Extended
    Iterable Unpacking

``` {.sourceCode .python}
>>> a, *b, c = range(5)
>>> a, b, c
(0, [1, 2, 3], 4)
>>> for a, *b in [(1, 2, 3), (4, 5, 6, 7)]:
...     print(a, b)
...
1 [2, 3]
4 [5, 6, 7]
```

General unpacking
-----------------

**New in Python 3.5**

-   PEP [448](https://www.python.org/dev/peps/pep-0448/) - Additional
    Unpacking Generalizations

Python 2

``` {.sourceCode .python}
>>> def func(*a, **k):
...     print(a)
...     print(k)
...
>>> func(*[1,2,3,4,5], **{"foo": "bar"})
(1, 2, 3, 4, 5)
{'foo': 'bar'}
```

Python 3

``` {.sourceCode .python}
>>> print(*[1, 2, 3], 4, *[5, 6])
1 2 3 4 5 6
>>> [*range(4), 4]
[0, 1, 2, 3, 4]
>>> {"foo": "Foo", "bar": "Bar", **{"baz": "baz"}}
{'foo': 'Foo', 'bar': 'Bar', 'baz': 'baz'}
>>> def func(*a, **k):
...     print(a)
...     print(k)
...
>>> func(*[1], *[4,5], **{"foo": "FOO"}, **{"bar": "BAR"})
(1, 4, 5)
{'foo': 'FOO', 'bar': 'BAR'}
```

Function annotations
--------------------

**New in Python 3.0**

-   PEP [3107](https://www.python.org/dev/peps/pep-3107/) - Function
    Annotations
-   PEP [484](https://www.python.org/dev/peps/pep-0484/) - Type Hints
-   PEP [483](https://www.python.org/dev/peps/pep-0483/) - The Theory of
    Type Hints

``` {.sourceCode .python}
>>> import types
>>> generator = types.GeneratorType
>>> def fib(n: int) -> generator:
...     a, b = 0, 1
...     for _ in range(n):
...         yield a
...         b, a = a + b, b
...
>>> [f for f in fib(10)]
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

Variable annotations
--------------------

**New in Python 3.6**

-   PEP [526](https://www.python.org/dev/peps/pep-0526/) - Syntax for
    Variable Annotations

``` {.sourceCode .python}
>>> from typing import List
>>> x: List[int] = [1, 2, 3]
>>> x
[1, 2, 3]

>>> from typing import List, Dict
>>> class Cls(object):
...     x: List[int] = [1, 2, 3]
...     y: Dict[str, str] = {"foo": "bar"}
...
>>> o = Cls()
>>> o.x
[1, 2, 3]
>>> o.y
{'foo': 'bar'}
```

Core support for typing module and generic types
------------------------------------------------

**New in Python 3.7**

-   PEP [560](https://www.python.org/dev/peps/pep-0560/) - Core support
    for typing module and generic types

Before Python 3.7

``` {.sourceCode .python}
>>> from typing import Generic, TypeVar
>>> from typing import Iterable
>>> T = TypeVar('T')
>>> class C(Generic[T]): ...
...
>>> def func(l: Iterable[C[int]]) -> None:
...     for i in l:
...         print(i)
...
>>> func([1,2,3])
1
2
3
```

Python 3.7 or above

``` {.sourceCode .python}
>>> from typing import Iterable
>>> class C:
...     def __class_getitem__(cls, item):
...         return f"{cls.__name__}[{item.__name__}]"
...
>>> def func(l: Iterable[C[int]]) -> None:
...     for i in l:
...         print(i)
...
>>> func([1,2,3])
1
2
3
```

Format byte string
------------------

**New in Python 3.5**

-   PEP [461](https://www.python.org/dev/peps/pep-0461/) - Adding `%`
    formatting to bytes and bytearray

``` {.sourceCode .python}
>>> b'abc %b %b' % (b'foo', b'bar')
b'abc foo bar'
>>> b'%d %f' % (1, 3.14)
b'1 3.140000'
>>> class Cls(object):
...     def __repr__(self):
...         return "repr"
...     def __str__(self):
...         return "str"
...
'repr'
>>> b'%a' % Cls()
b'repr'
```

fstring
-------

**New in Python 3.6**

-   PEP [498](https://www.python.org/dev/peps/pep-0498/) - Literal
    String Interpolation

``` {.sourceCode .python}
>>> py = "Python3"
>>> f'Awesome {py}'
'Awesome Python3'
>>> x = [1, 2, 3, 4, 5]
>>> f'{x}'
'[1, 2, 3, 4, 5]'
>>> def foo(x:int) -> int:
...     return x + 1
...
>>> f'{foo(0)}'
'1'
>>> f'{123.567:1.3}'
'1.24e+02'
```

Suppressing exception
---------------------

**New in Python 3.3**

-   PEP [409](https://www.python.org/dev/peps/pep-0409/) - Suppressing
    exception context

Without `raise Exception from None`

``` {.sourceCode .python}
>>> def func():
...     try:
...         1 / 0
...     except ZeroDivisionError:
...         raise ArithmeticError
...
>>> func()
Traceback (most recent call last):
  File "<stdin>", line 3, in func
ZeroDivisionError: division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 5, in func
ArithmeticError
```

With `raise Exception from None`

``` {.sourceCode .python}
>>> def func():
...     try:
...         1 / 0
...     except ZeroDivisionError:
...         raise ArithmeticError from None
...
>>> func()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 5, in func
ArithmeticError

# debug

>>> try:
...     func()
... except ArithmeticError as e:
...     print(e.__context__)
...
division by zero
```

Generator delegation
--------------------

**New in Python 3.3**

-   PEP [380](https://www.python.org/dev/peps/pep-0380/) - Syntax for
    Delegating to a Subgenerator

``` {.sourceCode .python}
>>> def fib(n: int):
...     a, b = 0, 1
...     for _ in range(n):
...         yield a
...         b, a = a + b, b
...
>>> def delegate(n: int):
...     yield from fib(n)
...
>>> list(delegate(10))
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

`async` and `await` syntax
--------------------------

**New in Python 3.5**

-   PEP [492](https://www.python.org/dev/peps/pep-0492/) - Coroutines
    with async and await syntax

Before Python 3.5

``` {.sourceCode .python}
>>> import asyncio
>>> @asyncio.coroutine
... def fib(n: int):
...     a, b = 0, 1
...     for _ in range(n):
...         b, a = a + b, b
...     return a
...
>>> @asyncio.coroutine
... def coro(n: int):
...     for x in range(n):
...         yield from asyncio.sleep(1)
...         f = yield from fib(x)
...         print(f)
...
>>> loop = asyncio.get_event_loop()
>>> loop.run_until_complete(coro(3))
0
1
1
```

Python 3.5 or above

``` {.sourceCode .python}
>>> import asyncio
>>> async def fib(n: int):
...     a, b = 0, 1
...     for _ in range(n):
...         b, a = a + b, b
...     return a
...
>>> async def coro(n: int):
...     for x in range(n):
...         await asyncio.sleep(1)
...         f = await fib(x)
...         print(f)
...
>>> loop = asyncio.get_event_loop()
>>> loop.run_until_complete(coro(3))
0
1
1
```

Asynchronous generators
-----------------------

**New in Python 3.6**

-   PEP [525](https://www.python.org/dev/peps/pep-0525/) - Asynchronous
    Generators

``` {.sourceCode .python}
>>> import asyncio
>>> async def fib(n: int):
...     a, b = 0, 1
...     for _ in range(n):
...         await asyncio.sleep(1)
...         yield a
...         b, a = a + b , b
...
>>> async def coro(n: int):
...     ag = fib(n)
...     f = await ag.asend(None)
...     print(f)
...     f = await ag.asend(None)
...     print(f)
...
>>> loop = asyncio.get_event_loop()
>>> loop.run_until_complete(coro(5))
0
1
```

Asynchronous comprehensions
---------------------------

**New in Python 3.6**

-   PEP [530](https://www.python.org/dev/peps/pep-0530/) - Asynchronous
    Comprehensions

``` {.sourceCode .python}
>>> import asyncio
>>> async def fib(n: int):
...     a, b = 0, 1
...     for _ in range(n):
...         await asyncio.sleep(1)
...         yield a
...         b, a = a + b , b
...

# async for ... else

>>> async def coro(n: int):
...     async for f in fib(n):
...         print(f, end=" ")
...     else:
...         print()
...
>>> loop = asyncio.get_event_loop()
>>> loop.run_until_complete(coro(5))
0 1 1 2 3

# async for in list

>>> async def coro(n: int):
...     return [f async for f in fib(n)]
...
>>> loop.run_until_complete(coro(5))
[0, 1, 1, 2, 3]

# await in list

>>> async def slowfmt(n: int) -> str:
...     await asyncio.sleep(0.5)
...     return f'{n}'
...
>>> async def coro(n: int):
...     return [await slowfmt(f) async for f in fib(n)]
...
>>> loop.run_until_complete(coro(5))
['0', '1', '1', '2', '3']
```

Matrix multiplication
---------------------

**New in Python 3.5**

-   PEP [465](https://www.python.org/dev/peps/pep-0465/) - A dedicated
    infix operator for matrix multiplication

``` {.sourceCode .Python3}
>>> # "@" represent matrix multiplication
>>> class Arr:
...     def __init__(self, *arg):
...         self._arr = arg
...     def __matmul__(self, other):
...         if not isinstance(other, Arr):
...             raise TypeError
...         if len(self) != len(other):
...             raise ValueError
...         return sum([x*y for x, y in zip(self._arr, other._arr)])
...     def __imatmul__(self, other):
...         if not isinstance(other, Arr):
...             raise TypeError
...         if len(self) != len(other):
...             raise ValueError
...         res = sum([x*y for x, y in zip(self._arr, other._arr)])
...         self._arr = [res]
...         return self
...     def __len__(self):
...         return len(self._arr)
...     def __str__(self):
...         return self.__repr__()
...     def __repr__(self):
...         return "Arr({})".format(repr(self._arr))
...
>>> a = Arr(9, 5, 2, 7)
>>> b = Arr(5, 5, 6, 6)
>>> a @ b  # __matmul__
124
>>> a @= b  # __imatmul__
>>> a
Arr([124])
```

Data Classes
------------

**New in Python 3.7**

PEP [557](https://www.python.org/dev/peps/pep-0557/) - Data Classes

Mutable Data Class

``` {.sourceCode .python}
>>> from dataclasses import dataclass
>>> @dataclass
... class DCls(object):
...     x: str
...     y: str
...
>>> d = DCls("foo", "bar")
>>> d
DCls(x='foo', y='bar')
>>> d = DCls(x="foo", y="baz")
>>> d
DCls(x='foo', y='baz')
>>> d.z = "bar"
```

Immutable Data Class

``` {.sourceCode .python}
>>> from dataclasses import dataclass
>>> from dataclasses import FrozenInstanceError
>>> @dataclass(frozen=True)
... class DCls(object):
...     x: str
...     y: str
...
>>> try:
...     d.x = "baz"
... except FrozenInstanceError as e:
...     print(e)
...
cannot assign to field 'x'
>>> try:
...     d.z = "baz"
... except FrozenInstanceError as e:
...     print(e)
...
cannot assign to field 'z'
```

Built-in `breakpoint()`
-----------------------

**New in Python 3.7**

-   PEP [553](https://www.python.org/dev/peps/pep-0553/) - Built-in
    breakpoint()

``` {.sourceCode .python}
>>> for x in range(3):
...     print(x)
...     breakpoint()
...
0
> <stdin>(1)<module>()->None
(Pdb) c
1
> <stdin>(1)<module>()->None
(Pdb) c
2
> <stdin>(1)<module>()->None
(Pdb) c
```

The walrus operator
-------------------

**New in Python 3.8**

-   PEP [572](https://www.python.org/dev/peps/pep-0572/) - Assignment
    Expressions

The goal of the walrus operator is to assign variables within an
expression. After completing PEP 572, Guido van Rossum, commonly known
as BDFL, decided to resign as a Python dictator.

``` {.sourceCode .python}
>>> f = (0, 1)
>>> [(f := (f[1], sum(f)))[0] for i in range(10)]
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

Positional-only parameters
--------------------------

**New in Python 3.8**

-   PEP [570](https://www.python.org/dev/peps/pep-0570/) - Python
    Positional-Only Parameters

``` {.sourceCode .python}
>>> def f(a, b, /, c, d):
...     print(a, b, c, d)
...
>>> f(1, 2, 3, 4)
1 2 3 4
>>> f(1, 2, c=3, d=4)
1 2 3 4
>>> f(1, b=2, c=3, d=4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f() got some positional-only arguments passed as keyword arguments: 'b'
```

Dictionary Merge
----------------

**New in Python 3.9**

-   PEP [584](https://www.python.org/dev/peps/pep-0584/) - Add Union
    Operators To dict

``` {.sourceCode .python}
>>> a = {"foo": "Foo"}
>>> b = {"bar": "Bar"}

# old way
>>> {**a, **b}
{'foo': 'Foo', 'bar': 'Bar'}
>>> a.update(b)
>>> a
{'foo': 'Foo', 'bar': 'Bar'}

# new way
>>> a | b
{'foo': 'Foo', 'bar': 'Bar'}
>>> a |= b
>>> a
{'foo': 'Foo', 'bar': 'Bar'}
```
