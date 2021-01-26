# R-Style-Guide -- Code RED 

Not about making your code "pretty"! Our goal here is to write code that
is **readable**, **extensible** and **debuggable** (RED).

# Why It's So Important

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

In other words, your goal should be 

**The RED Principle:**

* Readability.

* Extensibility.

* Debuggability.

Note that the latter will be especially important in this document.  You
will typically spend much more time debugging your code than in writing
it, and this will continue throughout the life of your code.  So, 
*anti-bugging* is key.

# What This Guide Does NOT Do

There are no suggestions here on object naming, number of spaces to
indent, placement of braces and so on.  Yes, meaningful names for variables
etc. are very important, as is indenting, but we have no recommendations
on the details.  We stick to more specific things that can really make a
difference in the quality of your code from an RED point of view.

# Motivating Example

Recently I was running some vital production code written by others.
The code runs input files and processes them in complex ways.

In many cases for this code, the input files have problems.  These can
occur in myriad ways, causing the software to choke.  In the case of one
particular input file, the code did choke, so I ran **debugger()**,
finding that the problem occurred on the line (I've changed things
slightly, changing some names and actually shortening it)

``` r
cn <- paste(g[which(!is.na(str_locate(clientLines,"^[]*cn")[,"start"]))[1]:(abs_-1L)],collapse="")
```

My major obstacle to solving the problem with the input file was
figuring out what this code does!  Mind you, the code itself was
correct; the input file was the problem, somehow.  But it was quite
difficult to work with here:

* Way too much nesting of uf function calls: 
**str_locate()** within
**is.na()** within
**"[()"** within
**paste()**.

* No code to check whether **cn** is NA (which it was in this instance).

* No preceding or following comments explaining what **cn** and
**cLines** 
  were supposed to contain.

# Writing Functions

## Nested function calls

Nesting should rarely if ever go beyond two levels. Something like

``` r
d <- f(g(h(x)))
```

should be broken down, saving intermediate results, e.g.

``` r
th <- h(x)
tg <- g(th)
d <- f(tg)
```

There are two advantages to this multi-stage, multiline approach:

* It's much easier to read, allowing one to "catch one's breath" at each
  stage and ponder what is happening.

* It allows adding comments at some of the key stages. 

* It is already set up for debugging, so you can easily set a
  breakpoint, say at the second stage, then step through
the code from there.

What about Magrittr pipes?

* You could do something similar with Magrittr pipes if you like using
them.  Make sure to put each stage of the pipe on a separate line, to
enhance the RED-ness of your code, especially for enabling comments at
the key stages.

* But that still would not address the debuggability issue.  One would nedd
to physically modify the code to set a breakpoint, which is distracting
and may break our train of thought.  That is the whole point of having a
debugging tool in the first place: the alternative, adding and deleting
**print()** statements, takes time and is distracting.

* I personally am not a fan of Magrittr pipes, as I just don't see the
need for them.  The same "left-to-right" (or top-to-bottom) thinking
that pipes are supposed to facilitate are easily attained with the "each
stage on a separate line" pattern displayed above.  And impact on
speed/memory is negligible.

## Size of functions (in lines)

In order to improve clarity of code, computer science students are
taught to code in a *top-down* or *modular* manner.  Roughly, the idea
is something like this:

``` r
f <- function(somearguments) 
{
   "glue" lines
   out1 <- g(some arguments)
   more "glue" lines
   out2 <- h(some arguments)
   more "glue" lines
   out3 <- i(some arguments)
   ...
}
```

In other words, the body of a function consists largely of calls to
other functions.  And those functions themselves will be of that form as
well.  

The advantage, which applies just as well to R users who are not
primarily programmers as it does to CS people, is that
in this manner, *we limit the length of a function*.  Just as a
supermarket may open a new checkstand whenever lines in the existing
ones exceed, say, three people, if the number of lines in a function you
write exceeds, say a screenful, you should shorten it by moving some of
the lines into separate functions.

