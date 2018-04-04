---
title: Fixing Matplotlib's Scientific Notation
layout: single
classes: wide
author_profile: true
read_time: true
share: true
date: '2017-10-26 18:37:01 -0800'
categories: coding
---

I use matplotlib all the time, but one of the most common problems I run into is when my axes are plotted on a scale that uses scientific notation. This is especially annoying when you're trying to make two plots stacked on top of each other which share an x-axis. Here's a simple example of the issue:

{% highlight python %}
import matplotlib
matplotlib.use("Qt5Agg")
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 1000)
y = np.sin(x)
z = 1e12*(1-np.exp(-x))

fig = plt.figure()
fig.subplots_adjust(hspace=0.05)

upperAxes = fig.add_subplot(211)
upperAxes.plot(x, y, color='k', linestyle='-')
upperAxes.set_xticklabels([])
upperAxes.set_ylabel("y")

lowerAxes = fig.add_subplot(212)
lowerAxes.plot(x,z, color='r', linestyle='-')
upperAxes.set_xlabel("x")
upperAxes.set_ylabel("y")


plt.savefig("example.svg")
{% endhighlight %}

This code producese the following:

![bad_exponent]

You can immediately see the problem - the exponent is jammed into the plot above. This behavior turns out to be harder to fix than you think. If you spend some time googling (which you've probably already done if you're here), most of the solutions to this problem suggest using some variation on `axis.yaxis.get_offset_text().set_y(yn)` to shift the exponent label to new location *yn*. I have two main issues with this solution:

* It only allows you to shift the label along one direction, not actually place it wherever you want
* Depending on the placement, it can be ambiguous whether the exponent is associated with the top plot or the bottom plot

[pyqtgraph][pyqtgraph] does this *so* much better than matplotlib by letting users specify units for the axes. Then the exponent is affixed to the axis units label by appropriate SI prefixes, i.e. an exponent of 10<sup>9</sup> becomes a unit prefix G (for giga-). With this system in mind, we can sort of emulate that functionality by moving the exponent into the axis label, and using a callback function to update the label when the axes limits change:

{% highlight python %}
def label_offset(ax, axis="y"):
    if axis == "y":
		fmt = ax.yaxis.get_major_formatter()
		ax.yaxis.offsetText.set_visible(False)
		set_label = ax.set_ylabel
		label = ax.get_ylabel()

	elif axis == "x":
		fmt = ax.xaxis.get_major_formatter()
		ax.xaxis.offsetText.set_visible(False)
		set_label = ax.set_xlabel
		label = ax.get_xlabel()

	def update_label(event_axes):
		offset = fmt.get_offset()
		if offset == '':
			set_label("{}".format(label))
		else:
			set_label("{} ({})".format(label, offset))
		return

	ax.callbacks.connect("ylim_changed", update_label)
	ax.callbacks.connect("xlim_changed", update_label)
	ax.figure.canvas.draw()
	update_label(None)
	return
{% endhighlight %}

Now, just call this function on your axis when you instantiate them, and it will take care of the rest. From the example above we add the following two lines to the bottom:

{% highlight python %}
label_offset(lowerAxes, "y")
label_offset(upperAxes, "y")
{% endhighlight %}

![better_exponent]

When we use `plt.show()`, the axes and labels work exactly how we want them to. Best of all, the factor of 10<sup>12</sup> will change appropriately if we interact with the plot.

This isn't a perfect solution, but it's a good start. We need to be careful not to call the `label_offset` function on an axis more than once, because we are actively altering the axis label, which means if we do call it more than once on the same axis it might append multiple copies of the exponent into the label, which isn't what we want. It also means we can't actually set the axis label after this function is called, since that would replace the exponent. As a first attempt at a solution, however, it works pretty well.


[pyqtgraph]: https://www.pyqtgraph.org

[bad_exponent]: /assets/images/bad-exponent-example.svg
{: .align-center}
[better_exponent]: /assets/images/better-exponent-example.svg
{: .align-center}