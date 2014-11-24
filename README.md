# Cityzen Data's blog

The static, markdown based, Jekyll powered Cityzen Data's blog

## So what is Jekyll, exactly?

Jekyll is a simple, blog-aware, static site generator. It takes a template directory containing raw text files in various formats, runs it through [Markdown](http://daringfireball.net/projects/markdown/) and [Liquid](https://github.com/Shopify/liquid/wiki) converters, and spits out a complete, ready-to-publish static website suitable for serving with your favorite web server. Jekyll also happens to be the engine behind [GitHub Pages](http://pages.github.com/).


## How to use

The full doc on Jekyll is available on [Jekyll's doc site](http://jekyllrb.com/docs/home/).


### Install Jekyll engine.

Using ruby gems:

```text
gem install jekyll
```

In debian/ubuntu:

```text
sudo apt-get install jekyll
```


### Development mode

To serve the blog in preview mode, use:

```text
jekyll serve --watch
```

In *watch* mode, Jekyll will scan the source file and re-generate the blog when files changes.    


### Write a new post

To write a new post, you add a new file to `_posts`.

Filename must respect the naming convention:

```text
YYYY-MM-DD-title-with-dashes.markdown
```

The markdown files must include a normalized header:

```text
---
layout:     post
title:      "A nice title"
subtitle:   "And the explanation thats follows it"
date:       2014-06-10 12:00:00
author:     "Your name here"
header-img: "img/post-bg-01.jpg"  
---    
```

The image is a header image, suggested sizes are 1900x600 or 1600x500.

The content of the post is written in markdown.

Images should be placed on  a directory inside `img`, following this structure:


```text
──img
  └──YY 
     └──MM
        ├── img01.jpg
        └── img02.jpg
```