Why do this?

* It's difficult to read/debug/enhance a long function.

* This style in essence presents an outline of what the function does
  (especially with good function names).

* When you are writing the code, you write this "outline" first, then fill
  in the details by writing the functions **g()**, **h()** and so on.

* This makes your code easier to *write* for *you*.

* For *others*, it makes your code easier to *read*.

* For both you and others, it makes your code easier to *debug*.


## Anonymous functions

Code like

``` r
g(x,y,function(t) t^2)
```

should be used very sparingly.  Once again, the problem is debugging.
Since the function has no name (technically, is not bound to a name),
one cannot instruct one's debugging tool to pause in that function.

## Use of global variables

Personally I have always thought the stern admonitions against global
variables are overblown.  Indeed, you may be surprised to see that some
of your favorite CRAN packages, e.g. **ggplot2**, do make use of some
globals.

Careful use of globals can make your code easier to write and debug.
You should, however, place your globals in an R **environment**, so that
they are clearly set apart from the others.

# Commenting Your Code

In our motivating example above, much of my debugging time would have
been unnecessary had the authors of the code included a comment stating
what the line is supposed to do, e.g. something as simple as

``` r
# find line number for client information
```

Let's discuss this further.

## Central role in code development

In any programming course for Computer Science students, this is
absolutely central.  If a student turns in a programming assignment with
few or no comments, it will get a [poor
grade](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-189-a-gentle-introduction-to-programming-using-python-january-iap-2011/lectures/MIT6_189IAP11_comment.pdf).
If comments are needed for clarity and readability for CS students, who
are presumably strong programmers, then R users who are not expert
programmers need comments even more.
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

## Comments spanning an entire file or a chunk of one

One tends to think of a comment as pertaining to the one or two lines of
code that follow it.  But one should also write comments with wider
scope.

* At the top of each source file, insert comments giving the reader an
overview of the contents.  This will typically be an overview of the roles
of each major function, how the functions interact with each other, what
the main data structures are, and so on.

I strongly recommend that you write these comments at the top of a file
BEFORE you start coding (and of course modifying it as you do write
code).  This will really help you focus during the coding process.

* Comments that prepare the reader for the following chunk of code, say
  6-12 lines, can also greatly enhance the RED-ness of your code, e.g.

``` r
# Our strategy will be to first create a matrix of index numbers of the
# k closest neighbors of each of the given points, then construct the
# corresponding graph.
```

Armed with this overview, the reader will be much better prepared to
tackle the code that follows.

## Can code be self-documenting?

To some degree, the number of comments can be reduced via use of
descriptive variable and function names, e.g.

``` r
OverAge65Rows <-getOlder(.....)
```

But comments can be much more descriptive, e.g.

``` r
# at this point, the data frame w will consist of the original rows for
# people over age 65 and who are renters; the data continue to be sorted
# by increasing ZIP Code
```

And as noted earlier, comments with scope spanning the entire file or
significant chunks of it can be quite helpful, something that mere choice
of object names can't achieve.

## Use of the roxygen Package

Use of this technique can save you time in writing the online help pages
for your package.  By writing some comments in **roxygen** format, you
simultaneously are writing the help pages.

But in my view, program comments need to be much more detailed that what
goes into help pages.  I recommend not using **roxygen**.

# Use of External Packages in General

One of the truly great things about the R language is CRAN and
Bioconductor, with packages for everything under the sun.  I use CRAN
packages a lot.

On the other hand, you should make sure an external package is really
necessary for your code.  Relying on a lot of packages can:

* Make your code hard to maintain.  Every time a package that you use
  changes, you run the risk that this breaks your code.  And remember,
  those packages may in turn depend on still more packages, and so on,
  further increasing your exposure.

* You never can be completely sure that those packages do exactly what
  you want in all circumstances.  Another package's edge case may be a
central use case for your package.  Subtle bugs can occur, hard to track
down.

