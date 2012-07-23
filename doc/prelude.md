---
layout: page
title: "Prelude"
description: ""
categories: doc
---

Overview
--------

Copperhead procedures may call only a restricted set
of procedures, which we call the prelude (following Haskell).
Currently, every module using Copperhead must start with the
statement:
`from copperhead import *`, which brings in the Copperhead prelude and
makes these procedures accessible to Copperhead code.

Some of the functions in the prelude are Python built-ins, such as
`map()`, `reduce()`, `filter()` and `zip()`.  These functions are
treated by Copperhead as parallel primitives.  Some, like `zip()`, may
also have a restricted interface in comparison to their Python
counterpart.  The built-ins `any`, `all`, `sum`, `min`, & `max` are
treated as special cases of `reduce`.

Documentation for each of these functions is found in the
[prelude][prelude] itself.

Prelude procedures
------------------
* `map`
* `reduce`
  - `sum`
  - `any`
  - `all`
  - `min`
  - `max`
* `filter`
* `zip`, `unzip`
* `gather`, `scatter`
  - `permute`
  - `update`
* `scan`
  - `rscan`
  - `exclusive_scan`
  - `exclusive_rscan`
* `indices`, `replicate`, `len`, `range`
* `shift`, `rotate`
* `sort`

Scalar functions
----------------
* `exp`, `abs`, `sqrt`, `log`
* Casting functions: `int32`, `int64`, `float32`, `float64`,
`cast_to`, `cast_to_el`
* Generic bounds: `max_bound`, `min_bound`


[prelude]: https://github.com/copperhead/copperhead/blob/master/copperhead/prelude.py






