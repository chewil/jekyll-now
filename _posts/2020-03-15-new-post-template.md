---
layout: post
title: Template File For New Posts
published: false
date: 2020-03-15 15:48:00
category: template test
tags: template test
---

##Predefined Global Variables

There are a number of predefined global variables that you can set in the front matter of a page or post.

|VARIABLE|DESCRIPTION|
|---|---|
|`layout`|  If set, this specifies the layout file to use. Use the layout file name without the file extension. Layout files must be placed in the `_layouts` directory.<br><br> - Using *`null`* will produce a file without using a layout file. This is overridden if the file is a post/document and has a layout defined in the [front matter defaults](https://jekyllrb.com/docs/configuration/front-matter-defaults/).<br><br> - Starting from version 3.5.0, using `none` in a post/document will produce a file without using a layout file regardless of front matter defaults. Using `none` in a page will cause Jekyll to attempt to use a layout named "none".|
|`permalink`| If you need your processed blog post URLs to be something other than the site-wide style (default `/year/month/day/title.html`), then you can set this variable and it will be used as the final URL. |
|`published`| Set to false if you don’t want a specific post to show up when the site is generated. 

