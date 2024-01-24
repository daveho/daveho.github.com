---
layout: post
title: "Pandoc for presentations"
---

TL;DR Pandoc is amazing, Pandoc filters are amazing, and you should
be using them to make presentation slides if making presentation
slides is a thing that you do.

# Making slides with Pandoc

Obviously millions of people have already discovered that [Pandoc](https://pandoc.org/)
is awesome for making slides for presentations. I've finally figured out what
is so great about it, which I document here. This post might be slightly helpful
for people who are trying to figure out how to implement Pandoc filters to
enhance their document workflow.

## Basics of making a presentation using Pandoc

The basic workflow for a presentation in Pandoc is

1. Write the presentation as [Markdown](https://daringfireball.net/projects/markdown/)
2. Run `pandoc` using `-t beamer` to translate the Markdown into a LaTeX
   [Beamer](https://ctan.org/pkg/beamer) presentation, and
   then produce a PDF of the presentation

The "limitation" of Pandoc is that it represents the document *structure*
(headings, paragraphs, inline elements, etc.) without representing the
*layout* of the document. Pandoc doesn't even have a standard way of
implementing simple layout techniques like centering elements.

Although the "out of the box" experience with Pandoc does have limitations,
it produces really nice presentations. If you just want something simple,
it's great.

## You might want *some* control over layout

Eventually, you will probably encounter a point where the "out of the box" experience
doesn't quite do what you need. Personally, I wanted the following improvements:

1. I wanted to use Beamer blocks for callouts in the presentation.
2. I wanted to control the text size in code listings.
3. I wanted variable substitution for things that I need to change periodically:
   for example, I wanted to write <code>&#123;&#123;semester&#125;&#125;</code>
   and have it mean `spring2024` for the Spring 2024 semester, `spring2025`
   for the Spring 2025 semester, etc.
4. I wanted to center images without captions.

What to do?

## Pandoc filters to the rescue!

Here's the awesome thing about Pandoc: it creates an abstract syntax tree
of the input document, and allows the execution of "filters" to transform
the AST. Hence, you can do things like

* Modify the structure of the AST
* Add attributes or classes to elements in the AST

in order to influence the generated output, which is simply a flattening
of the AST, with semantic actions to render each element
in the output format as appropriate.

Pandoc filters can also add "raw" block and inline elements for a particular
output format, which are essentially arbitrary text. (E.g., raw LaTeX
output could be arbitrary LaTeX commands.) This is useful for
generating translated output beyond what Pandoc itself will generate.
In theory this gives you nearly unlimited power to generate whatever kind
of output you need.

## Prebuilt filters

There are lots of great pandoc filters that people have already written!
Two of my requirements were met by existing filters.

*I wanted to use Beamer blocks for callouts in the presentation.*
The [pandoc-beamer-block](https://github.com/chdemko/pandoc-beamer-block)
filter allows this.

*I wanted to control the text size in code listings.*
The [pandoc-latex-fontsize](https://github.com/chdemko/pandoc-latex-fontsize)
filter allows this.

Both of these filters are implemented in Python and available using `pip`:

```
pip install pandoc-beamer-block
pip install pandoc-latex-fontsize
```

## Writing my own filters

My other two requirements weren't quite met by existing Pandoc filters
(at least the ones that I found.)

The [pandoc-mustache](https://github.com/michaelstepner/pandoc-mustache)
filter *nearly* worked for variable substitutions. However, it isn't
able to do variable substitutions in the URLs of links, which made it
unsuitable.

I implemented the following filter, which is quite similar in operation
to pandoc-mustache, but explicitly does substitutions in link URLs:

```python
"""
Panflute filter to do variable substitutions.
Goal is to allow placeholders for details that will vary
from semester to semester (e.g., the semester,
the webpage URL, etc.)
"""

import panflute as pf
import re

# Given a match of a substring that looks like a variable
# reference, return either the expansion of that variable
# (as looked up in the metadata), or the original string
# (if the variable doesn't seem to be defined.)
def expand(m, doc):
    varname = m.group(1)
    value = doc.get_metadata(varname, None)
    if value is not None:
        return value
    else:
        return m.group(0)

# Replace all variable references in given string with the value
# of the variable. References to nonexistent variables aren't expanded.
def subst_str(s, doc):
    repl = lambda m: expand(m, doc)
    x = re.sub(r"\{\{([A-Za-z_]+)\}\}", repl, s)
    return x

# Replace all variable references in given URL string with the value
# of the variable. References to nonexistent variables aren't expanded.
# This is slightly different than subst_str because the "mustache"
# delimiters will have gotten URL-encoded to
# "%7B" and "%7D"
def subst_url(s, doc):
    repl = lambda m: expand(m, doc)
    x = re.sub(r"%7B%7B([A-Za-z_]+)%7D%7D", repl, s)
    return x

def action(elem, doc):
    if isinstance(elem, pf.Str):
        elem.text = subst_str(elem.text, doc)
    elif isinstance(elem, pf.Link):
        elem.url = subst_url(elem.url, doc)
    else:
        return

def main(doc=None):
    return pf.run_filter(action, doc=doc)

if __name__ == '__main__':
    main()
```

This filter uses the excellent [Panflute](http://scorreia.com/software/panflute/)
framework (`pip install panflute`), which makes it pretty easy to
implement pandoc filters. (Again, a Pandoc filter is an AST transformation,
and Panflute gives you a very straightforward materialization of the Pandoc
AST to work with.)

I don't know if this code is good Python or not, but whatever, it works.

For centering images, the issue is that a Markdown image with a caption
gets generated by Pandoc as a Beamer figure with a caption. In a
presentation, most of the time I'm showing an image (photo, diagram,
etc.), I *don't* want a caption. However, Pandoc renders images without
captions as inline elements, meaning the image isn't centered.

The good news: Pandoc allows any element to have attributes and/or classes.
I implemented a filter to put any image with the "`center`" class in
a LaTeX `center` environment. For example, in the markdown

```markdown
![](figures/my-diagram.pdf){ width=4in .center }
```

the image "`figures/my-diagram.pdf`" will be rendered with a width of
4in (this is already handled by Pandoc itself) and because of the
`.center` designator and my filter, will be rendered in a `center`
environment.

Here's the filter (this one's really simple!):

```python
"""
Panflute filter to allow images to be centered in
LaTeX output if they have the ".center" class.
"""

import panflute as pf

def action(elem, doc):
    if isinstance(elem, pf.Image):
        if 'center' in elem.classes:
            return [pf.RawInline(text="\\begin{center}\n", format='latex'),
                    elem,
                    pf.RawInline(text="\\end{center}\n", format='latex')]

def main(doc=None):
    return pf.run_filter(action, doc=doc)

if __name__ == '__main__':
    main()
```

## Running Pandoc

Putting it all together, something along the lines of the following command
will generate `lecture01.pdf` from `lecture01.md`:

```
pandoc --filter pandoc-beamer-block \
  --filter pandoc-latex-fontsize \
  --filter filters/subst_var.py \
  --filter filters/center_img.py \
  --metadata subtitle:"601.418/618 Operating Systems" \
  --metadata semester:"spring2024" \
  --metadata date:"January 22, 2024" \
  -t beamer lecture01.md -o lecture01.pdf
```

Note that `filters/subst_var.py` and `filters/center_img.py` are the
Pandoc filters whose code is listed above.

The `--metadata` options allow definition of Pandoc "metadata" variables
on the command line. These in turn do things like set title page
attributes (the `subtitle` and `date` variables) and set values
of placeholders (the `semester` variable, which, like I said, is going to
change periodically, so I don't want to hard-code it.)

## Conclusions

Now that I understand Pandoc's AST and how to use Pandoc filters to
operate on it, I feel confident that the next time I need control over
how my presentation is rendered, I will be able to implement it easily.
The really awesome thing about this workflow is that the presentation
document itself can be mercifully free of obscure and verbose LaTeX commands,
and can focus on the presentation content itself.

You can see some examples of presentations I've made
[on this webpage](https://jhuopsys.github.io/spring2024/schedule.html).

Hope you find this useful!
