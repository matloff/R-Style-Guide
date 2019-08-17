# R-Style-Guide
Not about making your code "pretty"! Our goal here is to make code that has 
fewer bugs and is easier to maintain and extend.

## Why It's So Important

If you are writing code that you or others will be using in the future,
it is absolutely essential that write "good" code, meaning:

* It is easy to understand, both by others and *by yourself*, six months
  later.  This facilitates debugging, and adding new features.

* It is robust to errors, in that it does error checking and messaging.
  Especially important is avoiding "silent" errors, i.e. ones that do
not cause execution to cease but cause possibly major damage in the
output, undetected.

## What This Guide Does NOT Do

There are no suggestions here on object naming, number of spaces to
indent, placement of braces and so on.  Yes, good names for variables
etc. are very important, as is indenting, but we have no recommendations
on the details.  We stick to more specific things that can really make a
difference in the readability, maintanability and safety of your code.

## Other R Style Guides

The above recommendations stem from my experience as a software developer and
teacher of programming.  But in the end, style is a matter of taste.
The reader should look at some of the others:

* [Hadley Wickham](https://style.tidyverse.org/); see also
[Google](https://google.github.io/styleguide/Rguide.html)

* [Jean Fan](https://jef.works/R-style-guide/)
