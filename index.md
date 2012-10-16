---
layout: page
title: Dave's Blog
tagline: Random Thoughts and Such
---
{% include JB/setup %}

# About

<img style="float: right; width: 150px;" src="img/selfPortrait.jpg" alt="Self portrait" />

This is my blog.

I teach computer science at [York College of Pennsylvania](http://www.ycp.edu).
I post about programming, education, music, electronics, and other random
topics.

I am co-developer of the [FindBugs](http://findbugs.sourceforge.net) static
analysis tool.  I'm currently working on [CloudCoder](http://cloudcoder.org),
an [open source](http://github.com/daveho/CloudCoder) web-based programming exercise system.

There is an [Atom feed](/atom.xml).

# Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
