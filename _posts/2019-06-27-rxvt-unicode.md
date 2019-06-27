---
title: Enhanced rxvt-unicode
layout: single
author_profile: true
read_time: true
share: true
date: '2019-06-16 14:30:00 -0800'
categories: coding
toc: true
---

I really like rxvt-unicode (often called urxvt) as a terminal emulator. I can customize nearly everything I want without too much effort, and it seems to do everything I could want a terminal to do. Over time, the changes I've made to my configuration files have piled up, and now it's pretty far from a vanilla setup. Here are a few of my favorite changes.

# Enable unicode input with `Ctrl+Shift+u`

For anyone who doesn't use unicode symbols very often, a lot of Linux software accepts [unicode input][unicode] input sequences. Usually, you press `Ctrl+Shift+u`, followed by the unicode sequence, then `Enter`. It hasn't been until relatively recently that I've found use for unicode, but I've found that a lot of things, and especially math, can be more clearly and concisely expressed with symbols than with whole words. Consider this example, which converts spherical coordinates to cartesian, making use of θ and ϕ:

{% highlight python %}
import numpy as np
def spherical(r, ϕ, θ):
    x = r*np.sin(θ)*np.cos(ϕ)
    y = r*np.sin(θ)*np.sin(ϕ)
    z = r*np.cos(θ)
    return (x, y, z)
{% endhighlight %}

The language is so much more expressive when you use all the symbols available to you! Anyway, by default urxvt allows you to enter unicode symbols by pressing `Ctrl+Shift+<unicode sequence>`, which is different than usual (I don't want to memorize another key combination if there's no good reason to) and annoying, as it doesn't leave both hands free to type the `<unicode sequence>`. Fortunately, [Jeff Epler][post] wrote a [nice little script][script] to enable the usual unicode shortcut. To install it:

1. Copy the script to `~/.urxvt/ext/unichr`, making a directory if necessary
2. The script needs to be enabled in urxvt, and the default unicode input sequence ("ISO 14755 mode") needs to be disabled. Edit your `~/.Xresources`, adding the following lines:

```
URxvt.iso14755:					  false
URxvt.iso14755_52:	        		  false
URxvt.perl-ext:					  unichr
```

3. Enable the new settings by doing `xrdb ~/.Xresources`

After restarting urxvt, you should find that `Ctrl+Shift+u` works! Jeff's script shows a little preview of the symbol you've entered as you type the unicode sequence, too.

# `Ctrl+Arrows` to move the cursor by word

I like to use `Ctrl+Arrows` to move the cursor word-by-word. By default, urxvt has some other key combination, but it's been so long since I've used vanilla urxvt that I've forgotten what it is. If you prefer `Ctrl+Arrows`, open `~/.Xresources` and add the following lines:

```
URxvt.keysym.Control-Left:        \033[1;5D
URxvt.keysym.Shift-Control-Left:  \033[1;6D
URxvt.keysym.Control-Right:       \033[1;5C
URxvt.keysym.Shift-Control-Right: \033[1;6C
URxvt.keysym.Control-Up:          \033[1;5A
URxvt.keysym.Shift-Control-Up:    \033[1;6A
URxvt.keysym.Control-Down:        \033[1;5B
URxvt.keysym.Shift-Control-Down:  \033[1;6B
```

# Glorious transparency

Check this out:

![ideal_transparency]

See how the background in the highlighted window is transparent, but the text is not? You can't do this by modifying Compton transparency settings, since those settings affect the entire window including the text. Instead, open up `~/.Xresources` again and add:

```
URxvt.depth: 32
URxvt.background: [40]#11292e
```

Here, `[40]` is the transparency level (0-100), and `#11292e` is any hex color code. The `depth` argument adjusts the color bit depth, and somehow enforces alpha channel support. I'm not sure why or how, [it's just what the docs tell you to do][docs].

[ideal_transparency]: /assets/images/rxvt-unicode/ideal_transparency.png

[unicode]: https://en.wikipedia.org/wiki/Unicode
[post]: https://emergent.unpythonic.net/01340900642
[script]: /assets/files/unichr
[docs]: https://linux.die.net/man/1/urxvt