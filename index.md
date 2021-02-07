---
layout: page
title: "Dave's Blog"
tagline: "Random Thoughts and Such"
---

# About

Welcome to my blog!

I teach computer science at [Johns Hopkins University](https://www.jhu.edu/).
In this blog I post about programming, education, music, electronics, and other random
topics.

All opinions expressed here are entirely my own!

# Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

# Links

[Old blog, no longer updated](http://fullofleaves.blogspot.com/)

[My home page](https://www.cs.jhu.edu/~daveho/)
