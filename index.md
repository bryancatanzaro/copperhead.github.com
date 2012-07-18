---
layout: page
title: Copperhead
tagline: Data Parallel Python
---
<a href="http://github.com/copperhead/copperhead"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://s3.amazonaws.com/github/ribbons/forkme_right_red_aa0000.png" alt="Fork me on GitHub"></a>

Overview
--------
Copperhead is a project to bring data parallelism to Python. We define
a small functional, data parallel subset of Python, which we then
dynamically compile and execute on parallel platforms. Currently, we
target NVIDIA GPUs, as well as multicore CPUs through OpenMP and
Threading Building Blocks (TBB).

Copperhead is a project of [NVIDIA Research][nvr].

Example
-------
Here's a simple Copperhead program:
{% highlight python %}
from copperhead import *
import numpy as np

@cu
def axpy(a, x, y):
  return [a * xi + yi for xi, yi in zip(x, y)]

x = np.arange(100, dtype=np.float64)
y = np.arange(100, dtype=np.float64)

with places.gpu0:
  gpu = axpy(2.0, x, y)

with places.openmp:
  cpu = axpy(2.0, x, y)
{% endhighlight %}


In this example, when `axpy` is called, the Copperhead runtime
intercepts the call and compiles the function to CUDA and OpenMP,
respectively. It also converts the input arguments to `cuarray`
objects managed by the runtime, which are lazily copied to and from
heterogeneous memory spaces as needed. The programmer specifies the execution
place explicitly. Compilation is cached to minimize runtime overhead.

Source
------

You can download the source here:
[https://github.com/copperhead/copperhead][github]

Contributors
------------

The primary contributors to Copperhead are [Bryan
Catanzaro][bcc] and [Michael
Garland][mjg].

[github]: https://github.com/copperhead/copperhead
[nvr]: http://research.nvidia.com "NVIDIA Research"
[bcc]: http://catanzaro.name "Bryan Catanzaro"
[mjg]: http://mgarland.org "Michael Garland"