---
title: Finding a python interpreter with vscode
layout: single
author_profile: true
read_time: true
share: true
date: '2019-11-22 14:30:00 -0800'
categories: coding
toc: false
---

[vscode](https://code.visualstudio.com) is my favorite IDE, but for as long as I can remember, the python extension has
had problems finding python interpreters in custom locations, i.e. somewhere besides `/usr/bin/` etc. This makes the
vscode-python extension effectively useless, because you don't get any linting or introspection. For me this is
always a big pain because I like to build python myself and put the interpreter in a custom location. Every time I
install vscode on a new computer or vm, I have to waste time trying to figure out how to get it to find the python
interpreter I want it to. And despite the massive number of stackoverflow and github issues about this, I've never
been able to find anything that has helped (and it's clearly an issue which has persisted for years at this point).

Anyway, I have a solution; it's important to note that my python was build with `--enable-shared`, so YMMV.

1. Make a bash script to disable gpu, which causes problems on linux as far as I'm aware: `/usr/bin/vscode`, `/usr/local/bin/vscode`, or wherever you want to launch it from. I launch everything from `dmenu`, so either place would work:

```bash
#!/bin/bash
<path to vscode> --disable-gpu $@
```

2. Make another file, `/etc/ldconfig.so.conf.d/python38` with only the location of `libpython`. You can also just add a line to `/etc/ldconfig.so.conf`, if you don't have `/etc/ldconfig.so.conf.d/`:

```
<path to python>/lib
```

3. Run `sudo ldconfig` to update shared libraries


That's it. I haven't tested this with virtual environments, but presumably it would work the same way. If you get stuck,
you can always open up the `output` panel in vscode, and select `Python Language Server` from the drop down menu
to see if there are any useful error messages there. Hope this helps!