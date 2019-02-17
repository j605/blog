---
title: "(Neo)vim Macro to Create Numbered Lists"
date: 2019-02-17T13:05:22+01:00
tags:
  - vim
categories:
  - dgplug
---

I usually encounter this when saving notes about list of items that are not numbered but are generally better off being itemized.
Since this is such a common scenario I did find a couple of posts[^1] [^2] that explained the method but they had edge cases which were not handled properly.

Say you want to note down a shopping list and then decide to number it later,
```
Soy milk
Carrots
Tomatoes
Pasta
```

Start off by numbering the first line and then move the cursor to the second line.
Then, the steps are

1. Start recording the macro into a register, say `a`, by using `qa`.
2. Press `k` to go one line up.
3. `yW` to copy one big word, in this case “1. ”.
4. Then `j` to come one line down and `|` to go to the start of the line.
5. Use `[p` to paste before and `|` to go the beginning.
6. To increment, `Ctrl+A` and then `j` and `|` to set it up for subsequent runs.

To run the macro, go to the next line and execute `@a`.
For repeating it 3 times, you can use `3@a`.

[^1]: https://stackoverflow.com/a/4224521
[^2]: https://techedemic.com/2013/04/15/using-vim-to-create-a-numbered-list-e-g-list-of-ip-addresses/
