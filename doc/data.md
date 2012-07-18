---
layout: page
title: "Data Structures"
description: ""
categories: doc
---

Overview
--------

Copperhead data structures are designed to
interoperate with python and numpy data structures insofar as
possible.  Copperhead functions can accept python lists and numpy
arrays directly, but for best performance, you should build `cuarray`
data structures.

Scalars and Tuples
------------------

Copperhead functions operate directly on scalar data types and python
tuple types.  No wrapping is necessary.

cuarray
-------

For best performance, it's important to wrap sequences by constructing
a `copperhead.cuarray` data structure from python lists or numpy
arrays.

The `copperhead.cuarray` class takes care of managing memory across
heterogeneous memory spaces, such as between the CPU and the GPU while
using the CUDA backend.  When calling a Copperhead function with a
python list or numpy array, the data is first converted to a `cuarray`
internally.  This conversion requires copying the data on the CPU, and
if the data is used on the GPU, it must then be copied to the GPU.
The `cuarray` class lazily allocates and moves data for you.
Commonly, data is reused in a particular place, and `cuarray` data
structures manage data movement so that extraneous copies are not
needed as data structures are reused.  If you pass python lists or
numpy arrays to a Copperhead function, this copying will be done at
every invocation of the function, greatly reducing performance.

Results
-------

Sequences returned from Copperhead functions are always
`copperhead.cuarray` data structures.  We provide a `to_numpy`
function that converts them back to numpy arrays.

Asynchrony
----------

`copperhead.cuarray` data structures are a type of future: when
control returns to the Python interpreter, they may not yet be
constructed in memory.  Using `copperhead.cuarray` data structures in
Python will block until the results are valid before computation is allowed
to proceed, so they are safe to use in code that is not aware of this
asynchrony.

However, if you're just timing a call to a Copperhead function, you
should be aware that the function may return before the results are
completed. To ensure that results are complete, you may call the
`force` function, which takes an `cuarray` and a Copperhead place, and
returns a `cuarray` which is guaranteed to be complete at the
specified place.