layout: post
title: How to parse markdown file with images to hexo asset_img format
tags:
  - Hexo
categories:
  - Others
  - Hexo
date: 2018-11-10 15:24:00
---
{% asset_img a.jpg %}

In Hexo doc about [asset folders](https://hexo.io/docs/asset-folders.html), it shows two ways to insert image. One way is put images into one foler, like `images` and use them through `![This is title](/images/image.jpg)`. Another way is seperate images in each folder for each post. I think this is better for management and update, and we don't need to pay much attention on image file names. In short words, when we enable `post_asset_folder: true`, then when we create post, there's also created the same name folder to save images. We can use like `{% asset_img image.jpg This is title %}`.

What if I edit the markdown out of Hexo scope, we can only use `![This is title](/images/image.jpg)` to show to others. But when I want to post my article on Hexo site, I have edit the content of each image format, which is tedious. So the thing becomes clear. I need to change

```
1. This is some content
![](directory/regist.png)
2. This is some other content ![This is title](directory (contains bracket)/user_registered.png)
```

into

```
1. This is some content
{% asset_img regist.png %}
2. This is some other content {% asset_img user_registered.png This is title %}
```

First, I don't want to use advanced programing language. I just need a simple command to finish it. So `sed` can search and replace content in file. 

Second, I need to get file name in `![This is title](directory (contains bracket)/image.jpg)`, ignore directory name and parse file name into `{% asset_img image.jpg %}`.

Let's seperate `![This is title](directory (contains bracket)/image.jpg)` into parts: There's a `!` in front, and append`[]`, and we need content in `[]` to be as title, then goes `()`, and always a `/` in `()`. It's like **`![](a/b)`**, besides, `a` might contain bracket, because post title might contain bracket.
* `!` matches the character `!` literally
* `\[` matches the character `[` literally
* We need content inside `[]`, which is , we need content in front of `]`. So it should be: `([^]]*)`. (Explaination: `()` in regex means group, we can use the content in the group with `\1`, `\2` etc. `[abc]` means single character `a`, `b`, `c`. `[^abc]` means a character except `a`, `b`, `c`. `*` means matches 0 or more times.) So `([^]]*)` means we want the content as group 1, which the content should not contain `]`.
So right now, `!\[([^]]*)`, we have matched `![This is title`. Let's continue.
* `]` matches the character `]` literally
* `\(` matches the character `(` literally
* We want to skip content before `/`, vice versa, we accept the characters which are not `/`, so it should be: `[^\/]*`.
* `\/` matches the character `/` literally
So right now, `!\[([^]]*)]\([^\/]*\/`, we have matched `![This is title](directory (contains bracket)/`. Let's continue.
* We want to keep file name, the content after `/`, in front of `)`, which means the content should not contain `)`. So we need `([^)]*)`, the content is kept as group 2.
* In the end, we add `\)`, matches the character `)`.
So the regex is : `!\[([^]]*)]\([^\/]*\/([^)]*)\)`.

But `sed` uses Basic Regular Expressions (BRE) by default, which uses `\(` and `\)` for group capturing, not just `(` and `)` as used in Extended Regular Expressions (ERE). So you should be careful when treating `(` and `)`. If you want to match `(` literally in `sed`, just use `(`. If you need group, use `\(`. This is not good as we need to change the regex we created, it might cause mistakes. Good news is `sed` has a parameter `-E` to use Extended Regular Expressions.

The syntax is:

```
sed -E -i.bak 's/!\[([^]]*)]\([^\/]*\/([^)]*)\)/{% asset_img \2 \1 %}/g' file
```

or 

```
sed -i.bak 's/!\[\([^]]*\)]([^\/]*\/\([^)]*\))/{% asset_img \2 \1 %}/g' file
```

* `-i.bak` will backup original file into file.bak.
* `'s/old-text/new-text/g'`, `s` is the substitute command of set for find and replace.
* group 1 is `([^]]*)`, group 2 is `([^)]*)`

So above command means find `old-text` and replace file content with `new-text`, also backup fileinto file.bak.

And [here](https://regex101.com/r/uIBKVu/3) is online expression with example.

[[1]nixCraft - How to use sed to find and replace text in files in Linux / Unix shell](https://www.cyberciti.biz/faq/how-to-use-sed-to-find-and-replace-text-in-files-in-linux-unix-shell/)

[[2]Stackoverflow - How to extract string in file and replace specific pattern text](https://stackoverflow.com/questions/51406323/how-to-extract-string-in-file-and-replace-specific-pattern-text)

[[3]Stackoverflow - Sed error “\1 not defined in the RE” on MacOSX 10.9.5](https://stackoverflow.com/questions/28072190/sed-error-1-not-defined-in-the-re-on-macosx-10-9-5)

[[4]Stackoverflow - .bash_profile sed: \1 not defined in the RE](https://stackoverflow.com/questions/24717676/bash-profile-sed-1-not-defined-in-the-re)