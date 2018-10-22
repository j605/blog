---
title: "New Templates in Dolphin"
date: 2018-10-23T00:46:40+02:00
tags:
  - linux
---
I was using `kio-gdrive` to access my Google drive account using Dolphin (file manager).
Since these are mounted as a virtual filesystem, I was not able to save files in them directly from libreoffice or any external program.
So I thought creating a new document from dolphin and then editing an empty document would be easier to use.
But information was scant regarding how to put together.
I knew we needed a template which is just an empty file but I didn't know how to put all this together to make it show up in Dolphin's "Create New" context menu.

# Steps to get it working
The example assumes creation of an empty document (ODT file).
First create a template file by saving an empty document in `~/Templates`. This is just a suggested directory but any place would be fine.
As of `kf5`, the path for user templates is `~/.local/share/templates` which can be got from `kf5-config --path templates`.

So in `~/.local/share/templates`, create an application file like so
```ini
# ~/.local/share/templates/writer.desktop
[Desktop Entry]
Version=1.0
Name=Writer Document
Terminal=false
Icon=libreoffice-writer
Type=Link
URL=$HOME/Templates/Untitled 1.odt
```

After this Dolphin should pick this up and show this entry in the "Create new" menu.
![Context menu](/2018-10-23-01:09:08.png)
One has to take care to give proper extension to the files when naming them though since Google Docs won't like files without extension although they can opened from Drive into Docs.
