---
title: Profiling (neo)vim for speed
layout: single
author_profile: true
read_time: true
share: true
date: '2020-10-30 14:30:00 -0800'
categories: coding
toc: false
---

I switched from VSCode to neovim this year for a few reasons:

1. I didn't like using the mouse to navigate around my code.
2. Although it is pretty quick for what it does, VSCode is bloated (it's
   electron based; I didn't like the idea of running an entire browser when I
   just wanted to edit a text file...)
3. Microsoft has poured resources into VSCode, which on one hand is really cool
   but on the other hand means that VSCode will soon be the only game in town
   in terms of IDEs.

One of the great strengths of (neo)vim is its _extensibility_ - there are what
seem like thousands of plugins available to use right now which make it at least
as capable as PyCharm or VSCode if not more so. With plugin managers like
[vim-plug](https://github.com/junegunn/vim-plug) it's incredibly easy to get
started customizing neovim. With a few plugins, neovim is featureful and very
fast.

Occasionally, though, you'll install a plugin that slows things down noticeably.
If you have a lot of plugins, like me, it's going to be hard to tell which
one is responsible for ruining speedy, wonderful neovim. Fortunately there's an
easy way to find out what's slowing you down:

```vimscript
:profile start prof.log
:profile func *
:profile file *
<do stuff that's slow>
:profile pause
```

This creates a file `prof.log` in the current directory which contains profiling
information about every vim function that was run between the `:profile start`
and `:profile pause` commands. I found the colorizer plugin was slowing me down
when I switched between splits. This plugin searches each file for hexadecimal
color codes and colors the background accordingly; it's useful for web
development. A quick look at the github repo for colorizer told it runs
synchronously every time I entered a buffer. At this point the project is
essentially abandoned. In the repo readme, the authors suggest installing
hexokinase instead. After switching over to hexokinase neovim performance
improved immensely, returning to what I'd expect for a purely text-based editor.

Thanks to
