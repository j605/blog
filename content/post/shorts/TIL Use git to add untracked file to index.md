---
title: "TIL Git: Add Untracked File to Index"
date: 2019-08-14T16:10:24+02:00
tags:
  - git
categories:
  - dgplug
---
Recently I was browsing through `git add`'s manual to find out how to split hunks and stage them.
I found that there is an option called `--intent-to-add` which is pretty useful.
Like it says, one can use it to add an untracked file to git's index but not stage the file.

# Why is it useful?
An untracked file doesn't show up in diffs and cannot be staged by hunks since nothing is in the index yet.
So by adding it to the index, we just track it and can stage when ready.

# Example
This blog is in a git repo, so lets track the current post.
When the file is not yet tracked, we see
```git
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        content/post/shorts/TIL Use git to add untracked file to index.md
```

Now when you add it to the index,
```git
$ git add --intent-to-add content/post/shorts/TIL\ Use\ git\ to\ add\ untracked\ file\ to\ index.md
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        new file:   content/post/shorts/TIL Use git to add untracked file to index.md

no changes added to commit (use "git add" and/or "git commit -a")
```
With this you can look at a proper output of `git diff`,
```diff
diff --git a/content/post/shorts/TIL Use git to add untracked file to index.md b/content/post/shorts/TIL Use git to add untracked file to index.md
new file mode 100644
index 0000000..ff5d586
--- /dev/null
+++ b/content/post/shorts/TIL Use git to add untracked file to index.md 
@@ -0,0 +1,45 @@
+---
+title: "TIL Git: Add Untracked File to Index"
+date: 2019-08-14T16:10:24+02:00
+tags:
+  - git
+categories:
+  - dgplug
+---
+Recently I was browsing through `git add`'s manual to find out how to split hunks and stage them.
+I found that there is an option called `--intent-to-add` which is pretty useful.
+Like it says, one can use it to add an untracked file to git's index but not stage the file.
+
+# Why is it useful?
+An untracked file doesn't show up in diffs and cannot be staged by hunks since nothing is in the index yet.
+So by adding it to the index, we just track it and can stage when ready.
+
+# Example
+This blog is in a git repo, so lets track the current post.
+When the file is not yet tracked, we see
+```git
+$ git status
+On branch master
+Your branch is up to date with 'origin/master'.
+
+Untracked files:
+  (use "git add <file>..." to include in what will be committed)
+
+        content/post/shorts/TIL Use git to add untracked file to index.md
+```
+
+Now to when you add it to the index,
+```git
+$ git add --intent-to-add content/post/shorts/TIL\ Use\ git\ to\ add\ untracked\ file\ to\ index.md
+$ git status
+On branch master
+Your branch is up to date with 'origin/master'.
+
+Changes not staged for commit:
+  (use "git add <file>..." to update what will be committed)
+  (use "git checkout -- <file>..." to discard changes in working directory)
+
+        new file:   content/post/shorts/TIL Use git to add untracked file to index.md
+
+no changes added to commit (use "git add" and/or "git commit -a")
+```
```
I guess this has gone enough meta so lets wrap it up.
Enjoy and explore git, you will always find something new :)
