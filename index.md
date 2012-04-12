---
layout: page
title: Copperhead
tagline: Data Parallel Python
---
{% include JB/setup %}

Copperhead is a project to bring data parallelism to Python. We define
a small functional, data parallel subset of Python, which we then
dynamically compile and execute on parallel platforms. Currently, we
target NVIDIA GPUs, as well as multicore CPUs through OpenMP and
Threading Building Blocks (TBB).

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


In this example, when axpy is called, the Copperhead runtime
intercepts the call and compiles the function to CUDA and OpenMP,
respectively. It also converts the input arguments to cuarray
objects managed by the runtime, which are lazily copied to and from
heterogeneous memory spaces. The programmer specifies the execution
place using the with construct above.

