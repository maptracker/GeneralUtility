
* Unlike most programming languages, R does not really have (exposed)
  references. Variables are passed around as names, and are evaluated
  on-the-fly by
  [searching a complex environment](./Rnotes.md#scoping).
  * Loosely speaking, **you can not modify an existing
    object**. Rather, you can copy, or **make an altered copy of**, an
    existing object. But the copy loses the "environment", which is
    critical to maintaining object scope.
  * `data.table` *is an exception!* This package represents the table
    objects as references!
* You can get help on many (all?) operators by quoting them (since
  they are kind-of sort-of just names), eg `?"&"` or `?"<-"`
* R does not have scalars. Single values are
  [vectors of length 1](./Rnotes.md#vectors).
  * In addition to [formal null values](./Rnotes.md#specialvalues),
    you also have empty vectors (length 0).
* [Set and get](./Rnotes.md#setget) methods are generally written the
  same but are different underlying code.
* Be alert for [recycling](./Rnotes.md#recycling). This is where R
  needs additional data, so keeps looping through a "too short" input
  to make sure it has enough entries (like rectangularizing a
  matrix). You will be warned that recycling happens *only* if there
  is a modulus.
* Some [syntax](./Rnotes.md#syntax) is potentially confusing
  * The modulus operator is `%%`
  * Vecotrized logical operators are `&` and `|` (per
    [Brittany Parsons][Parsons1])
    * `&&` and `||` do someting ... different. Have not yet figured
      those out.
  * `:::` is not the same as `::`!
  * `<<-` is not the same as `<-`!
  * The `.` and `_` characters are semi-reserved. You *could* use them
    in varaible names if you wanted to, but there are
    [reasons not to](./Rnotes.md#s3)
  * You can `next` (no parenthesis) but you'll want to `return()`.
* R will work to coerce modes for you to "make things work." This
  occurs transparently and may take you places you don't want to be.
* [NA](./Rnotes.md#NA) carries with it a specific mode, and can be any
  of the other modes.
* If you mistype the name of an argument in a
  [... function](./Rnotes.md#dotdotdot), the argument will be clumped
  into the generic pool of arguments specified by ... (and of course
  your intended named argument won't get set).
* There is a [bewildering array](./Rnotes.md#import) of low-level
  import / export functions.
* [read.table()](./Rnotes.md#import) will by default factorize
  character data. Use `stringsAsFactors = F` to prevent, or specify
  `colClasses` explicitly.
* There is no `strict` mode in R. It will work tierelessly to help
  you, even if it kills you in the process. Code defensively.
* [class() and data.class()](./Rnotes.md#dataclassweird) return
  different modes for integer vectors ("integer" and "numeric",
  respectively).
* Argument parsing in [functions](./Rnotes.md#functions) is weird.
  * R can use partial matching in many places, including argument
    names. This is dangerous, aspire to use full argument names.
* [Symbol scoping](./Rnotes.md#scoping) is kind of weird.
  * Scope is associated with **functions**, not the call stack.
  * Functions with the same name in different namespaces will
    [mask](./Rnotes.md#masking) each other. Use `conflicts()` to get a
    list of currently masked functions and `find()` to determine the
    packages they're in.
* Don warns that R sometimes unexpectedly vectorizes output. If the
  output had a column header, then in the process of forcing a vector
  R will coerce all your values to character to accomodate the
  header.
* `sprintf()` can use vectors as bind arguments, producing a character
  vector as output. It will [recycle](./Rnotes.md#recycling) if
  needed, but only if all vector arguments are of the same modulus.
* R makes a brave attempt at
  [Object Oriented Programming](./Rnotes.md#oop). It is a powerful
  feature but different from OOP in other languages.
* GitHub Markdown is very touchy. Putting code blocks in lists can
  cause disruption of all following markdown syntax if the
  [rules](./Rnotes.md#markdown) are not carefully followed.
* R preserves your workspace, which is nice but potentially
  confusing. Using `rm(list=ls())` will clear all variables in the
  current environment.
* There's a weird list variant called a
  [pairlist](./Rnotes.md#pairlists). It only seems to crop up with
  certain functions, like `formals()`.
* If a file contains values that are explicitly quoted, `read.table()`
  (and variants) will *only* allow them to be read as character
  mode. You need to
  [coerce other modes AFTER reading](./Rnotes.md#quotedCoercion)
* `read.table()` is convienent, but for large data files
  [scan() is MUCH faster](./Rnotes.md#import)
* Be careful using "auto generated" names, such as column headers (eg
  "V1" in `read.csv`). These might change as R evolves (eg
  `reshape2::melt()` changed from [X1 to Var1][MeltColChange]).
* ggplot evaluates expressions lexically when they are needed. If you
  have provided a "data" object, they will be evaluated there. For a
  NULL data object, evaluation is in the current local context.
* Don points out that ggplot2 uses plyr. If you load dplyr *then*
  ggplot2, the plyr functions will be higher on the search stack and
  will get used whenever you think you're calling dplyr.
* `table()` will normally exclude `NA` counts. Use `table(x, exclude =
  NULL)` if you wish to have NAs explicitly tallied. ( @sdchasalow #1 )
* `sample(x)` will return a randomized vector of the elements from x
  **UNLESS** x has length 1. Then it will return `sample(1:x[1])`.
  That is, if `x <- c(12)`, then `sample(x)` will
  return a randomized vector of `1:12`. ( @sdchasalow #1 )
* `1:length(x)` will behave inappropritately if length(x) == 0. In
  that case you will get `c(1,0)` rather than an empty vector. For
  safety, use instead `seq_len(length(x))`. ( @sdchasalow #1 )
* In ggplot, using `aes()` with object names (eg `aes( x = Foo )`)
  will automatically transform the aesthetic if the primary data are
  transformed (eg `ggplot( mydata[order(mydata$Bar), ], aes( x = Foo)
  )`).  However, if you eschew names and pass the raw data instead
  (`aes( x = mydata$Foo)`) the relationship between the aesthetic and
  the data will be lost, and transformations on the data will not
  propagate to the aesthetic. ( @rossmacp #1 )


Arg. [GitHub hyperlinking][GFM] being weird...

#1

@sdchasalow

[Parsons1]: https://class.coursera.org/rprog-033/forum/thread?thread_id=224#post-1472
[MeltColChange]: https://github.com/hadley/reshape/blob/master/README.md
[GFM]: https://guides.github.com/features/mastering-markdown/#GitHub-flavored-markdown