In many, probably most cases, the advantages of relying on a package
will outweigh the above concerns.  But one should approach this very
carefully.

# Error Checking

Wherever your code, for instance, extracts a subset from a vector or
data frame, don't assume the result will be non-NULL!  You should have
code to check for this whenever you are not completely sure a non-NULL
result will occur.

Call **stop()** for serious errors, **warning()** for "iffy" cases.

It may be quite useful to use **tryCatch()** in many of these cases.
Instead of your code blowing up, it can give the user a chance to fix
her input error, or if the code does blow up, it may print out some
helpful information.

# Functional Programming 

Recently there has been a lot of interest in the R world in *functional
programming* (FP).  But of course, a desire to be fashionable should not
take priority over RED principles.

Typically FP will enable one to replace an entire loop with a single
line of code.  This can be beneficial to the RED-ness of your code.  For
instance, it can aid in making code "top-down" as described earlier
here.  I often use **apply()** and **lapply()** in my own code (and
sometimes **Map()**, **Reduce()** and **Filter()**.  I don't use
**purrr**, not having a need for it, but it is certainly a powerful
package.)

On the other hand, it must be kept in mind that **FP may increase the
complexity of your code**, which may run counter to our RED goals.

One should be especially aware of the possibility that an FP version of
your code may be difficult to debug (very un-RED!).  I like the comments
in [this blog post](https://www.weirdfishes.blog/blog/practical-purrr/#debugging-using-safely):

> One annoying thing about using map (or apply) in place of loops is
> that it can make debugging much harder to deal with. With a loop, it’s
> easy to see where exactly an error occurred and your loop failed (e.g.
> look at the index of the loop when the error occurred). With map, it
> can be much harder to figure out where the problem is, especially if
> you have a very large list that you’re mapping over.

The author then recommends the **safely()** function in the case of
**purrr** code.  For base-R functions such as **lapply()** one can use
**tryCatch()** as explained above.  But these approaches will only make
things somewhat easier, and in the end a loop may be easier to debug.

Note carefully that in the computer science world, FP is considered an
advanced, abstract concept.  An interesting discussion of the topic is
in [Charavarty and
Keller](https://www-ps.informatik.uni-kiel.de/~mh/reports/fdpe02/papers/paper15.ps.gz).
They believe FP in its standard form in introductory programming classes
is unsuitable even for CS majors.  R users, with generally less
sophistication, may find FP to be harder to code.

So, in many cases, using a loop rather than FP may be RED-der.  Don't
feel that you "must" avoid loops.  Again, if you browse through your
favorite CRAN packages, you'll see lots of loops.

# Classes

R is blessed with a number of different class structures, notably S3, S4
and R6.  My own view of R6 is that it is far too abstract for writing RED
code.  Simpler is often better!  I recommend sticking mainly to S3, or
if you prefer a bit more tightness, S4.

Also, resist the temptation to make a class out of everything.  Once
again, the principle should be whether the class would improve the
RED-ness of your code.  Adding structure means adding complexity, which
may result in weaker RED-ness.  My own code does often have S3 classes,
but it is not top-heavy with them.

# Shorter Code May Not Be Better Code

One often sees discussions on the Web in which people post various
solutions to a given problem, and in which the tacit goal is to make the
code as compact as possible.  This is very different from the RED goals,
and in my experience, is generally counter to it.

# Other R Style Guides

The above recommendations stem from my experience as a software developer and
teacher of programming.  But in the end, style is a matter of taste.
The reader should look at some of the others:

* [Hadley Wickham](https://style.tidyverse.org/); see also
[Google](https://google.github.io/styleguide/Rguide.html)

* [Jean Fan](https://jef.works/R-style-guide/)

* [more to be added as I encounter them]

## Debugging Tools

Make sure to use one!  R's internal browser is not bad, and RStudio, ESS
and StatET have visual ones.  Don't use **print()** lines to debug your
code!

