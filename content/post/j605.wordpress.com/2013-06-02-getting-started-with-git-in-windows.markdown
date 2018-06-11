---
author: j605
comments: true
date: "2013-06-02T14:25:10Z"
link: https://j605.wordpress.com/2013/06/02/getting-started-with-git-in-windows/
slug: getting-started-with-git-in-windows
tags:
- git windows bitbucket
title: Getting started with Git in Windows
wordpress_id: 39
---

I am going with git-bash since it doesn't mess up my Windows installation. Also it is more than enough for standard tasks. Get git from [http://git-scm.com/download/win](http://git-scm.com/download/win). Install git-bash, its a straight forward process. 

We need to generate ssh keys for communicating with either  [http://github.com](http://github.com) or [http://bitbucket.org](http://bitbucket.org). If you already have keys from a Linux or Mac, you can use that. Just copy your ~/.ssh to your home directory in Windows. To generate new keys, open git-bash and do

<blockquote>ssh-keygen
> 
> </blockquote>

Have a decent pass-phrase, or else it would just defeat the purpose. Lastly copy your public key and publish it to Github or Bitbucket.

PS: If you are going to use Github, they have a native application for Windows that will guide you through the process.
