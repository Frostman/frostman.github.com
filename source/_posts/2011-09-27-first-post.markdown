---
layout: post
title: "First Post"
date: 2011-09-27 18:48
comments: true
categories: welcome
excerpt: Welcome! It is the first post in my personal blog. I have planned to write some articles about programming and technologies in it. I think that most of them will be written in english. You can read about blog engine after cut.
---
Welcome!

It is the first post in my personal blog. I have planned to write some articles about programming and technologies in it. I think that most of them will be written in english. 

This blog is based on [Octopress](http://octopress.org) blogging framework and hosted at Github pages.

You can subscribe to this blog via [rss](http://feeds.feedburner.com/slukjanov) or [email](http://feedburner.google.com/fb/a/mailverify?uri=slukjanov).

There are some examples of embedding code into the article below:

###Embedding gist:
{% gist 1245365 %}

###Embedding inline code with pygments:
``` js Discover the power of alert http://slukjanov.name blog
alert("test")
```

###Embedding code from a file:
{% include_code title test.js %}

###Embedding inline code with the codeblock tag:
{% codeblock title lang:js http://slukjanov.name blog %}
alert("test")
{% endcodeblock %}


