---
title: Spotify (and widevine) in Manjaro
layout: single
author_profile: true
read_time: true
share: true
date: '2018-1-13 14:30:00 -0800'
categories: coding
---

Ever since I started using Manjaro as my primary desktop I've been unable to get the Spotify web player to run in Vivaldi (actually any web browser, but Vivaldi is my favorite). So I've been stuck using the awful native Spotify player available on the AUR. I'm happy to report that I've just figured out how to get the web player working, and it's super easy.

The Spotify web player relies on a plugin (?) called widevine which allows DRM-protected content to be played. For reasons I'm sure are important to somebody, this plugin doesn't install when you install Vivaldi from the AUR. In fact, if you `ls -l /opt/vivaldi` you'll notice there's a symbolic link libwidevinecdm.so which points to `/opt/google/chrome/libwidevinecdm.so`. For me there was no file at this location, but a quick search turned up this [help page][help] which talks about enabling streaming of DRM-protected content. Helpfully, the Vivaldi team has provided a [script][script] which downloads and installs the plugin for you. After running the script and restarting Vivaldi, the problem was solved. Although I don't have Netflix or HBO, presumably this fixes playback issues with those platforms as well. Enjoy!

[help]: https://help.vivaldi.com/article/netflix-on-linux/
[script]: https://gist.github.com/ruario/3c873d43eb20553d5014bd4d29fe37f1