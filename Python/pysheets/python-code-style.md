Style
=====

::: {.contents}
Table of Contents
:::

Naming
------

### Class

Bad

``` {.sourceCode .python}
class fooClass: ...
class foo_class: ...
```

Good

``` {.sourceCode .python}
class FooClass: ...
```

### Function

Bad

``` {.sourceCode .python}
def CapCamelCase(*a): ...
def mixCamelCase(*a): ...
```

Good

``` {.sourceCode .python}
def func_separated_by_underscores(*a): ...
```

### Variable

Bad

``` {.sourceCode .python}
FooVar = "CapWords"
fooVar = "mixedCase"
Foo_Var = "CapWords_With_Underscore"
```

Good

``` {.sourceCode .python}
# local variable
var = "lowercase"

# internal use
_var = "_single_leading_underscore"

# avoid conflicts with Python keyword
var_ = "single_trailing_underscore_"

# a class attribute (private use in class)
__var = " __double_leading_underscore"

# "magic" objects or attributes, ex: __init__
__name__

# throwaway variable, ex: _, v = (1, 2)
_ = "throwaway"
```
