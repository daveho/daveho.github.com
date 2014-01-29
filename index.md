---
layout: page
title: "Dave's Blog"
tagline: "Random Thoughts and Such"
---

# About

Welcome to my blog!

I teach computer science at [York College of Pennsylvania](http://www.ycp.edu/).
In this blog I post about programming, education, music, electronics, and other random
topics.

I'm currently working on [CloudCoder](http://cloudcoder.org),
an [open source](http://github.com/daveho/CloudCoder) web-based programming exercise system.

# Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

# Links

[Old blog, no longer updated](http://fullofleaves.blogspot.com/)

[My home page](http://faculty.ycp.edu/~dhovemey)
