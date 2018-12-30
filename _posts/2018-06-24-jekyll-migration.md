---
id: 1234
title: s/wordpress/jekyll/g
date: 2018-06-24T14:46:00+00:00
author: scott
layout: post
permalink: /wordpress-to-jekyll
categories:
  - Tech
tags:
  - Open-source
  - meta
---
I've migrated this blog from Wordpress to a statically-generated site made with Jekyll.

I was able to (sort of) automate this using benbalter's [Wordpress to Jekyll exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter). It managed to at least export the posts and top-level pages, which worked alright, but they required some post-processing to remove the HTML-escaped characters and changing HTML tags to native Markdown:

```
$ find -name *md | while read f; do sed -i "s|#8217;|'|g" $f; done
$ find -name *md | while read f; do sed -i "s|<pre>|```\r|g" $f; done
$ find -name *md | while read f; do sed -i "s|</pre>|\r```|g" $f; done
[... etc ...]
```

One post about embedding Jinja2 inside of a Jekyll blog (escaped for Wordpress!) was a casualty of the migration, but I'm working on getting that back!


### Other magic

I wanted to keep my broken posts stored in the blog's git repo, but renaming the broken posts to `foo.md.broken` or moving them to a sub-directory like `_broken_posts` didn't stop Jekyll from trying to render them:

```
 ❯ jekyll serve

Configuration file: /Users/scott/src/scottc.me/_config.yml
            Source: /Users/scott/src/scottc.me
       Destination: /Users/scott/src/scottc.me/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
  Liquid Exception: Liquid syntax error (line 26): Unknown tag 'endhighlight' in asdf.md
jekyll 3.8.3 | Error:  Liquid syntax error (line 26): Unknown tag 'endhighlight'
```

Whoops.

Turns out you can exclude certain directories in your `_config.yml`:

```
exclude:
  - _broken_posts
```

Now it works. woot!

---


Update: [the broken post is back!](http://127.0.0.1:4000/recursion/markdown/jinja2/jekyll/meta/2015/09/08/jinja-inside-of-jekyll-is-so-meta.html)
