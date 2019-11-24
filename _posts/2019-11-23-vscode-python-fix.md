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
Anyway, here's my solution:

1. Make a bash script: `/usr/bin/vscode`, `/usr/local/bin/vscode`, or wherever you want to launch it from. I launch everything from `dmenu`, so either place would work:

```bash
#!/bin/bash
PATH=$PATH:<python install dir>/bin
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<python install dir>/lib # Only needed if you built python to use shared libraries
<path to vscode> --disable-gpu $@
```


That's it. I haven't tested this with virtual environments, but presumably it would work the same way. After launching
vscode from the terminal once, it seems to recognize the same interpreter in subsequent launches, even if they are not
launched from the terminal. Hope this helps!