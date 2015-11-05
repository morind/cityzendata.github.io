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

*Note: the version of Jekyll on Ubuntu repositories **isn't up todate** and shouldn't be used*


### Development mode

To serve the blog in preview mode, use:

```text
jekyll serve
```
The preview mode automatically updates after any modification.


To build the static version of the blog, use:

```text
jekyll build
```

If you want it to rebuild the static site after each modification, add the `--watch`flag:

```text
jekyll build --watch
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

### Publish your post

As usual, you should fork the [blog repository](https://bitbucket.org/cityzendata/blog), 
push to your fork and do a pull request.


### Working with drafts ###


Drafts are posts without a date. They’re posts you’re still working on and don’t want to publish yet. To create a new draft, simply put it into the 
`_drafts` folder, without prefixing its filename with a date:

```text
──_drafts/
   └──a-draft-post.md
```
To preview your drafts, simply run `jekyll serve` or `jekyll build` with the `--drafts` switch. Each draft post will be assigned the value 
modification time of the draft file for its date, and thus you will see currently edited drafts as the latest posts.
