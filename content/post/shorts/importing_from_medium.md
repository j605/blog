---
title: "Importing from Medium"
date: 2018-06-18T17:59:09+02:00
lastmod: 2018-06-18T17:59:09+02:00

---

I did not realize that I had an article and a draft stuck in [Medium](https://medium.com)
and  I thought how difficult would it be to yank it from there and import it to
Hugo. This post documents a short version of how to do it.

First, export all your data from Medium. This contains posts as well as other content that
you have contributed to the site: claps, bookmarks, topics etc. Since I wanted to
export just one post I went in to posts directory and found an HTML file corresponding
to that article.

Next, you would have to convert it to markdown to edit it suitably in Hugo's format.
This [blog](http://www.cantoni.org/2013/01/19/converting-html-to-text-markdown)
showed me that it was easier to use `pandoc` for converting to markdown. After
that conversion further edits have to be done to strip unnecessary formatting
text. I then proceeded to add a date to the post and added it to the site.
