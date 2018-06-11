---
author: j605
comments: true
date: "2011-10-22T07:36:41Z"
link: https://j605.wordpress.com/2011/10/22/posting-from-vim-using-vimpress-2/
slug: posting-from-vim-using-vimpress-2
tags:
- blogging
- vimpress
- vimrc
title: Posting from vim using vimpress
wordpress_id: 15
---

This is my first post from vim using vimpress.Because of having a low bandwidth connection, I was forced to think for an alternative and vim being as versatile  it can getto throw answers to everything we can think of. Okay, now getting on with the topic at hand what is vimpress and what does it do ?


<blockquote>Vimpress is a plugin for vim which is used to post in your blog.</blockquote>


Here's how we start, download the script from [vim's site](http://www.vim.org/scripts/script.php?script_id=1953).Put the files blog.vim and blogsyntax.vim in ~/.vim/plugin folder ,if the folder does not exist create one. Open the blog.vim file and fill in your your username and password and site url in their respective places. Open vim and type in CmdLine


```vim
:so ~/.vim/plugin/blog.vim
:so ~/.vim/plugin/blogsyntax.vim
```


now that you have successfully setup vimpress, you can happily blog away.
