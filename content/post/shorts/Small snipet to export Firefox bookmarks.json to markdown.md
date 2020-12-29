---
title: "Small Python Snipet to Export Firefox Bookmarks To Markdown"
date: 2020-12-29T21:57:18+01:00
tags:
  - python
---
Firefox allows you to backup your bookmarks to a JSON file.
I wanted to quickly export it to markdown rather than the HTML offered by Firefox itself.

```python
import json
bdict = json.load(open('bookmarks-2020-12-29.json', 'r'))
```


```python
def parse_firefox_bookmark(bdict, header=""):
    if 'children' not in bdict.keys():
        try:
            # There are some nodes with no uri and string comparisons slows
            # the program so just ignore the exceptions.
            print('*', '[' + bdict['title'] + ']' + '(' + bdict['uri'] + ')') 
        except:
            pass
        return
    print(header, bdict['title'])
    for children in bdict['children']:
        parse_firefox_bookmark(children, header+'#')
```


```python
parse_firefox_bookmark(bdict)
```
Sample output.
```markdown
 
# Bookmarks Menu
## Mozilla Firefox
* [Help and Tutorials](http://www.mozilla.com/en-US/firefox/help/)
* [Customize Firefox](http://www.mozilla.com/en-US/firefox/customize/)
* [Get Involved](http://www.mozilla.com/en-US/firefox/community/)
* [About Us](http://www.mozilla.com/en-US/about/)
* [About Us](https://www.mozilla.org/en-US/about/)
* [Customize Firefox](https://www.mozilla.org/en-US/firefox/customize/)
* [Help and Tutorials](https://www.mozilla.org/en-US/firefox/help/)
* [Get Involved](https://www.mozilla.org/en-US/contribute/)
* [Help and Tutorials](https://support.mozilla.org/en-US/products/firefox)
## Software
* [The Last Statusline For Vim • kade killary](https://kadekillary.work/post/statusline/)
* [vimperator cheat sheet](http://sheet.shiar.nl/vimperator:)
```
