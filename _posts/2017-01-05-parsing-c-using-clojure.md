---
layout: post
title: "Parsing C Using Clojure"
---

# What I did on my winter vacation

**tl;dr** I wrote a good chunk of a C parser in Clojure:

> <https://github.com/daveho/catparty>

One of the perks of having an academic job is having a bit of free time at particular times of year.  Now is one of those times: the break between the fall and spring semesters.

One of the ideas I've had in my mind for quite a while is writing a C compiler that can run entirely within a web browser, *i.e.*, one that is implemented in JavaScript and which generates JavaScript.  I could have leveraged an existing codebase, such as [LearnCS](https://github.com/derrell/LearnCS) (neat project!) or a real C compiler (maybe [TCC](http://bellard.org/tcc/)?) compiled using [Enscripten](https://github.com/kripken/emscripten).  However, the C front-end component of LearnCS is not cleanly separated from its UI, and Enscripten is a pretty heavyweight piece of tooling.  Anyway, how hard can it be to write a C compiler?

Even though my target platform is JavaScript, I decided to use Clojure, knowing that [ClojureScript](https://github.com/clojure/clojurescript) is a fantastic way to compile Clojure to JavaScript.  I've been using Clojure in my [programming languages course](https://ycpcs.github.io/cs340-fall2016/) for quite a while now, and I've been wanting to work on a larger project in Clojure.  It's a really, really nice language, as I'll describe below.

## C is not easy to parse

If you've ever tried to declare a pointer to a function in C, then you understand that the C language is, well...weird.

The main source of weirdness is the syntax for *declarators*, which are the bits that correspond to variable and function names in declarations.  In the ancient mists of Unix history, Dennis Ritchie decided that these should look like the *uses* of the variable or function being declared, which is why the declarator for a pointer variable called `p` looks like `*p`.  (The `*` operator is the pointer dereference operator.)  This actually isn't so bad: the [C grammar](http://www.cs.dartmouth.edu/~mckeeman/cs48/references/c.html) has productions defining declarator syntax, and they're fairly reasonable to parse.  Except...

There are also "abstract" declarators, which can appear in type names and parameter type lists.  These are similar to normal ("concrete") declarators, except that there's no variable or function name.  Again, the grammar has productions for them, so that's well and good.  But here's the problem: there are contexts in the grammar where either an abstract or concrete declarator is allowed.  For example, here are the productions for *parameter-declaration*:

    parameter-declaration
        declaration-specifiers declarator
        declaration-specifiers
        declaration-specifiers abstract-declarator

So why is this a problem?

## I hate parser generators

The "traditional" way to implement a C parser is to use a parser generator such as [Bison](https://www.gnu.org/software/bison/).  The parsing algorithm most commonly used in parser generators is LALR(1), which is a "bottom up" algorithm.  Bottom up parsers can deal with difficulties like abstract vs. concrete declarator resolution fairly easily.  However, I am not a fan of parser generators.  My main objection to them is that you cede all control over parsing to the generated parser code, making error reporting and recovery quite complicated.  Also, it is all too easy too easy to write grammar rules that can't be handled by the parsing algorithm (e.g., the dreaded "reduce/reduce conflict" for LALR(1) parser generators.)  In any case, I'm not aware of any parser generators for Clojure.(1)

What I *do* like is parsing by recursive descent.  This is a "top down" approach where the parser starts with the grammar's start symbol, and then applies productions to transform the start symbol into the sequence of input tokens being parsed.  Recursive descent parsers are *predictive* because they can look ahead in the input token sequence to determine which production to apply next.  This tends to work well in practice, as long as the grammar is structured in a way that the correct production can be predicted using finite lookahead (1 or 2 tokens, typically).  For "reasonable" grammars, finding the correct heuristics to guide selection of productions tends to be fairly easy.

One really awesome advantage of recursive descent is that it is easy to embed specialized parsing algorithms in a recursive descent parser.  For example, parsing infix expressions can be awkward to do by recursive descent, because left associative operators require left recursive productions, which can't be directly implemented in a recursive descent parser.  There are [workarounds](https://ycpcs.github.io/cs340-fall2016/lectures/lecture05.html), but they greatly complicate the grammar.  However, there are specialized parsing algorithms for infix expressions, such as [precedence climbing](http://en.wikipedia.org/wiki/Operator-precedence_parser), which make parsing infix expressions incredibly easy.  So, when a recursive descent parser needs to parse an infix expression, it can just switch over to a more suitable algorithm (e.g., precedence climbing) for the extent of the infix expression, and then resume recursive descent.

Recursive descent and precedence climbing are powerful tools.  Surely, they can handle C...right?

The answer of course, is yes, they can.  The bad news is that C's "official" grammar has constructs, such as the parameter declaration rules shown above, where finite lookahead is insufficient to determine which production to apply.  So what do we do?

## Applying some moderate cleverness

I found two points in the C grammar where finite lookahead was insufficient to choose a production.

1. Choosing a init declarator or a function definition in the context of an external declaration
2. Distinguishing a direct concrete declarator from a direct abstract declarator in the context of a type name or parameter declaration

What I did in each case was to combine handling of the similar constructs into a single parse function.  So, I have a `parse-init-declarator` function that can parse an init declarator *or* a function definition, and I have a `parse-direct-declarator-base` function that can parse either a direct concrete declarator or direct abstract declarator (for just the part of each of those constructs which differ syntactically.)  A "parser context" data structure tells the parse function which alternatives are allowed: for example, whether an abstract declarator is allowed, or a concrete declarator, or both.  This is another nice feature of recursive descent: we can cheat by making the parser context-sensitive.

I won't describe the heuristics I used to handle these two cases, but you can see them in [the source code](https://github.com/daveho/catparty/blob/master/src/catparty/cparser.cljc) (look for the functions I just mentioned.)

There are some other tricks needed to parse C by recursive descent: for example, all of the list-generating syntax rules need to be right recursive rather than left recursive.  Integrating the precedence climbing parser was complicated slightly be the existence of the C conditional construct (`?:`), which is not a binary operator.  I ended up handling assignment operators and conditionals (the two lowest precedence levels) by recursive descent, and then switching over to precedence climbing for all higher-precedence binary operators.  Type casts and unary operators occur at the highest precedence levels (above all of the binary operators), so the precedence climbing parser switches back to recursive descent to handle these.

## What about backtracking?

I didn't mention one important technique for handling cases where finite lookahead is insufficient to choose a production: *backtracking*.  This is a pretty simple idea.  Basically, in cases where a unique production can't be predicted, the parser chooses a possible production arbitrarily.  If it works, great.  If the parse fails, then the parser resets itself to the state it was in before it tried the failed production, and simply tries another production.

Because Clojure is a functional language, without mutable data, it would be crazy easy to implement backtracking.  I actually did contemplate using it as a workaround for the init declarator vs. function definition problem.  However, recursive backtracking wastes time in cases where part of a parse has to be abandoned.  Depending on how many backtracking points the parser has, parsing time could become exponential.  By avoiding backtracking, I can guarantee that the parser will complete in O(*n*) time for input of size *n*.

## What's left to do

My C parser is still at a very early stage.  In particular, it doesn't handle

* typedef names
* preprocessor constructs

There are probably bugs, as well.  However, I was able to solve the problems I was most worried about, which was distinguishing the constructs described above.

## Why Clojure is awesome

I am a very, very late convert to functional programming.  This is the first substantial project I have worked on in a functional language.

I won't subject you to a lot of gushing about how great Clojure is (although it is great.)  Let me instead relate one anecdote that I think explains what I like about Clojure.

The output of my parser is, more or less, a *parse tree*.  The structure of this tree corresponds to the exact grammar productions used in the derivation of the input token sequence from the grammar's start symbol.  As such, it has lots of information that isn't really necessary.  For example, every semicolon in the input is dutifully represented as a node in the parse tree, even though semicolons have no significance semantically.  Also, recursive grammar rules for lists produce trees that are essentially linked lists.  It is nice to have a "flat" representation of such constructs; for example, if there is a sequence of statements, we want the node representing the overall sequence of statements to have all of the statements as direct children.

Later stages of the compiler, such as the semantic analyzer and code generator, will want to consume a more pared-down representation of the input program, containing only the semantic information needed to translate the program into the target language.  Traditionally, the compiler will construct an *abstract syntax tree* (AST) which is a distillation of the parse tree which eliminates unnecessary nodes and simplifies recursive structures by flattening them.  Producing an AST from a parse tree is not fundamentally hard, and I give it to students in my programming languages course as [an assignment](https://ycpcs.github.io/cs340-fall2016/assign/assign07.html).  However, given the complexity of a C parse tree and the variety of syntactic constructs that can be present, I wasn't really keen on writing a lot of ad-hoc code to translate the parse tree into an AST.  Being lazy, I thought about whether there was a more declarative way to produce an AST from a parse tree.  To this end, I added two features to the parser.

First, I added a "flatten" option to the function that continues a production recursively.  This causes the children of the parse node resulting from the recursive production to be copied to the parent node.  For all of the recursive list rules, this was sufficient to guarantee a flat structure.(2)

Second, I added a `filter-tree` function that provides a view of a tree in which arbitrary nodes are hidden.  This was so easy to do that I have to show you the code:

{% highlight clojure %}
;; Create a "filtered" view of a tree rooted at specified node.
;; Only the children selected by the specified predicate function will
;; be visible in the tree.
;;
;; Parameters:
;;   n - a Node
;;   pred - a Node predicate
;;
;; Returns:
;;   a view of the tree rooted at n in which nodes not matched by the
;;   predicate are not visible; note that n itself cannot be made invisible
;;
(defn filter-tree [n pred]
  (if (not (has-children? n))
    ; Do nothing 
    n
    ; Replace children with filtered sequence (just the children
    ; that match the predicate), in which each child is
    ; recursively filtered
    (let [children (map #(filter-tree % pred) (filter pred (:value n)))]
      (replace-children n children))))
{% endhighlight %}

Here is some code showing how "punctuation" symbols &mdash; things like parentheses and semicolons &mdash; are filtered out of a parse tree `t`:

{% highlight clojure %}
(def c-punct-tokens (set (map #(second %) cl/c-punct-patterns)))
(def c-punct-tokens-discard (disj c-punct-tokens :ellipsis))
(defn c-node-filter [n]
  (let [symbol (:symbol n)]
    (not (contains? c-punct-tokens-discard symbol))))

(def ft (node/filter-tree t c-node-filter))
{% endhighlight %}

As a result, `ft` is a view of the tree `t` with the punctuation nodes filtered out.

What I think the parse tree to AST transformation shows is that the power of functional programming is *composable abstractions*.  This is exemplified by the `filter-tree` function: the built-in `map` and `filter` functions do most of the work.  The only pieces I needed to worry about were which nodes to retain (specified by a predicate function) and how to construct the result (using my existing node functions.)  This was literally 5 minutes of work.

Ok, I said I wouldn't gush about Clojure, but what strikes me most about working in a functional language is that you are consistently rewarded for *thinking about the problem*.  The little voice in your head that tells you "I don't want to write a bunch of boring tedious code" becomes a signal to think about the problem a bit more deeply and find an elegant solution.  And an elegant solution, consistently, seems to be possible in Clojure.

## What next

What I have is a long way from being a full C parser, but I feel like I'm on the way.  Next will be dealing with typedef names, and probably implementing at least some form of preprocessor.

<hr style="margin-top: 8em;">
Notes:

(1) I am aware of [Instaparse](https://github.com/Engelberg/instaparse), which is nifty, but I wasn't convinced that I would be able to implement the symbol table hack for typedef names.  There is also [cljcc](https://cljcc.com/), which seems to be incomplete.

(2) If you think about this a bit, you'll realize that it's an O(*n*<sup>2</sup>) algorithm.  I have ideas of how to do this in O(*n*) by deferring the flattening until the recursive list is complete.

<!-- vim:set wrap: ­-->
<!-- vim:set linebreak: -->
<!-- vim:set nolist: -->
