---
layout: page
title: "Types"
description: ""
categories: doc
---

Overview
--------

Python's dynamic type system brings great
flexibility, but at an efficiency cost.  Since we aim to produce
parallel implementations that compete with native code, we overlay a
strict type system on Python code.  Programs which do not typecheck
correctly are rejected by the Copperhead compiler.  Our type system is
designed to be as unobtrusive as possible: no type annotations are
necessary, and we adopt `numpy` types whenever possible.  This page
documents the type system.

Primitive Types
-----------

Copperhead supports the following primitive types:
1. `Bool`: A boolean type, corresponding to a C++ `bool`.
2. `Int`: A 32-bit signed integer type, corresponding to a C++ `int`.
3. `Long`: A 64-bit signed integer type, corresponding to a C++ `long`.
or `long long`, depending on your compiler.
4. `Float`: A 32-bit floating point type, corresponding to a C++
`float`.
5. `Double`: A 64-bit floating point type, corresponding to a C++
`double`.

Compound Types
--------------

Copperhead supports the following compound types:
1. `Tuple`: A heterogeneously typed tuple.
2. `Fn`: Function types consist of two parts: a `Tuple` of inputs, and
an output type.
3. `Seq`: A one-dimensional, homogenously typed
sequence. Multidimensional arrays are not yet supported. Currently,
sequences cannot be nested, although support for nested sequences is underway.

Polymorphic Types
-----------------
The Copperhead type system supports polymorphism.  For example, the
following prelude function

{% highlight python %}
@cu
def op_add(x, y)
    return x + y
{% endhighlight %}

Has type `ForAll a: Fn(Tuple(a, a), a)`.

Python Interoperability
-----------------------

Numpy scalars receive their corresponding types:
1. `np.bool` will be typed as `Bool`.
2. `np.int32` will be typed as `Int`.
3. `np.int64` will be typed as `Long`.
4. `np.float32` will be typed as `Float`.
5. `np.float64` will be typed as `Double`.

Following Numpy, Python scalars receive the following types:
1. `int` will be typed as `Long`.
2. `float` will be typed as `Double`.

One-dimensional Numpy arrays will be typed as `Seq(A)`, where `A` is
the type of each element of the Numpy array.  Multidimensional Numpy
arrays are not yet supported.

Python lists will be converted to Numpy arrays, and then to Copperhead
sequences. This means that the same logic Numpy uses to ensure
homogenously-typed arrays is employed to create Copperhead sequences.

Literals
--------
Literals are weakly typed. If no other constraints are placed upon
them, they are typed as Python literals.  For example, the following
function:

{% highlight python %}
@cu
def zero():
    return 0
{% endhighlight %}

has type `Fn(Tuple(), Long)`. Since nothing in the procedure
constrains the type of the result, we type the literal with the native
Python type.

However, the function:

{% highlight python %}
@cu
def increment(x):
    return x + 1
{% endhighlight %}

has type `ForAll a: Fn(Tuple(a), a)`, and the literal `1` will be
automatically cast to the type of the input `x`.

You can explicitly cast literals to different types, or to the type of
other variables, using functions provided in the prelude.

Casting
-------

To change the type of a scalar, we have some casting functions:
`int32`, `int64`, `float32`, `float64`, which produce scalars of the
appropriate type.

We also have two polymorphic cast functions: `cast_to(x, y)`, which
accepts scalars `x` and `y`, and returns `x` cast to the type of `y`.

Along with `cast_to_el(x, y)`, which accepts scalar `x` and sequence
`y`, and returns `x` cast to the element type of `y`.

For example `sum` is implemented as
{%highlight python %}
@cu
def sum(x):
  return reduce(op_add, x, cast_to_el(0, x))
{%end highlight%}

The `cast_to_el` function allows me to express the type of the default
prefix `0` in a way that's compatible with the type of `reduce`.

Caveats
-------

Without typeclasses, our polymorphic types are not truly
polymorphic. Most prelude functions that are polymorphic can only
operate on a restricted set of types. Our polymorphic types are akin
to C++ templates, which often are restricted to a particular set of
types, although this is not enforced in the type system, and
instantiating them with unsupported types causes other compilation
errors.  We have the same issue with our type system.

For example, although `op_add` has type `ForAll a: Fn(Tuple(a, a),
a)`, which would seem to allow it to add two functions together, it
actually is restricted to operating on primitive types.  It does not
work on compound types such as sequences, tuples, or functions.  This
can cause unexpected type mismatch errors during compilation.  We are
working on adding useful error messages as well as better
documentation to help with this problem.  Ultimately, we will need a
richer type system (probably one which uses typeclasses) in order to
solve it completely.