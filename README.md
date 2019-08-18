# R-Style-Guide
Not about making your code "pretty"! Our goal here is to make code that has 
fewer bugs and is easier to maintain and extend.

**WORK IN PROGRESS**

## Why It's So Important

*Your goal should be to avoid future grief,* both for yourself and for
others who might work on your code.

If you are writing code that you or others will be using in the future,
it is absolutely essential that write "good" code, meaning:

* It is easy to understand, both by others and *by yourself*, six months
  later.  This facilitates debugging, and adding new features.

* It is robust to errors, in that it does error checking and messaging.
  Especially important is avoiding "silent" errors, i.e. ones that do
not cause execution to cease but cause possibly major damage in the
output, undetected.

In other words, the goals should be:

* **Readability.**

* **Extensibility.**

* **Debug-ability.**

## What This Guide Does NOT Do

There are no suggestions here on object naming, number of spaces to
indent, placement of braces and so on.  Yes, good names for variables
etc. are very important, as is indenting, but we have no recommendations
on the details.  We stick to more specific things that can really make a
difference in the readability, maintanability and safety of your code.

## Motivating Example

Recently I was running some vital production code written by others.
The code runs input files and processes them in complex ways.

In many cases, the input file have problems.  These can occur in myriad
ways, causing the software to choke.  In the case of one particular
input file, the code did choke, so I ran **debugger()**, finding that
the problem occurred on the line (I've changed things slighly)

``` r
cn <- paste(g[which(!is.na(str_locate(clientLines,"^[]*cn")[,"start"]))[1]:(abs_-1L)],collapse="")
```

My major obstacle to solving the problem with the input file was
figuring out what this code does!  Mind you, the code itself was
correct.  But it was quite difficult to work with here:

* Way too much nesting of uf function calls: 
**str_locate()** within
**is.na()** within
**"[()"** within
**paste()**.

* No code to check whether **cn** is NA (which it was in this instance).

* No preceding or following comments explaining what **cn** and
**cLines** 
  were supposed to contain.

## Recommendations

### Nested function calls

Nesting should rarely if ever go beyond two levels something like

``` r
d <- f(g(h(x)))
```

should be broken down, saving intermediate results, e.g.

``` r
th <- h(x)
tg <- g(th)
d <- f(tg)
```

There are two advantages to this:

* Much easier to read, allowing one to "catch one's breath" at each
  stage and ponder what is happening.

* It allows adding comments at some of the key stages. 

* It is already set up for debugging, so you can easily add a
  breakpoint, say before the first stage, then step through
the code from there.

You could do something similar with Magrittr pipes if you like using
them.  Make sure to put each stage of the pipe on a separate line, both
for readability and especially for enabling comments at the key stages.

But that still would not address the debugability issue.  One would nedd
to physically modify the code to set a breakpoint, which is distracting
and may break our train of thought.  That is the whole point of having a
debugging tool in the first place: the alternative, adding and deleting
**print()** statements, takes time and is distracting.

I personally am not a fan of Magrittr pipes, as I just don't see the
need for them.  The same "left-to-right" (or top-to-bottom) thinking
that pipes are supposed to facilitate are easily attained with the "each
stage on a separate line" pattern displayed above.  And impact on
speed/memory is negligible.

## Comment Lines/Partial Lines

In our motivating example above, much of my debugging time would have
been unnecessary had the authors of the code included a comment stating
what the line is supposed to do, e.g. something as simple as

``` r
# find line number for client information
```

In any programming course for Computer Science students, this
is absolutely central.  If a student turns in a programming assignment
with few or no comments, it will get a failing grade.  If comments are
needed for clarity and readability for CS students, who are presumably
strong programmers, then R users who are not expert programmers need
comments even more.

A [style
guide](https://www.cs.utah.edu/~germain/PPS/Topics/commenting.html)
at a top university computer science department puts it well:

> Commenting involves placing Human Readable Descriptions inside of
> computer programs detailing what the Code is doing. Proper use of
> commenting can make code maintenance much easier, as well as helping
> make finding bugs faster. Further, commenting is very important when
> writing functions that other people will use. Remember, well
> documented code is as important as correctly working code.

(Also see specific tips on commenting, later in that document.)

*Don't be under the illusion that your code is self-documenting; it
  isn't!  A typical comment might look like this:*

``` r
w <- f(w)
# at this point, the data frame w will consist of the original rows for
# people over age 65 and who are homeowners
```

*At the top of each source file, insert comments giving the reader n
overview of the contents.*

This will typically an overview of the roles of each major function, how
the functions interact with each other, what the main data structures
are, and so on.

I strongly recommend that you write these comments at the top of a file
BEFORE you start coding (and of course modifying it as you do write
code).  This will really help you focus during the coding process.



## Other R Style Guides

The above recommendations stem from my experience as a software developer and
teacher of programming.  But in the end, style is a matter of taste.
The reader should look at some of the others:

* [Hadley Wickham](https://style.tidyverse.org/); see also
[Google](https://google.github.io/styleguide/Rguide.html)

* [Jean Fan](https://jef.works/R-style-guide/)

## Debugging Tools

Make sure to use one!  R's internal browser is not bad, and RStudio, ESS
and StatET have visual ones.  Don't use **print()** lines to debug your
code!

