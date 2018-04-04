---
title: Numba+CUDA on Windows
layout: single
author_profile: true
read_time: true
share: true
date: '2018-04-03 14:30:00 -0800'
categories: coding
---

I've been playing around with Numba lately to see what kind of speedups I can get for minimal effort. If you haven't heard of it, [Numba][numba] is a just-in-time compiler for python, which means it can compile pieces of your code just before they need to be run, optimizing what it can. I've always been partial to Cython as a way to optimize code because it's extremely widely used, but lately I've come across a few examples where [Numba has performed _better_ than Cython][jakevdp]. The fact that Numba optimizes pure python code is enticing as well. The other thing that appealed to me about Numba was that it allows for the user to access the CUDA API for running code on a supported GPU. Although [PyCUDA][pycuda] also allows for GPU computation, you still have to write CUDA C kernels _as python strings_. Numba doesn't have this issue, so I wanted to learn a little more.

Installing Numba is seemingly easy if you're running Anaconda: `conda install numba` and `conda install cudatoolkit`. I don't use Anaconda so I can't confirm if it really is that easy, but if you're using vanilla python it's a bit different:

1. `pip install numba`.
2. Install the [CUDA Toolkit][CUDATK]. I used v9.1.85.3.
3. Update to the latest NVIDIA driver.
4. Set environment variables:
    * NUMBAPRO_NVVM = &lt;cuda path&gt;/v9.1/nvvm/bin/nvvm64_32_0.dll
    * NUMBAPRO_LIBDEVICE = &lt;cuda path&gt;/v9.1/nvvm/libdevice/
5. Restart (maybe not necessary?)
    
And you're good to go. Thanks to this [thread][thread] on StackOverflow for the info.

[numba]: https://numba.pydata.org
[jakevdp]: https://jakevdp.github.io/blog/2013/06/15/numba-vs-cython-take-2/
[pycuda]: https://documen.tician.de/pycuda/
[CUDATK]: https://developer.nvidia.com/cuda-toolkit
[thread]: https://stackoverflow.com/questions/49021437/libnvvm-cannot-be-found