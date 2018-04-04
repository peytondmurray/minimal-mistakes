---
title: Jekyll On Ubuntu on Windows
layout: single
author_profile: true
read_time: true
share: true
categories: coding
---

When I first started my website, I tried the simplest approach. At the time, this meant just choosing a cool looking theme from the first list of HTML templates I came across, and then populating my site with my own crudely written HTML files. While in principle anyone can write a website this way, it actually ended up being way more work than I thought it should be. Mostly this comes down to the fact that besides the actual content the user has requested, modern websites have a lot of code for adding title bars, social media links, and other stuff. For me, that meant that each blog post on my simple website required an enormous amount of boilerplate which I often had to tweak by hand to tailor it for the particular page I was writing. Of course I'm not the first to have this issue, so smart and talented people invented static site generators, which basically create websites from simple, human-readable Markdown. There are a number of choices, but Jekyll is the one I went with because it seems to be the most widely used.

Setting up Jekyll on Windows was more difficult than I thought it would be, but only because I'm a dumbass and kept screwing up environment variables. The approach I ended up taking was to install Ubuntu on the Windows Subsystem for Linux (WSL), and then install Jekyll from there. I use a handy plugin [jekyll-admin][ja] which has a few dependencies that otherwise wouldn't be necessary, but the installation is pretty straightforward. From the shell, in one line:

{% highlight shell %}
sudo apt update; sudo apt upgrade; sudo apt install build-essential ruby-full libffi-dev zlib1g-dev libcurl4-openssl-dev; sudo gem install bundler jekyll;
{% endhighlight %}

For anyone else doing this: every single one of my issues getting this working stemmed from conflicts between windows and WSL environment variables. Importantly, the Windows PATH is automatically added to the bash environment variables during installation; for example, after setting up WSL running `ruby -v` from a bash prompt attempted to invoke the windows binary for the Ruby interpreter. If your installation isn't working, check this first.

[ja]: https://github.com/jekyll/jekyll-admin