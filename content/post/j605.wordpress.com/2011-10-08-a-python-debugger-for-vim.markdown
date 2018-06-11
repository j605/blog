---
author: j605
comments: true
date: "2011-10-08T10:23:39Z"
link: https://j605.wordpress.com/2011/10/08/a-python-debugger-for-vim/
slug: a-python-debugger-for-vim
title: A python debugger for vim
wordpress_id: 8
---

"vim-debug" is a python debugger that is integrated into vim. This works like any other debugger but you don't have to come out of vim to debug.Before we get started ensure that the [python-pip](http://packages.ubuntu.com/search?keywords=python-pip) package is installed in your distro.For installing open a terminal and type :


```sh
sudo apt-get install python-pip
```


The package pip is an improved python package installer.Next we move onto installing the plugin which are essentially python scripts. To install type:


```sh
sudo pip install dbgp
sudo pip install vim-debug
install-vim-debug.py
```


Like any other vim command you type this command in comdline-mode.Here you start the session by the command
Dbg as vim does not seem to accept lower case characters at the beginning of a user defined command.After
the session is started basic ways of operating dbg is shown.For further information on how to operate dbg
you can see the video by Jared Forsyth who is the maintainer for the project.
[youtube=http://www.youtube.com/watch?v=kairdgZCD1U]

i hope Jared Forsyth comes back successfully from his mission.
