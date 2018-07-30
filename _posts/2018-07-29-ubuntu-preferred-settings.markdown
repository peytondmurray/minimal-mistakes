---
title: Better Ubuntu
layout: single
author_profile: true
read_time: true
share: true
date: '2018-07-29 14:30:00 -0800'
categories: coding
---

I recently switched over to linux on my laptop because I want a native linux environment for work. I used to run linux at home a few years back, but I got tired of troubleshooting it all the time. Don't get me wrong, it's great for some stuff, but the more "user-friendly" linux distros (re: anything Debian based) have at times been rough around the edges. Over the years I've come to realize

1. I don't want to spend all day googling for answers, reading man pages, or searching stack overflow to get basic functionality working
2. Stable is better than bleeding edge
3. Help is harder to find the further away from Ubuntu you get
4. Getting stuff done is more important than configuring the desktop

which rules out pretty much everything except the most basic flavored Ubuntu. So naturally I installed Lubuntu, because hey, it's exactly the same as normal Ubuntu except without the slow desktop environment, right? After fixing my laptop sound buttons misbehaving, finding out that nobody knows how to get rid of the two wifi icons that mysteriously appear in LXPanel, battling to get my touchpad to work and then realizing multitouch wasn't supported, and (finally), after installing i3, realizing that my screen brightness buttons didn't work anymore, I gave up and clean-installed plain old Ubuntu 18.04.

I can actually say that it's pretty good. Everything kinda...works. It could be better, though, which is why I'm writing this. This will get a fresh Ubuntu install up and running quickly:

{% highlight bash %}
sudo tee -a $HOME/.bash_aliases "alias python="$HOME/python36/bin/python3 '"
gsettings set org.gnome.desktop.interface enable-animations false
{% endhighlight %}

A few notes:
* 