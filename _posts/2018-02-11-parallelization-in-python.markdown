---
title: Parallelization in Python
layout: single
author_profile: true
read_time: true
share: true
date: '2018-02-11 18:37:01 -0800'
categories: coding
---

Recently, incredible changes to computationally intensive fields have been brought about by insanely powerful systems designed for parallel processing. Deep learning is all the rage. Parallelization is way simpler to implement than it used to be. And yet I think many of us in science who program at least semi-regularly tend to avoid writing parallel code because it's easy to feel like you'll go down the rabbit hole searching through documentation on how to do it properly. We even avoid writing efficient code because we can get away with it. These days computers are so fast that the overhead associated with doing something in an inefficient way oftentimes doesn't matter much. That being said, Python's multiprocessing library has made it so easy to implement parallelization that we no longer have any excuse not to use it all the time.

Here's the problem: we have a dataset composed of a bunch of different objects, and we'd like to process each object independently of one another. The easy way to process the dataset is to loop over each element in the dataset, processing each item sequentially:

{% highlight python %}
newDataset = []
for item in dataset:
    newDataset.append(processItem(item))
{% endhighlight %}

or using a one-liner list comprehension, `newDataset = [processItem(item) for item in dataset]`. We could also just as easily use the somewhat uglier `map` builtin: `newDataset = list(map(processItem, dataset))`. `map(f, args)` simply applies the function `f` to each element in `args`, effectively achieving the same result as the list comprehension above.

What we'd really like to do is make the Python interpreter apply our `processItem` function to the items in `dataset` across multiple CPU cores simultaneously - this is exactly what the multiprocessing module does. All we have to do is include the module along with some boilerplate:

{% highlight python %}
from multiprocessing import Pool

workerPool = Pool()
newDataset = list(workerPool.map(processItem, dataset))
workerPool.close()
workerPool.join()
{% endhighlight %}

Let's break it down:
* `Pool()` creates a set of worker processes which will be given jobs to do later on. By default creating the same number of workers as CPU cores in your machine.
* We give these workers the job of evaluating `processItem` on each element of `dataset`, converting the output mapping to a list.
* `Pool.close()` tells the workers that we are done asking them to do work, and that they need to exit. `Pool.join()` stops execution of the script and waits for the workers to exit.

I'm glossing over a few details here, and there are admittedly a few lines of boilerplate, but it's not a lot of effort for what can be an enormous performance gain.