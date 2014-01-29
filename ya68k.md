---
layout: page
title: "Ya68k"
description: ""
---

I am working on a small computer system based on the MC68008 CPU
called "ya68k".  (**Y**et **A**nother **68K**.)
At the moment, it's at a very early stage of development:
I've tested the CPU in free-run mode, but that's about it.
Currently, I'm working on adding an EPROM so that it can execute
actual code.

Eventually I will post schematics, firmware and GAL equations,
and (hopefully!) a board layout.

Here are the blog posts I've written about the project:

{% for tag in site.tags %} 
  {% if tag[0] == 'mc68008' %}
  <ul>
    {% assign pages_list = tag[1] %}  
    {% include JB/pages_list %}
  </ul>
  {% endif %}
{% endfor %}
