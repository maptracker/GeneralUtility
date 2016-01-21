* [Fundamentals](#fundamentals)
  * [Syntax](#syntax)
    * [Non-obvious Operators](#oddop)
  * [References and Helpful Tools](#reftool)
* [Objects](#objects)
  * [Object Utility Methods](#objutil)
  * [Atomic Classes](#atomic)
  * [Date and Time](#datetime)
    * [lubridate](#lubridate)
  * [Vectors, Matrices and Data Frames](#vecmat)
    * [Subsetting](#subsetting)
    * [Vectors](#vectors)
      * [Vectorization](#vectorization)
    * [Lists](#lists)
    * [Matrices](#matrices)
    * [Recycling](#recycling)
    * [Arrays](#arrays)
    * [Factors](#factors)
    * [Data Frames](#dataframe)
  * [Formulae](#formula)
* [Data Import and Export](#importexport)
  * [General File Manipulation](#filesystem)
  * [Data Import](#import)
  * [Data Export](#export)
  * [Connections](#connections)
  * [Databases](#databases)
    * [ODBC](#rodbc)
    * [PostgreSQL](#postgres)
    * [MySQL](#mysql)
    * [SQLite](#sqlite)
* [Data Transformation](#transformation)
  * [Sorting](#sorting)
  * [String Manipulation](#strings)
    * [Regular Expressions](#regexp)
    * [stringr](#stringr)
  * [dplyr](#dplyr)
  * [reshape](#reshape)
    * [melt](#melt)
    * [cast](#cast)
* [Control Structures](#control)
  * [if, else](#ifelse)
  * [for loops](#forloop)
  * [while loops](#while)
  * [repeat, next, break, return](#nextbreak)
  * [Loop Functions](#loopfunc)
    * [apply](#apply)
    * [lapply](#lapply)
    * [sapply](#sapply)
    * [vapply](#vapply)
    * [mapply](#mapply)
    * [apply](#apply)
    * [tapply](#tapply)
      * [split](#split)
    * [plyr](#plyr)
* [Graphics](#graphics)
  * [Devices](#devices)
  * [Core Graphics](#coregraphics)
  * [Lattice Graphics](#lattice)
  * [ggplot2](#ggplot2)
  * [D3 Networks](#networkD3)
  * [Image Packages](#images)
* [Functions](#functions)
  * [Symbol Scoping](#scoping)
* [Packages](#packages)
* [Object Oriented Programming](#oop)
  * [S3 OOP](#s3)
    * [Importance of Classes in S3](#s3classes)
  * [S4 OOP](#s4)
    * [setClass](#s4setclass)
    * [new](#s4new)
    * [Rle](#s4rle)
* [Developing in R](#rdeveloping)
  * [Errors and Error Handling](#errors)
  * [Debugging](#debugging)
  * [Profiling](#profiling)
  * [Programmer-like Objects](#dataobject)
* [Text Handling](#texthandling)
* [Probability](#probability)
  * [Distributions](#distributions)
  * [Random Number Generator](#rng)
* [Clustering](#clustering)
  * [Hierarchical Clustering](#HierarchicalClustering)
  * [K-Means Clustering](#kmeans)
* [Random Things](#randomstuff)
  * [Generating Examples](#makeexamp)
  * [Internal R Stuff](#rinternals)
  * [Lexical Scoping](#lexicalscoping)
  * [Serialization](#serialization)
  * [Scott Wisdom](#wisdom)
  * [Weird Things](#weird)
    * [Aliases, Aliases, Aliases](#aliases)
    * [data.class() vs class()](#dataclassweird)
  * [Markdown Notes](#markdown)

# <a name='fundamentals'></a> Fundamentals #

* `x <- y` : The arrow operator is **always** a copy function. That
  is, it does NOT simply create a new reference/pointer to the
  right-hand argument. In the above example, x and y will be "the
  same", but will be two independent objects (manipulating x will not
  change y, even if y is a data frame or other 'complex' object).
  * The `identical(x,y)` function can be used to do a deep
    identity test on two objects.
  * `all.equals(a,b,c,...)` is similar to identical, but it can take
    multiple arguments and can specify a `tolerance` to allow for
    minor mathematical differences.
* `=` can be used as an alias for `<-`
  * Scott avoids this, because it can cause confusion when dealing
    with parameter assignment, which also uses equals, for example
    with the 'nrow' parameter in `diag(1:2, nrow = 4)`
* R is case-sensitive: b != B
* <a name='setget'></a>Left methods and right methods may have the
  same name, but are generally different functions.
  * Example: `dim()`
    * On the right(eg `myDims <- dim(myMatrix)`), will **get**
      the dimensionality of the query
    * On the left (eg `dim(myMatrix) <- c(2, 3)`) will **set** dimensions
    * These are **two different functions** in R
      * The right function is named "as is", that is just "dim"
      * The "real" name of the left function is `dim<-` (that is,
        with the `<-` arrow tacked on), which R calls a "replacement
        method". Scott calls this "dim gets"
      * `?dim` will show you help for *both* methods
  * Not all functions follow this paradigm; For example, finding the
    locale of the current system is broken into the separate functions
    `Sys.getlocale()` and `Sys.setlocale()`

### <a name='syntax'></a>Syntax Considerations ###

* `.` (period) has - *sometimes* - special meaning. Sometimes it does
  not. If you wish, you can use it as an arbitrary character in a
  variable name. This is probably not a good idea, though, as it can
  cause confusion with [S3 OOP classes](#s3).
* `_` (underscore) has no special meaning in R, and can be used in
  variable names. *However*, it has (had?) special meaning in S, where
  it served as an alias for `<-`. This means that use of underscores
  can make your code incompatible in S. Probably best to avoid usage.
  * The emacs ESS major mode [ESS][ESS] will irritatingly convert `_`
    to ` <- ` automatically. Probably can override somewhere.
* `<<-` is a special assignment vector that will bypass normal
  [scope](#scoping) to assign variables in the
  function-environment-hierarchy
* `:::` is used to access [internally scoped](#internalscope) variables
* Backticks allow recovery of function objects that would otherwise
  insist on be function-y eg `` `if` ``
* There are a few "naked" operators, like `next` or `break`, but
  nearly all other functions should include parentheses: Using
  `return` will return the *function* return; You probably wanted to
  `return()`.
* Curly brackets define an "expression", eg `{ op1; op2; op3}`. The
  last operation is what will be returned.

#### <a name='oddop'></a>Non-obvious Operators ####

* `%%` Modulus
* `%/%` Integer division (no decimal / remainder)
* `%*%` Matrix multiplication
* `&` / `|` = Vectorized logical AND / OR. `c(T,F,T) & c(F,F,T)` =
  `[1] FALSE FALSE TRUE`. That is, it cycles over the elements and
  performs the requested boolean logic.
  * `&&` / `||` = I still have not quite figured out what this
    does. They're boolean operations that cascade in some way.

### <a name='reftool'></a>References and Helpful Tools ###

* [Advanced R][AdvancedR] = Hadley Wickham's R site
* [The R Inferno][Rinferno] : A fairly deep look at some of the issues
  in R
* `library(codetools)` / `?codetools` : debugging utilities
  * `checkUsage( myFunction )` : provides some feedback on potential
    issues, like undeclared variables.
* [The Elements of Statistical Learning][ElStatLearn] = Available free
  online, heavy statistical reference

# <a name='objects'></a>Objects #

* [Hadley Wickham : Data structures][WickhamDataStructures] - nice
  overview shared by Scott.
* Objects have an initial value that is used when an object is needed
  but has not been explicitly provided. For example, if you create a
  structure that is specified as having integers, but you don't
  provide the actual values, then the default value is used until such
  time as you provide your own.
* Names
  * Most R objects can have names
    * If you try to name something that is un-name-able, you'll get
      `target of assignment expands to non-language object`
  * `names(someObject) <- c("First name", "another name", "name 3")`
    * If you supply insufficient names, `NA` is assigned to the
      remaining parts of the object
    * Assigning too many names yields `'names' attribute [#] must be
      the same length as the vector [#]`, and the assignment fails
      (nothing changes).
  * `dimnames(myMatrix) <- list(c("alpha", "beta"), c("hot", "cold"))`
    * Assigns row and column names, respectively, to a matrix
  * In some circumstances R allows "partial matching", where you need
    not provide the entire name, only enough of the first letters to
    unambiguously distinguish the name from other names in the object.
    * This is potentially dangerous and should probably not be used in
      practice.
* Attributes
  * `attr()` can get and set values
  * names & dimnames, dimensions (matrices, arrays), class, length, etc
  * user-defined
  * Setting attributes on a vector will cause it to be
    [reported as "atomic" in str()][atomicstr].

### <a name='objutil'></a>Object Utility Methods ###

* `str()` = "Structure"
  * Provides a verbose report of what the object is and what
    value(s) it contains.
  * Includes a dozen parameters controlling output
* `summary()`
  * Context-specific, brief information on an object
  * For lists / vectors reports min, max, mean, median and quartiles
* `head()` = report first n (default 10) lines of a list / vector /
  file / etc
  * `tail()` = reports last n lines
* `typeof()` = "storage mode" (basic class)
  * For a vector, reports the mode of the contents (ie not "vector")
* `class()` = like typeof, but reports object classes (ie, inherited
  classes in an Objected Oriented paradigm).
  * class can also be a left-had argument used to *set* the class of
    an object. This can be used to assign a class
    * The R documents disadvise doing this, and recommend `as()`
      to coerce a class instead.
    * `inherits()` tests if a particular object belongs to a particular class
      * `inherits(x, what = 'vector')`
  * `data.class()` = seems similar-but-different to class. It
    [behaves weirdly](#dataclassweird).
* `summary()` = brief overview of object
  * Reports "NA" content
* `quantile( myNumericVector, na.rm = FALSE)` = Report numeric values
  within your vector residing at specific probabilities.
  * `probs` = Specifies probability bins, default is `seq(0, 1, 0.25)`
  * `cut2(myNumericVector, g = 4)` = cut the vector into 4 quantile
    groups, represented as a factor, with names capturing the quantile
    boundaries.
    * This function is in `library(Hmisc)`
* `table( factors1, factors2, ... )` = Report summary counts for
  factors or factorizable data.
  * `as.table()` = will try to coerce the input to a table
  * `is.table()` = will test input to see if it is a contingency table
  * `use.NA = "ifany"` = Always useful to add, will include a count of
    NA values (otherwise will not!)
* `is.SOMETHING()` = class / type test method
  * eg `is.numeric()`
  * Returns a boolean indicating if the object "is" that thing
* `as.SOMETHING()` = coerce / cast an object from one mode to another
  * eg `as.integer()`

### <a name='atomic'></a>Atomic Classes ###

* Character
  * default value `""` (empty string)
  * `is.character()` / `as.character()`
* Numeric
  * The aliases "double" and "real" can be interchanged anywhere
    "numeric" is used. `typeof(1.3)` will return "double".
  * default value `0`
  * `is.numeric()` / `as.numeric()`
  * Double-precision reals
  * `Inf` = infinity, `-Inf` = negative infinity
    * `is.finite()` / `is.infinite()`
    * R defines `1 / 0` as Inf - why isn't that UNDEF, since it's
      either Inf or -Inf ??
  * With some effort can [increase magnitude of floats][BigNumbersInR]:
    * `library(gmp)` = "Arithmetic Without Limits"
    * `library(Brobdingnag)` = represent numbers in log form
  * `options(digits=16)` = set the number of digits R displays
    ([StackOverflow][SObignumbers])
* Integer
  * default value `0`
  * `is.integer()` / `as.integer()`
  * If defining explicitly, suffix with `L` to avoid being cast as
    numeric, eg `172L`
* Complex
  * default value `0+0i`
  * `is.complex()` / `as.complex()`
* Logical (Boolean)
  * default value `FALSE`
* <a name='specialvalues'></a>Special values
  * <a name='NA'></a>`NA` = "Not Available"
    * `is.na()` = boolean check
    * Apparently returned for "not possible", eg `as.integer("pineapple")`
    * Is the default value for some objects, such as matrices without
      any values.
    * NA can exist in many (any?) of the other modes. That is, there
      is an integer mode for NA, a character mode, etc. The default
      mode appears to be logical.
      * These can be assigned directly with `as`, for example `as.character(NA)`
      * More commonly the mode will be assigned "automatically" to
        make the mode fit the surrounding context. In the example
        below, the "NA" is mode numeric, since that is the mode of the
        source vector:
        
        ```R
        > x <- c( one = 1, two = 2, four = 4)
        > x[c("one", "three")]
         one <NA> 
           1   NA 
        > mode(x[c("one", "three")][[2]])
        [1] "numeric"
        ```
    * `NaN` = Not a Number
      * `is.nan()`
      * More specific form of NA
      * Used for missing values, plus "not numbers".
  * `NULL` = undefined
    * `is.null()`
      * Throws an error with no argument. Seems like that should be "TRUE"
    * Weird behavior. Throws an error when passed to is.na(), but
      spits out a bizarre value of `logical(0)` with is.nan(). May
      be a sign of a latent malignant AI - must watch carefully.
    * Asking Scott about NULL seems to make him uncomfortable, like
      telling a parent that their child is eating paste.

### <a name='datetime'></a>Date and Time ###

* `Date` class
  * Uses POSIX standard relative to 1 Jan 1970, resolved in days for
    dates and seconds for times.
    * Internally the date is an integer (?) value with zero at the
      start of 1970
      * *Times can capture sub-second resolution, not sure how that's
        handled with an integer representation.*
    * **POSIXct** = internal format that represents dates and times
      numerically.
    * **POSIXlt** = internal format representing dates and times as
      structures, such that sub-parts (eg month, seconds) can be
      accessed by name.
      * If you see the error `$ operator is invalid for atomic
        vectors` then your object may be in ct format and should be
        transformed to lt.
    * `as.POSIXct()` and `as.POSIXlt()` can be used to convert back
      and forth.
* `Time` class
* Built-in utility methods:
  * `as.Date( dateString )` = Make a date object. The default `format` is
    `"%Y-%m-%d"`, but can be set as something else.
  * `strptime( dateString)` = Format date output, similar to sprintf.
  * `Sys.Date()` = Current date for the system's locale.
  * `Sys.time()` = System's time in POSIXct format
    * `date()` = Current time as a character string, fixed format "%a
      %b %d %H:%M:%S %Y"
  * `Sys.getlocale()` / `Sys.setlocale()` = get or set
  * `weekdays( dateObject )` = return the name of the day (eg "Sunday")
  * `months( dateObject )` = return month name
  * `quarters( dateObject )` = Quarter name (eg "Q3")
  * `julian( dateObject, origin = dateObject)` = get the difference in
    days between a date and an origin (also a date).
    * Default origin is Jan 1, 1970
  * The generic `format()` method can be used to format output from
    Date objects.
* Date math
  * As long as the objects are the same classes, mathematical
    operations can be done.
  * Generates `difftime` time range objects.
  * Can provide specific time zones to each object in the comparison

```R
dt <- as.Date("1970-02-01")
```

#### <a name='lubridate'></a>lubridate ####

Helper package for manipulating dates. **LOTS** of functions.

* `library(lubridate)`
  * [Tutorial on R-stats blog][lubridatetutorial]
  * `vignette("lubridate")`
  * First install with `install.packages("lubridate")`
* `ymd()`, `mdy_hm()`, `dmy_hms()`, etc = Parsers for Year-Month-Day,
  Month-Day-Year+Hour-Minute, Day-Month-Year+Hour-Minute-Second, etc.
  * *Many* permutations pre-packaged
  * Will look at non-numeric separators to extract components
  * Vectorized, input needs to be in same order but not with same separators
* `guess_formats( myDateStrings, orders = myOrders )` = Guess formats
  used for specific date strings.
  * `orders` = Appears to be detailed guidance for MonDay vs DayMon?
* Period Class
  * [S4 object](#s4) extending Timespan, tracks the difference between
    two objects
  * The period is generally undefined until anchored to a specific
    reference; This is because "a year" has differing amount of time
    (for example, days in leap years) depending on when it is set.
  * Periods can be constructed "piecemeal" using functions like
    `days()`, `years()`, `microseconds()` etc:
    * `x <- as.POSIXct("2009-08-03") + day(12) + hours(3) + seconds(45)`

## <a name='vecmat'></a>Vectors, Matrices and Data Frames ##

* "Lists of stuff"

#### <a name='subsetting'></a>Subsetting ####

* Selecting part of a list
* `[` = returns an object of the same class, can select multiple elements. Simple example with a vector:

  ```R
v <- c(12,6,7,NA,19,5)
v[3] # Get the third element
v[c(3,5)] # Get third and fifth element
v[2:4] # Elements 2,3,4
v[v < 15] # All elements less than 15
b <- v < 15 # b is a logical vector same length as v, reporting entries < 15
v[b] # Will perform the same selection as two lines up
# NA entries can be irritating; they return NA in booleans, too
v[which(b)]  # Use which() to avoid them
  ```
  
  * Single brackets are needed for accessing multiple elements from a list
* ``[[`` = Access a specific element
  * Only one element at a time!
* `$` = Access by name. Simple example with list:

  ```R
  myList <- list( dog = c(17,32,1), cat = c(7,5,2) )
  myList$dog      # A numeric vector representing the 'dog' column
  myList[[1]]     # Same as above, a vector from the dog column
  myList[["dog"]] # Same as above, accessing via name
  
  myList[1]       # a *list* of just the dog column. Includes the name
  myList["dog"]   # Same as above, accessing via name
  colName <- "dog"
  myList[colName] # Same as myList["dog"]
  
  myList[[1]][[2]] # Get single element in first row, second column
  myList[[c(1,2)]] # Same as above
  ```
  
* Matrix subsets that are 1 dimensional by default will return
  vectors. If you want such results to remain as matrices, use `drop
  = FALSE`. Examples:
  
  ```R
  x <- matrix( 11:22, nrow = 3, dimnames = list
    ( c("Alice","Bob","Chris"),
      c("Alpha","Beta","Gamma","Delta") ) )
  x[[10]]        # Get the 10th element
  x[ 2, ]        # Get the second row
  x[ "Bob" ]     # second row by name
  x[ , 3]        # Third column
  x[ , "Gamma" ] # Third column by name
  x[ 2,1 ]       # Element in second row, first column *as a vector*
  x[ 2,1, drop = FALSE ] # As above, but now returns a 1x1 matrix
  x[ c(2,3),c(2,4) ]     # Slice of rows 2+3 / cols 2+4
  # Matrices do not use '$' for named accession:
  x[ c("Bob","Chris"), c("Beta","Delta") ] # Same slice as above
  ```

* Missing values can be found with `is.na()`, and exclued with `!is.na()`.
  * `complete.cases()` can be used to exclude any row with a missing
    value in any column.

  ```R
  > x <- data.frame( color = c("blue", "red", NA, "yellow"),
                     weight = c(13.2, NA, NA, 19.9))
  > !is.na(x$color)
  [1]  TRUE  TRUE FALSE  TRUE
  > ccx <- complete.cases(x)
  > x[ ccx, ]
     color weight
  1   blue   13.2
  4 yellow   19.9
  ```

* When using [S4 objects](#s4), the "slots" are accessed with the `@`
  operator.

#### <a name='vectors'></a>Vectors ####

* Most basic object
  * Scott says instead of scalars R uses vectors-of-length-one. That
    is, if you need to represent a single number, it's not a scalar
    object, but rather a vector with one entry. Presumably that's
    why when you evaluate just `4.3` R will reply `[1] 4.3`; the
    "[1]" is a reminder that R views this as a vector with one
    element, which is the double 4.3.
* Must contain homogeneous (same class) entries
* Creation
  * `vector()`
    * `x <- vector("numeric", length = 10)`
    * Initialized with default values.
  * `c()` = "concatenate"
    * `x <- c( 1L, 2L, 3L )`
    * **CAUTION:** c() will type-cast your input to force it into
      homogeneity, if required. For example:
    * `x <- c( 1L, 2L, 3L, 5, "7" )` (integer, numeric, character)
      * `str(x)` &rarr; `chr [1:5] "1" "2" "3" "5" "7"` (all characters!)
* Sequences
  * Iterative vectors, defined easily with ":"
  * Shorthand `1:20` = 1, 2, 3 ... 19, 20
  * Scott says to use `seq()` as a safer version:
    * `1:length(x)`: If x is zero length, you probably wanted an
      empty vector, but instead you'll get a vector of length 2: `c(1,0)`
    * `seq(from = 1, to = length(x), length = length(x))` will
      provide what you need (`integer(0)`, an integer vector with no
      members).
    * `seq(from, to, by = step)` = create a sequence from "from" to
      "to", incrementing by "step" (may not reach "to" if there is a
      modulus)
    * `seq(from, to, length = len )` = "from".."to", with exactly
      "len" equally spaced members.
    * `seq(along = x)` = a sequence starting at 1, incremented by 1
      with a length equal to the length of x.

##### <a name='vectorization'></a>Vectorization #####

* Many R operations occur in vector context - the arguments are
  vectors, and are operated on in an implicit loop on all vector
  members.
* Be very aware of [recycling](#recycling), which will occur if you
  perform operations with two vectors of different length. You will
  only be warned of this if the shorter vector can not be "evenly"
  recycled to match the longer one.

```R
> v <- c(4, 10, 25)
> v + 3
[1]  7 13 28
> v %% 2
[1] 0 0 1
> v < 14
[1]  TRUE  TRUE FALSE
> w <- c(2,3,2)
> v * w
[1]  8 30 50
> x <- c(2,4,6,8,10)
> v - x  # recycling is unhappy
[1]  2  6 19 -4  0
Warning message:
In v - x : longer object length is not a multiple of shorter object length
> y <- c(2)
> v - y  # recycling is content!!
[1]  2  8 23
```

#### <a name='lists'></a>Lists ####

* A list of things, optionally keyed to names.
  * The "things" are formally referred to as "elements". When I
    mentioned that I think of them as "columns" I got a disapproving
    look from Scott.
* Utilities
  * `is.list( obj )` = Logical check
  * `typeof( genericList )` &rarr; `list`
* Lists can be turned into arrays by assigning `dim()`. Scott says
  this is generally useless, but occasionally very useful;
  Conversion to an array lets members be accessed by row and column
  indices/names.
* <a name='pairlists'></a>A **dotted pair list** is a weird variant that
  shows up in some output. It apparently has something to do with
  Lisp, but I have not found anything beyond cryptic
  references. AFAICT you can use these lists just like other lists.
  * Found while playing with the `formals()` function.
  * `typeof( myWeirdDottedPairList )` &rarr; `"pairlist"`
  * `is.list( myWeirdDottedPairList )` &rarr; `TRUE`
  * `is.pairlist( myWeirdDottedPairList )` &rarr; `TRUE`
  * `is.pairlist( myNormalList )` &rarr; `FALSE`
  * `is.pairlist(NULL)` &rarr; `TRUE`
  * `is.list(NULL)` &rarr; `FALSE`

#### <a name='matrices'></a>Matrices ####

* Two dimensional array
* default components `NA`
* `is.matrix()` / `as.matrix()`
  * as.matrix will make a single column array
  * `is.array()` will evaluate TRUE
* `matrix( nrow = 2, ncol = 3 )` = empty
  * Attribute `dimnames` can be used to name the rows an columns
* `matrix(1:6, nrow = 2, ncol = 3 )` = populated with the sequence
  * Construction is "column-wise", meaning the matrix columns are
    filled first into column 1, then column 2, etc
  * While maintaining dimensionality, the contents of the matrix are
    effectively a linear vector wrapped into those dimensions.
    * However, `is.vector` will evaluate FALSE.
* `dim()` = return dimensions of matrix
  * `dim` can also be **assigned**:
    * `dim(myMatrix) <- c(2,5)`
  * Doing so will "linearize the contents of the matrix, and then
    re-construct them (column-by-column) into the specified dimensions.
* `cbind( col1, col2, ... )` / `rbind( row1, row2, ...)` = pass
  explicit vectors that you wish to write into columns or rows of a new matrix
  * **CAUTION:** If your provided vectors are not the same length, R
    will "pad" them out by *repeating the input vector* until the full
    dimensionality is filled. See the [Recycling section](#recycling).
* `diag()` = weirdly polymorphic matrix function:

  ```R
  myDiag <- diag( myMatrix ) # Extract the diagonal
  mySquare <- diag( 5 ) # Creates a 5x5 matrix
  myMostlyEmpty <- diag(1:10) # Creates a 10x10 matrix with only the diagonal populated
  diag( myMatrix ) <- value # Change the diagonal
  x <- diag(5,4)  # Creates a 4x4 empty matrix with a diagonal of fives.
  ```

#### <a name='recycling'></a>Recycling ####

* Some structures have an implicit size (for example, a 4x4 matrix has
  a length of 16). In at least some cases, these structures can be
  provided with content that has a lesser size. R can behave in
  different ways in different circumstances.
* In some cases, the "extra" values will be populated with the
  initial/default values for the mode.
* In other cases, the input will be **recycled**. That is, R will
  keep looping through the input to fill out the result. For
  example:
  
  ```R
  > rbind(16:18,5:9)
       [,1] [,2] [,3] [,4] [,5]
  [1,]   16   17   18   16   17
  [2,]    5    6    7    8    9
  Warning message:
  In rbind(16:18, 5:9) :
    number of columns of result is not a multiple of vector length (arg 1)
  ```
  
    * In the case above, the matrix has five columns, but the first
      row has only three values (`c(16,17,18)`) provided. R responds
      by "recycling" the row over and over until the matrix is
      padded out to the full five columns.
    * In this case, R can pad out the row by recycling the input
      twice, but it has "one left over" (`5 %% 3 == 2`). This upsets
      it, and while it completes the recycling, it complains `number
      of columns of result is not a multiple of vector length`
    * If R was able to "cleanly" recycle the input, **you will be
      given no warning**:
      
      ```R
      > rbind(7:9, 20:28)
           [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9]
      [1,]    7    8    9    7    8    9    7    8    9
      [2,]   20   21   22   23   24   25   26   27   28
            ```
* In some cases, recycling will refuse to operate if the modulus is not met:
  ```R
  > sprintf("%s %d %d", "test", 1:3, 10:11)
  Error in sprintf("%s %d %d", "test", 1:3, 10:11) : 
    arguments cannot be recycled to the same length
  ```

#### <a name='arrays'></a>Arrays ####

* Under construction

#### <a name='factors'></a>Factors ####

* Factors are "labeled integer vectors"
* Useful for categorical data, like "Male" / "Female" or "10 ug" /
  "20 ug" / "50 ug"
* Functionally will be utilized as vectors, but the labels provide
  human interpretation (so you don't forget what 4 represents).
* Used in modeling methods like `lm()` and `glm()`
  
  ```R
  > x <- factor(c("peach", "pear", "marmoset", "peach", "peach"))
  > typeof(x)
  [1] "integer"
  > str(x)
   Factor w/ 3 levels "marmoset","peach",..: 2 3 1 2 2
  ```
  
  * Note that internally while your order is maintained the integer
    assignment is made independently by R; peach is 2, even though it
    was "first".
  * `levels(x)` = reports the factor names as ordered by integer value
  * `as.numeric(x)` = explicitly extract the integer representation of
    a factor.
  * If you want to set the factor order explicitly, the `levels`
    argument can be used:
    
    ```R
    x <- factor(c("peach", "pear", "marmoset", "peach", "peach"),
                 levels = c("peach", "pear", "marmoset"))
    ```
    
* The order of levels is important in some modeling because the
    first level is taken as baseline.
* `table()` = simple contingency table of the factor
  * By default missing values will be excluded in the counts! set
    `exclude = NULL` to count them as well. Scott says some
    statistic shops have altered the table function to have this as
    the default.
* `gl()` = "Generate Levels", make a vector of factors based on some
  pattern:
  
  ```R
  gl(n = 3, k = 4, labels = c("alpha", "beta", "gamma"))
   [1] alpha alpha alpha alpha beta  beta  beta  beta  gamma gamma gamma gamma
  Levels: alpha beta gamma
  ```

#### <a name='dataframe'></a>Data Frames ####

* Special form of list
  * Each element is effectively a column
  * Like a matrix, is rectangular: All columns must have same length
    (same number of rows)
  * Like a list, but unlike a matrix, a data frame can be
    heterogeneous; Each column can be a different data type
  * Popularly abbreviated **DF**.
* Every row is named (attribute `row.names`)
  * These names are preserved when subsetting.

#### Data Frame Operations #####

* `read.table()` [see below](#import)
* `data.frame( col1, col2, ... )` = direct generation
* `myDF$myCol <- myVector` = Assigns myVector to the column myCol in
  DF myDF
  * `myDF <- cbind( myDF, myCol = myVector )` = Same as above
* `rbind(DF1, DF2)` = Appends rows, columns must be identical
* `data.matrix()` = convert to matrix
* `newDF <- transform(myDF, ColA = Something, ColB = someAction(ColX), ... )`
  * Similar to `with()`, allows context-constrained operations on a
    DF. Will return a **new** data frame (ie, will not alter the
    input) with new or modified columns based on the provided
    arguments.

##### <a name='datatable'></a>Data Tables #####

* Not part of core R:
  * `install.packages("data.table")`
  * `library(data.table)`
  * [Has its own website][datatable], plus [code on GitHub][datatablegithub]
  * [Comparison to data.frame][dfVSdt]
  * [Good intro to using data.table][advmanip]
  * Is supposed to be much faster than data.frame, particularly for
    subsetting, group and updating.
  * **OBJECTS ARE UTILIZED AS REFERENCES!!**
    * `yourDT <- myDT` = yourDT and myDT *are the same object*
    * Use `yourDT <- copy(myDT)` to get a "distinct" object
* `myDT <- data.table()` = Create a new data table
* `tables()` = Shows current tables held in memory
* Can efficiently add new columns with `:=`
  * `myDT[, w := z^2]` = Adds a column "w" as the square of z.
  * `myDT[,b:= mean(x+w),by=a]` = [plyr-like](#plyr) operation that
    sets column "b" to be equal to the mean of x+w, where x+w are
    aggregated "by" column a as a factor.
  * `myDT[, .N, by=x]` = uses special variable `.N` which represents the
    count of the expression
* Subsetting
  * Subsets get complex enough that data.table simply *discards row names*. 
  * Row subsetting is effectively the same as a DF
  * Column subsetting is quite different
    * Can pass a list of functions
    * Still confused here...
* `setKey(myDT, z)` = specifies that column "z" holds the "keys"
  associated with the data.table.
  * `myDT[ "rootbeer" ]` = Will subset the table with all rows where
    the designated key column is equal to "rootbeer".
  * `merge( myTab1, myTab2 )` = Will perform a join on the two tables,
    merging rows where their key columns match.
* `fread( myFile )` = Fast read of a data.table.
  * In addition to being faster than `read.table()`, the defaults look
    to be different (such as `stringsAsFactors` being false for DT).

# <a name='formula'></a>Formulae #

* Can construct with `formula()`, or by directly using `~` notation
  * Can also call `as.formula()` on string representations
* Will have `class` "formula, `typeof` "language" and `mode` "call".
* Left argument is optional. Not sure what it means if it is missing.
* Has three subsets:
  * `[[1]]` = The "~" operator.
  * `[[2]]` = The left argument, if it was provided. Otherwise the
    right argument.
    * Ewwww. Seems accident prone.
  * `[[3]]` = The right argument if a left argument was
    provided. Otherwise throws an error.
* Include their own [environment](#scoping).
* Operators
  * `~` = Basic "equals" operator, eg `y ~ a + b`
  * `+` = Separates terms in the model
  * `:` = Indicates interaction between terms in the model
  * `*` = Inidcates crossing between terms
    * = `a*b` = `a + b + a:b`
  * `^` = Indicates crossing to the indicated order
    * `(a+b+c)^3` = `(a+b+c)*(a+b+c)*(a+b+c)` =
      `a + b + c + a:b + b:c + a:c + a:b:c`
  * `-` = Excludes terms from the model
    * Useful to remove expansion from `:` or `^`.
    * Also pointed out that `- 1` will remove the intercept term in a
      linear model, specifying that the model crosses at zero.
  * `.` = Usually means "all columns not otherwise in the formula"
    * When used in `update.formula`, means "what was previously in
      this part of the formula"
  * `I()` = Used to escape notation that would otherwise be
    interpreted as one of the operators above. `I(a + b)` is equal to
    "a mathematically-added-to b" as opposed to "term a with term b"
* `plot()` has a function dedicated to formulae input
  * I am finding it somewhat perplexing.
* There is the infuriatingly named `Formula` package
  but is distinct from `formula`
  * `vignette("Formula")`
  * `|` = Special operator only used by "Formula"; Separates multiple
    parts of the formula

# <a name='importexport'></a>Data Import and Export #

* [CRAN guide to Data Import/Export][CranImportExport]
* *I remain very confused over the diversity of import / export methods*
  * Some of the functions appear to be almost identical (`serialize`
    vs `saveRDS`) and it's unclear what the nuances are.
* [Advanced Data Manipulation][advmanip] - Tips for working
  efficiently with large data sets.

### <a name='filesystem'></a>General File Manipulation ###

* General Considerations and Quirks
  * There are directory-specific commands below, but many of the
    "file" commands will work with directories as well. For example,
    file.exists() will return a true value if passed either an
    existing file or existing directory. Use dir.exists() if you need
    to explicitly determine if the path is a directory.
  * Platform variation
    * Help indicates that you *must not* include a trailing backslash if
      passing a directory path on Windows.
    * Case sensistivity varies by platform. On insensitive platforms,
      some functions may map filenames to upper or lower case.
  * In most cases R appears to be aware that `~` represents the user's
    home directory.
* File manipulation
  * `list.files( myDir )` = Returns a character vector of the file
    paths in the specified directory
    * Defaults to the current working directory
    * `pattern = myRegExp` = Specifies an optional regular expression
      against which files must be matched.
    * `full.names` = Default FALSE, if TRUE will return absolute
      paths.
    * `recursive` = Default FALSE, when TRUE the listing recursively
      drills into directories.
    * `include.dirs` = Default FALSE, which will exclude directories
      from the returned value.
  * `file.exists( myFileList )` = Returns a logical vector if the file
    exists. Note that for links the result is if the target exists.
  * `file.remove( myFileList )` = Deletes the passed file paths. Can
    remove empty directories. Returns logical "success vector"
  * `unlink( myFileList, recursive = FALSE, force = FALSE )`
    * Deletes the requested files / directories.
    * Returns logical "success vector"
    * Allows `*` and `?` wildcards, as supported by host filesystem.
    * If `recursive` is TRUE then encountered directories will be
      fully deleted, if possible. If FALSE then even
      explicitly-specified empty directories will not be removed.
  * `file.create( myFileList )` = Creates the files if they do not
    exist. Returns logical "success vector"
    * **WILL TRUNCATE EXISTING FILES**
  * `file.rename( from, to )` = Change a file's name. Returns logical
    "success vector"
  * `file.info( myFileList )` = Returns a data frame of information
    related to the provided paths:
    * `size` = Size in bytes
    * `isdir` = Logical flag indicating if the path is a directory
    * `mode` = Permissions octal flags
    * `mtime` = Modification time
    * `ctime` = Creation time
    * `atime` = Access time
    * `uid` = User ID
    * `gid` = Group ID
    * `uname` = User name
    * `gname` = Group name
  * `file.mtime()`, `file.size()`, `file.mode()` = Wrappers around
    file.info that return just a single column.
  * `file.show( myFileList )` = Paginated file viewer, like `more`,
    showing the file(s) requested.
  * `file.append( targetFiles, sourceFiles )` = Appends the second
    argument to the first. Not clear to me how multiple/multiple
    requests are handled.
  * `file.copy( from, to )` = Make copies of "from" in "to"
    * `to` can be a list of files, or a single directory.
      * `recursive = FALSE` When copying to a single directory, the
        recursive flag indicates if "from" directories should be
        deep-copied.
    * `overwrite` = If false, will prevent existing files from being
      clobbered. The default is "recursive", unclear what that is.
    * `copy.mode = TRUE` = Specifies if "to" should inherit mode
      (permissions) from "from"
    * `copy.date = FALSE` = Specifies if "to" should inherit date from "from"
* Directory manipulation
  * `getwd()` = get the current working directory
  * `setwd( myPath )` = set working directory. Allows absolute and relative
    paths
    * Lecture says escaped "\\" must be used for windows hierarchy?
      That seems poorly designed. Help indicates that getwd will
      consistently use "/", even on Windows.
  * `dir.exists( myPath )` = Check that a path exists AND is a
    directory. Vectorized, will return a logical vector.
  * `dir.create( myPath, recursive = FALSE, mode = "0777")` = Creates
    the specified directory with requested mode
    * A TRUE `recursive` flag will also create any parent directories
      as needed.
    * Will throw a warning if the directory already exists
* Permissions
  * Use a special integer mode of class `octmode`
  * `Sys.chmod( myPaths, mode = "0777")` = Sets the permissions on one
    or more files.
  * `Sys.umask( optionalNewValue )` = Sets and returns the current umask
  * `file.mode( )` = wrapper around file.info; Returns the octal mode of the
* Links
  * Platform dependent! Generally if a link is passed to one of the
    above functions, the operations will be on the target, not the
    link itself.
  * `file.symlink( from, to )` = Create a symbolic link to a
    file. Returns logical "success vector"
  * `file.link( from, to )` = Create a hard link to a file. Returns
    logical "success vector"

### <a name='import'></a>Data Import ###

* `read.table()` = flexible file import, lots of parameters:
  * `file` = path to file, or a connection
  * `header` = flag indicating a header row is present
  * `spe` = column separator
  * `colClasses` = character vector specifying column classes
    * Always a good idea to specify this for large files, since
      otherwise an initial scan is required for R to analyze the file.
  * `nrows` = number of rows to read in
    * Presumably this would be useful in testing to read in a subset
    * Lecture implies that providing the actual value (or something a
      bit larger) improves read performance?
    * `skip` = number of leading rows to skip over (useful for
      interrupted loads))
  * `comment.char` = character that defines a comment row
    * Default is "#"
    * Set to `""` if you are reading a large file with no comments
  * `stringsAsFactors` = if true (**DEFAULT!**), factorize strings
    * `as.is` = utility flag that is the opposite of stringsAsFactors,
      forces import of data as they are in the file.
  * `na.strings` = character vector of strings to be treated as `NA`
  * `strip.white` = if true, the leading and trailing white space
    around character information will be automatically removed.
    * `blank.lines.skip` = skip rows that are entirely empty
  * `read.csv()` = alias for read.table, but with different defaults
    for CSV files. Use `read.csv2()` for CSV formats from countries
    where ',' is used as a decimal point.
  * <a name='quotedCoercion'></a>**Caution** - If a "cell" in a file is
    quoted, R will consider it character data *no matter what you've
    specified with colClasses*. If you try to set the mode with
    colClasses to something other than "character" (numeric, logical,
    etc), R will halt with an error. You need to read such columns as
    character, [then explicitly cast them][ReadCsvForcedChar], for
    example with as.numeric().
* `readLines(fileName, numLines)` = read arbitrary lines from a text file
  * returns a character vector
* `load()` = reads in binary R object saved to a file
  * **PREFERRED METHOD**
  * Inverse of `save`
  * Can reconstitute an entire R workspace
* `source()` = reads ASCII-serialized R objects
  * Inverse of `dump`
* `dget()` = reads ASCII-serialized R objects
  * Inverse of `dput`
* `unserialize()` = reconsitute an R object via a [connection](#connections)
  * Inverse of `serialize`. Can also use `loadRDS`
* `loadRDS` = Similar to `unserialize`. Preferred?
* `scan()` = less convenient that read.table, but **much** faster; Useful
  for very large files.
* <a name='downloadfile'></a>`download.file(url, destination)` =
  Download a file over the network and save it to a local destination.
  * `method` = Specifies the method used (internal, libcurl, wget, curl)
    * `extra` = Additional options passed to the method
    * HTTPS sites may not work with some methods (`curl` will work)
  * `quiet` = Set to TRUE to suppress status and progress
  * `cacheOK` = Default TRUE, which allows server-side cached values
    to be used.
  * Proxies are handled with environment variables http_proxy /
    ftp_proxy (or upper case variants).
  * `options( internet.info = 2 )` = Default verbosity. Setting to 0
    or 1 providesmore information.
  * [url()](#connections) provides a finer-grained connection
    mechanism to access a networked file.
* `library(XML)` - [Using XML in R][xmlInR]
  * `myDoc <- xmlTreeParse( fileOrUrl )` = parses the tree into a structure
    * `htmlTreeParse()` = version of xmlTreeParse with defaults
      altered for HTML data.
  * `xmlSApply( xmlNode, <xmlType> )`. `<xmlType>` is one of the naked
    identifiers:
    * `xmlValue` = The text content of the nodes
    * `xmlAttrs` = Attributes assigned to the nodes
    * `xmlSize` = Number of child nodes
  * `xpathSApply( xmlNode, "<nodeSelector>", <xmlType> )` = Like
    above, but `<nodeSelector>` is a quoted xpath node selection
    syntax
* `library(jsonlite)` - [jsonlite tutorial][jsonlite]
  * `jsonData <- fromJSON( myFileOrUrl )`
    * jsonData will be a nested data.frame representing the
      hierarchical JSON object.
* <a name='complexformats'></a>More complex formats
  * `install.packages("XLConnect")`
    * Looks to be a very rich library to read and write Excel workbooks.
    * `xlcDump()` = Dumps R objects as worksheets in a workbook
    * `xlcRestore()` = Reads such a workbook back into R
  * `library(xlsx)` = Allows import and export of Excel spreadsheets
    * `read.xlsx( file, sheetIndex = mySheetNum, header = TRUE)`
    * `colIndex` and `rowIndex` are vector arguments specifying
      specific columns and rows to read.

### <a name='export'></a>Data Export ###

* `save()` = full export of an R object to a file
  * **PREFERRED METHOD**
  * Inverse of `load` 
  * Default format is binary, but argument `ascii` can be used to make
    an ascii file
  * `save.image()` will save the entire workspace
* `dput(obj, file = "/file/path/myObj.R")` = serializes an R object to
  ASCII text
  * Inverse of `dget`
  * Not as complete as `save`; will not export object name
  * Essentially generates R code that will reconstitute the object.
    * Somewhat human-readable, but the generated code is generally
      much more verbose than the code you used to generate the object
      in the first place. Presumably the code generation function
      works as a reverse-compiler.
* `dump(c("obj1", "obj2"), file = 'myDump.R')` = serialize one or more
  R objects to ASCII text
  * Inverse of `source`
  * Takes a list of *names* as input (not objects)
    * When reconstituted with `source` it will invoke the objects under
      their original names.
    * You can set the first argument with `ls( pattern = "myRegExp")`
      to select objects by a complex regular expression
  * May not generate an exact copy
* `serialize` = Serialize a single R object
  * Inverse of `unserialize`. The output can also be read by `readRDS`
    * Docs say `saveRDS` is "more convenient"
  * Default is binary format, but can be set to ASCII
  * Documentation warns that the format may change in future releases of R
* `saveRDS()` = Serialize a single R object
  * Inverse of `loadRDS`. Resultant files can also be read by `unserialize`?
* Lectures point out that text-based formats are more friendly with
  version control systems for finer-granularity change tracking
* Also see the [Serialization](#serialization) section below
* Note also the [more complex formats](#complexformats) mentioned
  above in the Import section, many of those packages also export
* `library(jsonlite)`
  * `jsonText <- toJson( rObject )` = will serialize an R object to
    JSON. Lots of configuration arguments.

### <a name='connections'></a>Connections ###

* Includes files, but also file compression/decompression, URLs, pipes
* `file()` = general utility read/write
* `url()` = general utility URL access, supports http, https, ftp and file.
  * `method = "libcurl"` exposes more schemes, depending on platform
  * file methods are local machine only and need to be absolute paths
  * `URLencode` can be used to escape parameters in the
    URL. `URLdecode()` might be useful for unescaping.
  * [download.file()](#downloadfile) is a simpler mechanism 
* Compression algorithms: `gzfile()`, `bzfile()`, `xzfile()`, `unz()`
  * zip files may be read with unz, but not created
* `open( myCon ... )` = used to create a handle after specifying one
  of the connections above.
  * `close()` and `flush()` can operate on an open connection
  * `isOpen()` and `isIncomplete()` utility functions
* Connections have an `open` argument specifying the read/write state
  * `r`, `w`, `a` = ASCII read, write, append
  * `rb`, `wb`, `ab` = Binary read, write, append
  * `r+` = read and write, `w+` = truncate, then read + write, `a+` =
    read and append. Not sure where these modes would be used; Maybe
    if memory is limited?
* `file( description = "clipboard", open = "r" )` = special mode to
  read the X11 clipboard. Setting description to "X11_secondary" reads
  the alternate clipboard.
* Session limit of 125 user connections

### <a name='databases'></a>Databases ###

* Some packages will mask one another; For example, `dbApply()` in
  both RPostgreSQL and RMySQL. Be careful if using two or more
  database architectures.

#### dbConnect ####

* Higher-level supporting package
* Comes with MySQL by default?
  * Specific database driver support below
* The number of simultaneous connections appears to be 10 (per
  driver?)

```R
# Change for the DB architecture you use:
library(RPostgreSQL)

# Establish a connection object
# The first argument is the driver, match to the DB architecture
con <- dbConnect(PostgreSQL(), user="username", db="mydb", port="5433",
                    host="example.com")

# Build and execute a SQL statement:
sql <- "SELECT * FROM tags WHERE tag LIKE 'ran%'"
# GetQuery will execute the SQL and return the results
ranTags <- dbGetQuery(con, sql)
# The returned object is a DF with matched column headers
ranTags
        tag       name url_id     ldap
1    random     Random      4 tilfordc
2 randomtag random tag      3 tilfordc

# SendQuery will get a results object:
rs <- dbSendQuery(con, "SELECT * FROM tags")
# Fetch then recovers a set of rows as a DF from the query:
dbFetch(rs, n = 1)
      tag    name url_id     ldap
1 testing testing      1 tilfordc
# ClearResult closes the statement
dbClearResult(rs)

# column headers will honor AS remappings:
sql <- "SELECT tag, name AS NiceName FROM tags WHERE tag = 'bioperl'"
colMap <- dbGetQuery(con, sql)
colMap
      tag nicename
1 bioperl  BioPerl

# If you wanted to the entire table could be read:
fullTable <- dbReadTable(con, "tags")

# Close the connection
dbDisconnect(con)
```

* t looks like *some* [drivers support placeholders][SqlPlaceholders]
  using `dbGetPreparedQuery()`, but apparently not all

Utility methods:

```R
# For convienence in calls below:
myDriverObj  <- PostgreSQL() 
myDriverName <- "PostgreSQL"
# con is presumed to be a connection to your database
dbListTables(con) # Character vector of all tables in schema
dbListFields(con, "myTable") # List columns in a table
dbListConnections( myDriverObj ) # List of currently open connections
dbListResults(con) # List of pending results
dbExistsTable(con, "tableName" ) # Logical test for table
dbIsValid(con) # Logical check if the connection is still ok
dbQuoteString(con, "A'B\"C\\") # Escapes SQL characters
dbGetInfo(resultObject) # Information about a live result
dbDataType(con, "hello world") # Get the database value type for the R object
                               # eg TRUE -> "bool", 1.3 -> "float8"
# Not all drivers are fully compliant. This method will show the functions
# that may be missing or non-compliant:
dbiCheckCompliance( myDriverName )
```

##### <a name='postgres'></a>PostgreSQL #####

* Setup: `install.packages("RPostgreSQL")`
  * On Ubuntu/Debian needs: `sudo apt-get install libpq-dev`
* Usage: `library(RPostgreSQL)`

##### <a name='mysql'></a>MySQL #####

* Setup : `install.packages("dbConnect")`
  * On Ubuntu/Debian [needs][mysqlDebian]: `sudo apt-get install libmysqlclient-dev`
* Usage : `library(dbConnect)`

##### <a name='sqlite'></a>SQLite #####

[SQLite][SqliteWP] is a very cool light-weight database. It is
efficient, fast, and generally fully SQL-compliant.

* Setup : `install.packages("RSQLite")`
* Usage : `library(RSQLite)`
* [dplyr](#dplyr) includes its own support for SQLite:

  ```R
  library("dplyr")
  library("nycflights13")
  my_db <- src_sqlite("mySQLiteFile.sqlite3", create = T)
  copy_to(my_db, flights, temporary = FALSE, indexes = list(
          c("year", "month", "day"), "carrier", "tailnum"))
  ```

  * `src_sqlite(path, createFlag)` = Connect to a SQLite database
    file, creating it if needed (and allowed)
  * `copy_to(dest, myDataFrame)` = when `dest` is a SQLite object,
    will copy the provided data.frame into the DB

##### <a name='rodbc'></a>Generalized ODBC #####

[ODBC][OdbcWP] is a generalized interface to databases

* Setup: `install.packages("RODBC")`
  * On Ubuntu/Debian [needs][rodbcDebian]: `sudo apt-get install libiodbc2-dev`
* Usage: `library(RODBC)`
* Documentation: `RShowDoc("RODBC", package="RODBC")`
* Your database needs to be configured to use ODBC

# <a name='transformation'></a>Data Transformation #

* Helper package for working with [data frames](#dataframe)
* Very fast
* For all functions:
  * The first argument is a data frame
    * To be useful the input should be properly formatted / annotated
  * Result will always be a new data frame
  * Columns can be refered to "raw", as just the name (does not need $)

### <a name='sorting'></a>Sorting ###

* `sort( myVector, decreasing = FALSE, na.last = NA )` = sorts the
  input values, returning a *vector of values*
  * Makes use of `order()` under the hood
  * Custom sort functions can be written for objects
  * `decreasing` will sort from high-to-low when true
  * `na.last` controls how NA values behave
  * `partial` optional vector of indices to just sort part of input
* `order(vec1, vec2, ... )` = sorts on one or more vectors, returning
  a *vector of indices*
  * Shares arguments with `sort()`
  * Does not appear to support descending on some inputs and ascending
    on others.
* `arrange( myDF )` = [plyr](#plyr) function

### <a name='strings'></a>String Manipulation ###

* `tolower( myCharVec )` = Lowercase
* `strsplit( myCharVec, split = myRegExp)` = Split strings based on
  one or more regular expressions.
  * `fixed` = Default "FALSE", if TURE then `split` will be
    interpreted literally rather than as a regexp.
  * `perl` = Default "FALSE", if TRUE then use Perl RegExp rules.
* `nchar(myText)` = Count number of characters
* `paste( Obj1, Obj2, ..., sep = ",", collapse = ". ")` = Concatenates
  objects (stringifying them if needed). Vectorized operation.
  * `sep` = Separator to use between objects
  * `collapse` = Default NULL (do not collapse). If non-NULL then the
    provided string will be used to collapse all final results into a
    single string.
  * `paste0()` is a convienence function with `sep = ""` (empty string)
* `cat(Obj1, Obj2, ..., sep = mySep, file = myFile )` = Concatenate
  objects to a file
  * `file` = A [connection](#connections) or a file name. If set to
    `""` then will be sent to the standard output. If `|someCommand`
    then will be piped to someCommand.
  * `sep` = Separating string between objects
  * `fill` = default FALSE. If TRUE, then newlines will be added
  * `append` = If TRUE, and `file` is a file path, then append the
    output rather than clobbering the file.
* `sprintf(myFormat, Obj1, Obj2, ...)` = Full access to C's sprintf
* `formatC( myObject, <lots of options>)` = Fine-grained formatting
  control of a single vector
  * `digits` = Number of digits after decimal point
  * `width` = Total field width
  * `format` = Specifies type of object
    * `d` = integers
    * `f` = 'xxx.xxx', `fg` = same, but interprets `digits` as number
      of *significant* digits.
    * `e` = 'n.ddde+nn', `E` = 'n.dddE+nn', `g`/`G` = same as e/E, but
      will only use scientific notation if it saves space.
    * `s` = strings
  * `zero.print` = Optional character for zeros, handy if you want to
    hide them in a sparse matrix.
  * `drop0trailing` = Removes trailing zeros and "e+00"
  * `...` = Additional arguments are passed on to format (below)
  * `prettyNum(myObject, ...)` = specifically for numbers
* `format(myObject, <lots of options>)` = More formatting! High-level
  function used for pretty printing in R
  * [Subclassing](#s3classes) of format() can be used to customize
    pretty printing of objects.
  * `trim` = default FALSE, if TRUE will remove padding spaces
  * `nsmall` = "minimum number of digits to the right of the decimal point"
  * `justify` = One of left/right/centre/none, used for character vectors
* `ngettext(n = numThings, msg1 = fmtOne, msg2 = fmtNotOne)` = Cute
  function that automatically applies plural values to a string
  `gettext(4, "You see %d mouse.", "You see %d mice.")`
  * `gettext()` = Still not really sure what this one does.

#### <a name='regexp'></a>Regular Expressions ####

The default (`fixed = FALSE, perl = FALSE`) uses
[POSIX 1003.2 extended regular expressions][posixre].

* **Common Arguments**
  * `ignore.case` = Default "FALSE", which will force matches to be
    case-specific.
  * `perl` = Default "FALSE". If TRUE, then use
    [Perl Compatible Regular Expressions][pcre]
  * `fixed` = Default "FALSE". If TRUE, then do not treat the pattern
    as a regular expression, but as a fixed string.
  * `useBytes` = Default "FALSE", which matches character by
    character.
* `grep(myRegExp, myText)` = looks for matches of the pattern in
  myText.
  * `value = FALSE` (default) returns a numeric vector indicating the indices of
    myText that matched
  * `value = TRUE` returns a character vector of the elements in
    myText that matched.
  * `invert = TRUE` will return non-matches instead
* `grepl(myRegExp, myText)` = like grep, but returns a logical vector
  of same length as myText.
* `sub(myRegExp, repText, myText)` = Replaces the first occurance of
  myRegExp with repText in each string in myText
* `gsub()` = Same as above, but replaces *all* occurances in each
  string ("g" = "global")
* `regexpr(myRegExp, myText)` = Returns an integer vector of same
  length as myText containing the character position of the first
  match of myRegExp. "No match" are represented as `-1`. The vector
  includes two additional attributes:
  * `match.length` = An integer vector reporting the number of
    characters involved in the match, again with -1 representing "no
    match"
  * `useBytes` = Logical flag reflecting the value passed
* `gregexpr(myRegExp, myText)` = Like above, but returns a *list* of
  same length as myText, each element being an attributed-vector of
  the same format provided by `regexpr`, reporting *all* occurances of
  matches in the string.
* `myMatch <- regexec(myRegExp, myText)` = Used for parenthetical capture, the
  return value will be a list the same length as myText, each value
  being an attributed-vector similar to regexpr.
  * `regmatches(myText, myMatch)` = Takes a text vector and the
    matches generated by regexec, and returns the substrings (as a
    simple character vector) corresponding to the matches.

#### <a name='stringr'></a>stringr ####

Set of `str_*` methods for sting manipulation. Most (all?) have an
underlying `stri_*` method supporting them.

* `library(stringr)`
* `str_trim(myStrings, side = "both")` = Remove whitespace from edges
  of string
  * `side` = left/right/both
  * White space includes tabs and newlines
* `str_pad(myStrings, width = minWidth)` = Opposite of str_trim, adds
  whitespace to pad out a string to the specified width
  * `side` = Specifies which side(s) to pad
  * `pad` = Default a space, but can be any single character
* `str_dup(myStrings, times = x)` = Duplicate strings x times
  (duplicates are concatenated with an empty string)
* `str_c(vec1, vec2)` = Kind of like paste, but works off of two
  vectors using [recycling](#recycling).
  * `collapse` = If TRUE, then collapse to a single string
  * `str_join()` = Is this an alias for str_c??
* `str_order(myStrings)` = Returns an integer vector of the ordered
  indices in myStrings.
  * `decreasing` = Reverse ordering
  * `na_last` = Default TRUE, will put `NA` at end of sort
  * `str_sort()` = As above, but returns sorted input character vector
* `str_sub( myStrings, start = myStarts, end = myEnds)` = Extract
  substrings by coordinates.
  * **All arguments** will use recycling to make the cookies and milk
    come out even!
  * `start` can be a two column matrix holding both start/end
  *`str_sub(...) <- myReplacement` can be used to replace the
  *substring with new values.
* `str_wrap(myStrings)` = Wrap strings into a new length-constrained vector
  * `width` = Default 80, maximum characters per string
  * `indent` = Default 0, Optional left padding of first line
  * `exdent` = Default 0, Optional left padding of lines 2+
* Simple Utilities
  * `str_to_upper(myStrings)` = "lower case"
  * `str_to_lower(myStrings)` = "UPPER CASE"
  * `str_to_title(myStrings)` = "Title Case"
* `word( myStrings, start = myStarts, end = myEnds, sep = sepText)` =
  Extract words from a vector of strings
  * `sep` = Default a single space, the separator pattern
  * `start` = Character(s) to start searching from. Negative numbers
    count backqwards from end of string.
  * `end` = Like start, default = start.
* Regular Expression methods
  * `str_count(myStrings, myPattern)` = Vectorized count of pattern
    occurences in vector of strings
    * `str_detect(myStrings, myPattern)` = As above but returns
      logical vector
      * `str_subset( myStrings, myPattern )` = identical to calling
        `myStrings[str_detect(myStrings, myPattern)]`
  * `str_extract(myStrings, myPattern)` = Looks for the pattern in the
    input vector, returning a character vector of the same length. NA
    will be used for no matches, otherwise the first match will be
    reported.
    * `str_extract_all(myStrings, myPattern)` = As above, but returns
      a list of character vectors. No matches are empty vectors,
      otherwise will hold all matches for each input.
  * `str_replace( myStrings, myPattern, myReplace)` = Replaces the
    first occurance of pattern in string vector.
    * `str_replace_all()` = Replaces all occurances
    * `str_replace_na( myStrings )` = Replaces `NA` with the string "NA"
  * `str_locate(myStrings, myPattern)` = Recover start/end coordinates
    for pattern match in a vector of strings.
    * Return value is an integer matrix with as many rows as entries
      in myStrings, and two named columns, "start" and "end"
    * Non-matches will have `NA` for both columns
    * Zero length matches will have end = start - 1
    * `str_locate_all()` = As above, but returns a list of matrices
    * `invert_match()` = Wrapper to go around str_locate output (or
      only _all?) to invert the match coordinates for unmatched areas.
  * `str_split(myStrings, myPattern)` = Returns a list of character
    vectors, each representing the split of the input by the pattern.
    * `n` = Maximum number of places to split, default infinite
    * `str_split_fixed()` = As above, but treat pattern as fixed (not RegExp)
  * RegExp modifying functions
    * These functions can be wrapped around "pattern" in any of the
      above methods
    * `fixed()` = Force argument to be a fixed string, not a RegExp
    * `boundary()` = Set `type` to one of character, line_break,
      sentence or word.
    * `coll()` = Something to do with collation
    * `regexp()` = Seems to be the default wrapper? Maybe useful to
      over-ride some settings, like "fixed"?
* Nerdy technical stuff
  * `str_length(myStrings)` = measures length of strings in
    [code points][codepoint].
  * `str_conv(myString, myEncoding)` = Change
    [character encoding][charencode]


### <a name='dplyr'></a>dplyr ###

* Setup: `install.packages("dplyr")`
  * Sample data: `library(nycflights13)`
* Usage: `library("dplyr")`
   * Sample data: `library(nycflights13)`
* Documentation: `"browseVignettes(package = "dplyr")"`

#### Basic dplyr "Verbs" ####

* `select(myDF, cols)` = Pick a subset of the columns
  * "cols" could be a wide range of column selectors. In additional to
    traditional selections (ranges, quoted names) there are:
    * `foo1:foo2` = Range from column named "foo1" to the one called
      "foo2"
    * `-(c(foo3, bar6))` = Excluding columns named "foo3" and "bar6"
* `filter()` = Pick a subset of the rows
  * `filter(myDF, foo8 > 4 | bar1 < 10)` = Returns a data frame based
    on myDF, mathematically filtered on the values of columns "foo8"
    and "bar1"
* `arrange()` = Re-order rows
  * `arrange(myDF, foo1, desc(bar2))` = Sort by "foo1" ascending,
    "bar2" descending
    * `desc()` wrapper sets descending sort
    * Note that this is more flexible than `order()`, which apparently
      can only do all columns either ascending or descending.
* `rename()` = Change column names
  * `rename(myDF, Temperature = t, Mass = m )` = Rename "t" to
    "Temperature" and "m" to "Mass"
* `mutate()` = Add new variables or columns, or alter existing ones
  * `mutate(myDF, xy = x * y)` = New column "xy" is product of "x" and
    "y"
* `group_by()` = Creates a subclass of the DF that is structured to
  allow grouping by one or more columns
  * `groups()` = returns a list of the groups assigned to the DF
  * `ungroup()` removes grouping
  * `n()` = Returns the number of observations within a group. It is
    only functional within `summarise`, `mutate` and `filter`.
* `summarize()` = Create summary statistics.
  * Generates a new DF based on 

### <a name='reshape'></a>reshape ###

`reshape` is a core pacakge, while `reshape2` is an external update.

* [GitHub Readme][reshape2github]: "Reshape2 is a reboot of the
  reshape package ... This version improves speed at the cost of
  functionality"

#### <a name='melt'></a>melt ####

* [R Bloggers][rblogmelt]: "The melt function takes data in wide
  format and stacks a set of columns into a single column of data."
* Turns "wide" tables into tall ones by splitting table into new rows
  based on one or more columns
  * `id` = Specifies one or more identifier columns
  * `measure.vars` = Specifies the columns that should be broken out
    into rows. Will be factorized. If not provided, it seems that all
    factor columns are taken by default.
* Returns a vanilla `data.frame` that includes the `id` columns, plus
  two melted columns:
    * `variable` = A factor indicating which column from
      `measure.vars` is being shown
    * `value` = The coresponding value from that column for this row

```R
library(reshape2)
mtcars$carname <- rownames(mtcars)
# In original dataset mpg and hp are separate columns:
mtcars[mtcars$carname == "Mazda RX4", ]
          mpg cyl disp  hp drat   wt  qsec vs am gear carb   carname
Mazda RX4  21   6  160 110  3.9 2.62 16.46  0  1    4    4 Mazda RX4
# Indicate that we wish these varaibles to be on separate rows
carMelt <- melt(mtcars, id=c("carname","gear","cyl"),
                measure.vars=c("mpg","hp"))
# We now have a row for each distinct entry in mpg and hp:
carMelt[carMelt$carname == "Mazda RX4", ]
     carname gear cyl variable value
1  Mazda RX4    4   6      mpg    21
33 Mazda RX4    4   6       hp   110
```

#### <a name='cast'></a>cast ####

* Takes melted data and summarizes the data by the melt variables.
* `reshape2` uses `dcast()` for returning DFs (two dimensions) or
  `acast()` to return a vector, matrix or array (2+ dimensions).
* `dcast(myMeltedDF, myFormula, myAggregateFunc)`
  * `data` = Must be a melted data.frame
  * `formula` = "casting formula". Complex specification described in
    help.
  * `fun.aggregate` = Needed if your formula does not uniquely
    identify rows in the data set. The function should accept a vector
    of numbers and return a single statistic.

# <a name='control'></a>Control Structures #

* Useful functions
  * `seq( along.with = myObject )` = same as `seq_along(myObject)` =
    get an iteration of the things in myObject.
  * `length(myObject)` = get the total size / length of myObject

#### <a name='ifelse'></a>if, else ####

```R
size <- if (!is.numeric(x)) {
    "Error"
} else if (x < 10) {
    "Small"
} else if (x < 100) {
    "Medium"
} else {
    "Large"
}
```

* There is also a formal `ifelse()` function, which is similar to
  Perl's `condition ? ifTrue : ifFalse`:

```R
speed <- ifelse( atRedLight( myCar ), "stop", "go")
```

#### <a name='forloop'></a>for loops ####

* Curly braces are not needed if the interior of the loop is a single
  statement.

```R
for (i in 1:10) {
    for (j in 1:10) {
        # Do something
    }
}

dwarves <- c("Happy", "Sleepy", "Grumpy")
for (dwarf in dwarves) {
   cat("Looking for", dwarf, "\n")
}
```

#### <a name='while'></a>while loops ####

```R
i <- 0
while (i < 10) {
  i <- i + 1
  print(i)
}
```

#### <a name='nextbreak'></a>repeat, next, break, return ####

* `repeat` is basically `while( TRUE )`
* `break` will exit any of the loop structures
* `next` will continue the loop to the next iteration
* `return()` exits a function with a return value.
  * **NOTE THE PARENTHESES** - repeat, break and next can be used
    "naked", but if you try to just `return` then you'll return the
    function named "return" (if it's the last line in the function) or
    not return at all (if it's in the middle).
* `invisible( return(something) )` - invisible is used when you want
  to return a value without having it print out if it's not
  captured. That is, it's an aesthetic function to help avoid
  cluttering your terminal output.


```R
repeat {
   if (someCondition) break
   # Do stuff
}
```

## <a name='loopfunc'></a>Loop Functions ##

* `lapply(x, fun)` = Returns a list the same length as x after
  applying fun to each element of x
  * `sapply` = simplified version of lapply
    * `vapply` = similar to sapply, may be faster in some cases
    * `mapply` = multivariate version of sapply
* Any `...` arguments will get passed on to the supplied function
  * Important to watch for argument name collisions here!
* Potentially helpful links:
  * [The Apply Family of Functions][ButtreyApply] - via Dr. Buttrey at
    Naval Postgraduate School

### <a name='apply'></a>apply() ###

Takes an array or matrix and applies a function to its elements,
returning a vector, array or list.

* `apply(X, MARGIN, FUN, ...)`
  * `X` = An array, including matrices
    * Attempts will be made to coerce all other 2D objects with
      as.matrix, and everything else with as.array.
  * `MARGIN` = The dimension(s) of the input to extract. For a 2D array/matrix:
    * `1` = rows
    * `2` = columns
    * `c(1,2)` = rows and columns
    * If `dimnames()` have been set, you can pass the names instead
  * `FUN` = A function that will be applied to every extracted vector
  * `...` = All other parameters will be passed on to `FUN`.

```R
# Get a matrix of integers with 3 rows representing current color palette:
myRGB <- col2rgb( palette() )
# Convert them to hex codes - need to pass R,G,B separately:
myHex <- apply( myRGB, 2, function (x) {
    rgb(x[1], x[2], x[3], maxColorValue = 255)
} )
```

#### <a name='lapply'></a>lapply() ####

```R
x <- list(x1 = 35:42, x2 = c(4:1, 2:5))
#    Input  Function
lapply( x,    mean )
```

* **Always** returns a list of same length as the input, with values
  resulting from applying the function to each list member
* If the provided object is not a list, R will try to coerce it to one
* Looping is a C internal (fast)
* An anonymous function can be provided "in-line" in the lapply()
  call.

#### <a name='sapply'></a>sapply() ####

* Designed to modify the returned value from lapply:
  * List of vectors of *uniform* length:
    * Length 1 : Return a **vector**
    * All other lengths: Return a **matrix**. This is a handy way to
      get tabular output for an analysis.
  * Otherwise, return an unaltered list

#### <a name='vapply'></a>vapply() ####

#### <a name='mapply'></a>mapply() ####

```R
# Call vector() three times:
# vector( mode = "integer", length = 2 )
# vector( mode = "logical", length = 3 )
# vector( mode = "character", length = 5 )

str(mapply( vector, mode = c("integer", "logical", "character"),
            length = c(2,3,5)))
List of 3
 $ integer  : int [1:2] 0 0
 $ logical  : logi [1:3] FALSE FALSE FALSE
 $ character: chr [1:5] "" "" "" "" ...
```

* Multivariate version of sapply, applying the function to the `...`
  arguments.
  * The argument `MoreArgs` is used to shuttle additional arguments
    into the called function.
* mapply is very useful in [vectorizing](#vectorization) a function

#### <a name='apply'></a>apply() ####

```R
x <- cbind(x1 = 35:42, x2 = c(4:1, 2:5))
dimnames(x)[[1]] <- letters[1:8]
#    Input  MarginFlag  Function
apply( x,      2,         mean )
```

* Generally used with matrices, but can also work with general arrays
* 'margin' is a flag indicating what apply should, uh, apply the
  function to:
  * `1` = rows
    * This will work on lists (including data.frames) but in such
      cases *each column must have identical modes*, because R will
      coerce each row into a vector.
  * `2` = columns
  * `c(1,2)` = rows and columns
  * `c(1,3)` = First and third dimensions
  * A character vector will select dimensions with those names
* The return value depends on the return value of the function:
  * fixed-length vector : apply returns an **array**, or a **vector**
    if the returned array has length 1.
  * variable-length vector : apply returns a **list**
* Formal looping is just as efficient, apply has advantage of being a
  "one-liner".
* Utility functions based on apply:
  * `rowSums(x)` &rarr; `apply(x, 1, sum)`
  * `rowMeans(x)` &rarr; `apply(x, 1, mean)`
  * `colSums(x)` &rarr; `apply(x, 2, sum)`
  * `colMeans(x)` &rarr; `apply(x, 2, mean)`
  * For some reason, these shortcuts are *much* faster than using
    apply directly.
  * The argument `na.rm` can be set to true to automatically exclude
    NA entries from the calculation. Otherwise a single NA value in an
    operation will result in a final value of NA.

#### <a name='tapply'></a>tapply() ####

```R
x <- c(20,33,49,33,42,30,41,26,5,23)
y <- c('b','a','a','c','c','a','b','c','c','c')
z <- c('i','i','i','k','j','i','j','k','k','k')
#      Input    Factors       Function
tapply(  x,   list(y=y,z=z),    mean)

   z
y          i  j     k
  a 37.33333 NA    NA
  b 20.00000 41    NA
  c       NA 42 21.75
```

* Applies a function over a subset of a vector. The subsets are
  defined by one or more vectors, each the same length as the data.
  * Uses `split` (below) to break up the data. `tapply(x, f, mean)`
    &equiv; `lapply(split(x,f), mean)`

##### <a name='split'></a>split() #####

```R
myData <- c(20,33,49,33,42,30,41,26,5,23)
myFactors <- c('b','a','a','c','c','a','b','c','c','c')
split(myData,myFactors)

$a
[1] 33 49 30

$b
[1] 20 41

$c
[1] 33 42 26  5 23
```

* `split()` is the function used by `tapply` to combine a vector of
  data with a factor vector and produce a list of the data,
  partitioned by factors.
* Factors can be a single vector, or a list of vectors.
  * Argument `drop` (default FALSE) will cause any unrepresented
    factor levels to be excluded.

## <a name='plyr'></a>plyr ###

See also [dplyr](#dplyr) above for the next-generation package.

* `library(plyr)`
* Formal combination of "split-apply-combine". Functions are of format
  `<in><out>ply`, where `<in>` and `<out>` are one of:
  * `a` = array
  * `l` = list
  * `d` = data.frame
  * `m` = multiple inputs
  * `r` = repeat multiple times
  * `_` = nothing
* `join(xDF, yDF)` = Similar to SQL join
  * `type` = One of left (default), right, inner or full.
  * `by` = One or more common variable names to match on. If left out,
    all common names will be used.
  * `match` = Default "all", defines how to handle duplicates.
  * `join_all(df1, df2, df3, ... )` = Recursively joins two or more DFs

```R
ddply(InsectSprays, .(spray), summarize, sum=sum(count))

```

# <a name='graphics'></a>Graphics #

* Handy Examples
  * [Analysis of Nobel Prize Data][NobelGraphics] - Very nice examples
    mixing analysis of JSON objects with ggplot2
  * [R Graph Gallery][rgraphgallery] - Collection of examples from the lectures

## <a name='devices'></a>Devices ##

A "Device" is an output format / mechanism that allows R to display or
write to a file graphical images. By default, R should have a "screen"
device which opens a graphics window on your monitor. You will likely
spend most of your graphics time in such windows, but eventually will
want to preserve your output as a file. Devices allow you to write
your data to disk as a particular file format.

Device manipulation is less standardized than one might hope for. For
example, `png` specifies the file path with "file", but `pdf` requires
"filename" to be provided instead. In general, a device is opened by
calling the specific function associated with it:


Many devices can be **defined** at any time, but only one can be
**active** at a time. 

### Specific Devices ###

I am skipping some of the more esoteric formats and focusing on the
ones likely to be of utility.

* `capabilities()` = This function will tell you which formats are
  available on your specific system. It returns a named logical
  vector, with the names referencing the different device formats.
* `pdf` = Generates a PDF file. This is likely the most versatile as
  standalone output, but unlike raw image formats it can't be readily
  embeded in other files.
  * `file` = The path to the output file. Alternatively, can be `|
    myCommand`, where "myCommand" is a command line argument that will
    take the PDF as input.
  * `title` = PDF metadata field stored in the file
  * `paper` = Default "a4", but can be other paper sizes
* `png` = A lossless, compressed graphical format. Probably the best
  choice for images that you wish to embed in other files.
* `svg` = Vector (non-pixel) image format. Vector files can be scaled
  to arbitrary resolution without becomming "jagged". For some uses,
  this may be the most desirable output.
* `postscript` = Another vector format
* `jpeg` = A lossy graphics format. Will always have some graphical
  artifacts due to its compression, and for most images you generate
  will likely compress more poorly that png.

## <a name='coregraphics'></a>Core Graphics ##

[ggplot2](#ggplot2) seems to be the sexy new graphics package, but the
core graphics still offer a lot of reasonable functionality.

* Pros : Fast, adequate for many needs
* Cons : All formatting / planning must be done in advance, can't
  alter aspects of a plot once they're already defined (like expanding
  an axis to accomodate larger-ranged values)

### Formatting ###

`par()` is used to set general graphical parameters. Can be called
directly, but parameters are generally passed on to it transparently
from specific plotting functions. The most interesting arguments are
summarized below, see help page for full list and detials. Some
parameters **must** be set with par(), however - they do not seem to
throw errors when passed from a plotting function, but they're
ignored.

* `*.axis`, `*.lab`, `*.main`, `*.sub` = Fine tuning of parameters
  related to axis values, axis labels, title and subtitle
* Numeric values (eg `lend`) can also generally be passed as text (eg
  either `2` or `square`)
* par also has some read-only parameters that can be used to recover
  information about the current graphics device; These are not shown
  below.
* I am somewhat confused by units. This is partly that scaling is
  generally a universally irritating issue, but there are parts of the
  documentation that are alien to me.
* **Content Control**
  * `lend` = Line end style. **0 = round**, 1 = butt, 2 = square
  * `ljoin` = Line join style. **0 = round**, 1 = mitre, 2 = bevel
  * `lty` = Line type. 0 = blank, **1 = solid**, 2 = dashed, 3 =
    dotted, 4 = dotdash, 5 = longdash, 6 = twodash
  * `lwd` = Line width
  * `pch` = Token/symbol to use for plotting points. Integer values
    0-25 correspond to internal presets, character values will use
    that character. See [here][pchLookup] for a lookup table.
  * `usr` = vector `c(x1, x2, y1, y2)` giving extremes of plot window
    coordinates (?).
* **Color Specification**
  * The function `colors()` lists all (600+) recognized color names
    (character strings) that can be used (eg "red", "mediumspringgreen")
    * The naked string `transparent` can be used for a 100%
      transparent color (useful for fills over existing data)
      * Some functions will quietly map RGBA values to "transparent"
        if the alpha channel is zero.
  * `#RRGGBB` Hexadecimal specification, case insensitive (eg '#AA11ff')
    * `#RRGGBBAA` = As above, but with alpha channel defined as well
  * `rgb( red, green, blue, alpha)` = flexible specification including
    setting transparency level (alpha: 0 = full transparency, 1 = full
    opacity)
    * Return values are hexidecimal character strings
    * RGB and alpha values can be vectors of length over 1; This will
      return a vector as long as the longest parameter, with recycling
      occuring on any parameter shorter than the longest.
  * `palette()` = A predefined set of colors (default 8
    members). Passing an integer to a color parameter will recover
    that index from the palette. Values greate than the length of the
    palette will wrap (modulus).
    * `palette( c('red', '#004400', rgb((1:10)/10, 0.5, 0, (1:3)/3)))` =
      Sets the palette to a list of 12 colors
  * `colRamp( mySetofColors)` = returns a function that maps values
    from 0-1 to a gradient of RGB or RGBA values, returned as a
    numeric matrix
  * Gradient generators. These functions create a static list of RGBA
    hex codes. All require the first parameter, "n", which specifies
    the number of colors to generate
    * `rainbow( )` = Like it says.
    * `heat.colors( )` = 
    * `terrain.colors( )` = 
    * `topo.colors( )` = 
    * `cm.colors( )` = 
    * `( )` = 
  * `library('RColorBrewer')` contains additional color utilities.
  * `color2RGB( myColorNames )` = For a character vector of length n,
    returns a 3xn named matrix of RGB values (0-255)
* **Text Control**
  * `adj` = Justification of text
  * `cex` = font size magnification factor
    * `cex.axis`, `cex.lab`, `cex.main`, `cex.sub`
  * `crt` = character rotation (degrees). Non multiples of 90 could
    get weird
    * `srt` = string rotation, also degrees, also weird for not % 90.
  * `family` = font family name
  * `font` = integer defining font. Not sure how it relates to
    `family`
    * `font.axis`, `font.lab`, `font.main`, `font.sub`
  * `lheight` = Height of lines (for multi-line formatting) **MUST
    CALL WITH par()**
  * `oma` = text margin size, vector (bottom, left, top, right). Units
    "lines of text" **MUST CALL WITH par()**
  * `omi` = text margin size, units inches **MUST CALL WITH par()**
  * `ps` = point size of text **MUST CALL WITH par()**
* **Axis Control**
  * `lab` = vector of three values, `(NumXTicks, NumYTicks,
    LabelLength)`. Kind of.
  * `las` = style (orientation) of axis labels
  * `tck`, `tcl` = Control length of tick marks
  * `xaxp`, `yaxp` = Control of number of tick marks. Complex parsing,
    see help.
  * `xaxs`, `yaxs` = Pretty print controlling of axis interval calculations
  * `xaxt`, `yaxt` = Type of axis. "n" = no axis, "s" = show
  * `xlog`, `ylog` = Logical flag indicating if axis is logarithmic
* **General Frame**
  * `bg` = background color
  * `bty` = box (frame) type
  * `fg` = foreground (frame) color
  * `fig` = vector of (x1, x2, y1, y2) defining the figure region
    **MUST CALL WITH par()**
    * `fin` = figure dimensions, in inches **MUST CALL WITH par()**
  * `col` = default plotting color
    * `col.axis`, `col.lab`, `col.main`, `col.sub`
  * `din` = device dimensions (w, h) in inches
  * `mai` = margin width (inches) **MUST CALL WITH par()**
  * `mfrow`, `mfcol` = Divide the device into a grid ("lattice" /
    "trellis") of two or more rows and/or columns. This allows
    multiple plots ("figures") to be directed to a single device in a
    tidy layout. mfrow will fill grid row-first, mfcol will fill
    column-first. **MUST CALL WITH par()**
    * `mfg` = Specify which cell a figure should be written to. Two
      element vector specifying (col, row). **MUST CALL WITH par()**
    * If you have set up a grid with i x j elements, the plot will
      wipe itself clean before plotting the i x j + 1 entry.
  * `new` = default FALSE, which means that "high level" plot commands
    should wipe the plot window clean. If you find your plots being
    inexplicably erased, try passing `new = TRUE`. **MUST CALL WITH par()**
  * `page` = boolean controlling if plot.new starts a new page.
  * `pin` = Plot dimensions c(width, height) in inches **MUST CALL
    WITH par()**
  * `pty` = plot region to use, `s` = square, `m` = maximum **MUST
    CALL WITH par()**
  * `xpd` = Clipping region for plotting
    * `FALSE` = Only show data in plot region
    * `TRUE` = Only show data in figure region
    * `NA` = Show everything (anything that falls in device region)

### Coordinate Specification ###

`xy.coords()` is a helper function that many plotting programs use to
normalize one or more (x,y) coordinate pairs. The arguments are
generally passed to it via `...` from the calling function.

* `( x = myXcoords, y = myYcoords )` = Providing two arguments
  presumes they are a pair of vectors, which must be of the same
  length
* `xlab`, `ylab` = Default "x", "y". These parameters define the names
  of the x and y variables if the coordinates need to be extracted
  from an object (for example, if a list was provided)
* `( x = something )` = Without Y, x will be interpreted as providing
  both x and y coordinates. Parsing depends on what "something" is:
  * `xvar ~ yvar` = A formula is interpreted as describing the x and y
    variable names to use.
  * `myList` = A list will be inspected for columns named by the `xlab`
    and `ylab` variables (default "x" and "y")
  * `myMatrix` = The first column is taken as x, the second as
    y. **Labels are ignored!** - it's really simply columns 1 and 2.
  * `myDataFrame` = The first column is taken as x, the second as
    y. **Labels are ignored!** - it's really simply columns 1 and 2.
  * `myTimeSeries` = For a Time Series, x will be taken as the time,
    and y the values.
  * All other cases: x is coorced to a vector. y values are the values
    of the vector, and x values are the vector indices.

### Plotting Commands ###

* `plot()` = Basic xy point plotting.
  * Uses `xy.coords()` (above) to parse coordinate information
  * `main`, `sub`, `xlab`, `ylab` = Titles and axis labels
  * `type` = Plot type, a single character
    * **p**oints
    * **l**ines
    * **b**oth points and lines, with the lines being truncated to
      prevent overlap on the points.
    * **c** = Like "both", but with only the lines - that is, the
      truncated lines with the points left out.
    * **o**verplotted = Like "both", but with full-lenght lines (no truncation)
    * **h**istogram = lines are drawn from the bottom to each point
    * **s**teps = Each subsequent point is connected to the prior one
      by a "step", a horizontal and vertical line
    * **S**teps = like "steps", but the rules determining if
      horizontal or vertical are drawn first are inverted.
    * **n**othing = Data not drawn. Presumably useful in some circumstances
* `smoothScatter()` = Density scatterplot. Useful when you have a very
  large number of xy information to plot. Rather than representing
  individual points, this method will represent data as a colored
  contour map.
  * `nbin` = Specifies number of grid points (cells). Numeric vector
    of length 1 (same for both axes) or 2 (different values for (x,y))
  * `bandwidth` = smoothing parameter, again vector of length 1 or 2.
  * `nrpoints` = How many "outliers" (points in areas of lowest
    density) should be explicitly shown.
  * `colramp` = function to return color map (?)
  * `transformation` = function mapping density scale to color scale
    
    ```R
    smoothScatter(faithful, pch = 4, nrpoints = 10, title = "Old Faithful",
    xlab = 'Eruption Duration', ylab = 'Eruption Delay')
    ```
    
* `contour()` = Create a _de novo_ contour plot, or add contours to an
  existing one.
* `boxplot()` = Uses a function to create a box-and-whiskers plot from
  grouped data.
  * `boxplot(formula = count ~ spray, data = InsectSprays)`
    * Formula-based specification, where the left side of the formula
      defines the y-axis values, and the right side defines the x-axis
      groups.
    * `data` should either be a data.frame or a list.
  * `range` = Configures the extent of the whiskers, see help
  * `notch` = If TRUE, then a notch is added around the median of each
    box. Non-overlapping notches are "likely" to have different
    medians.
  * `add` = If TRUE, then add the plot to the current plot
* `hist()` = Plots a histogram for a vector of values
  * `breaks` = Default "Sturges", a string defining the breakpoint
    algorithm. Can also be:
    * A vector of values
    * A function that returns a vector of breakpoints
    * An integer defining the number of intervals
    * A function that returns the number of intervals (integer)
  * `freq` = Default "TRUE", which reports counts. If FALSE, will
    report density between 0-1, summing to 1.
  * `density`, `angle` = controls shading lines (default off)
  * `col`, `border` = color of the bars and their borders
  * `main`, `xlab`, `ylab` = Lables
  * `plot` = default TRUE, if FALSE then a list of breaks and counts
    is returned and no plot is made.



### "Add-on" functions ###

These functions do not generate a new plot, but instead add additional
features to an existing one. If you attempt to call one without a plot
being active, you get the error `plot.new has not been called yet`

* `points()` = Add arbitrary xy points to a plot
  * Uses `xy.coords()` (above) to parse data input
  * Uses many of the parameters from `plot()` (above)
* `lines()` = adds arbitrary line segments to a graph.
  * Uses `xy.coords()` (above) to parse data input
* `rug()` = Appends a 1-D plot of data to an existing plot.
  * Handy little tick marks detailing where the data lie in an
    otherwise summarized plot.
* `text()` = Add text to plots
  * `label` = Vector of strings to be shown
  * `x`, `y` = Vectors off plot coordinates where the text should go
  * `col`, `font` = Color and font of text, can be vectors
  * `cex` = size expansion factor, to scale font size up or down
  * `pos` = Can be NULL, or one of (1, 2, 3 or 4), which specifies
    that the text should be (below, left, above or right) of the
    coordinate.
* `mtext()` = Similar to text(), but adds text *outside* the plot (in
  the margins).
  * `text` = irritatingly different from `label`, but same thing - the
    text to add.
* `abline( a = intercept, b = slope )` = Adds straight lines to a plot
  * `coef` = Vector of two values, `c(a,b)`. That is, an alternate to
    a and b separately
  * `reg` = A regression object with a `coef` function. For example, a
    value produced by `lm()`
  * `h`/`v` = Vector of y/x values for drawing horizontal/vertical
    lines
  * `untf` = Default FALSE. If TRUE, then lines are drawn presuming
    that most passed values are already transformed (for example if
    the plot is log-transformed). `h` and `v` always refer to values
    in the original data space.

## <a name='lattice'></a>Lattice Graphics ##

Derived from the "Trellis" package in S, builds on top of the `grid`
package. The documentation is much more sparse than other R packages.

* `library('lattice')`
* Pros = Organizes many plots into one page
* Cons = Awkward and unintuitive, must be done monolithically with no
  later alterations
* Generally take a formula as the first argument
  * `xyplot(myYvar ~ myXvar | myCond1 * myCond2, myData)`
    * Y axis will be from "myYvar", X axis from "myXvar"
      * `y1 + y2 ~ x | a * b` = Both the left and right side of the
        formula can use `+`, which then causes expansion to multiple
        plots. In the above example, it will expand to both `y1 ~ x |
        a * b` and `y2 ~ x | a * b` _in the same panel_; Overlaid with
        different graphical parameters.
    * "myCond1", "myCond2" are zero or more "conditioning variables" -
      See below
    * `myData` is a data.frame or list. If left out, the parent frame
      will be used (?)
* `trellis.par.set()` = Controls high-level parameters, which appear
   to be very poorly documented; "The actual list of the components in
   'trellis.settings' has not been finalized, so I'm not attempting to
   list them here.". A corresponding `.get()` method exists.
* `show.settings()` = Pops up a nice graphical overview of the current
  settings, which also highlights some of the variables used to
  control formatting (via [mage's blog][showsettings])
* [Nice overview][npslattice]

### Conditioning Variables ###

Lattice will generate a grid of panels based on the conditioning
variables, which come after the pipe (`|`) in the formula
specification.

* `myYvar ~ myXvar | myCond1 * myCond2` = Two conditioning variables,
  "myCond1" and "myCond2"
* Separated by `*` or `+` to indicate interactions between them.
* Conditioning variables should be either factors or shingles.
  * Characters are cooerced to shingles
* `layout` = parameter controlling grid structure. A numeric vector of
  length 2 or 3, `c(numCols, numRows, numPages)`.
  * Default is cols = # levels for condition 1, rows = # levels for
    condition 2.
  * Default for only 1 variable is `c(0,n)`, where n is number of
    levels. The zero causes the layout to find the best CxR
    arrangement for the deviec aspect ratio (at time of plotting -
    will not auto-rearange if a screen window is resized).
  * `as.table` = Default FALSE. The layout normally starts in the
    lower left, and adds panels left-to-right, bottom-to-top. This
    keeps with the "smaller values go to lower left" paradigm of
    plots. Setting this to TRUE will add panels top-to-bottom. That
    keeps with the "first figure goes in upper left" paridigm of most
    publications.
* `shingle()` = A factor plus intervals.
  * Used by many plots when handling conditioning
  * Kind of confused by the concept

### Lattice Plotting Functions ###

Even though it seems like these functions are doing the plotting, they
are actually returning a complex list object of class "trellis", which
auto-prints itself when it is generated from the command line. If you
capture the output into a variable, you will need to explicitly invoke
the variable to render the plot.

* Shared (typically) parameters
  * `groups = myGrouping` = "myGrouping" is either a variable or
    expression evaluated within the "data" context, and is used to
    sub-style the output within a single panel. Typically this will be
    assigning different colors to each group instance
  * `auto.key` = Default FALSE (no legend), or a list of key values,
    or TRUE, which will take all "relevant" keys for the legend, or a
    complex list argument.
  * `key` = Rocket-science complex parameter controlling how the
    legend is rendered. Takes a list with a bunch of sub
    options. Default positioning is unfortunately not very space
    efficient.
  * `panel` = A powerful mechanism to control how each panel of the
    lattice is rendered. See below for more details.
* Univariate Plotting
  * `densityplot()` =
  * `histogram()` =
  * `qqmath()` =
  * Also listed as univariate, but help is under bivariate:
    `barchart`, `stripplot`, `bwplot`, `dotplot`
* Bivariate Plotting
  * First argument can be a formula per above, or something else (?
    see help)
  * `xyplot()` = Scatterplot
    * First parameter _must_ be a formula.
  * `barchart()` =
  * `bwplot()` =
  * `dotplot()` =
  * `stripplot()` =
  * `qq()` =
* Trivariate Plotting
  * `levelplot()` =
  * `contourplot()` =
  * `cloud()` =
  * `wireframe()` =
* Multivariate
  * `splom()` =
  * `parallel()` =

### Lattice Panel Functions ###

Every lattice graphics function has a `*.panel` function associated
with it (for example `xyplot.panel()`. You can redefine the function
that a method uses with the "panel" argument.


### Lattice Examples ###

```R
library(datasets)
library(lattice)
airquality$MonName <- factor( month.abb[ airquality$Month], levels = month.abb)
xyplot(Ozone ~ Wind | MonName, data = airquality, as.table = T)
```

## <a name='ggplot2'></a>ggplot2 ##

Attempt to combine best features of base and lattice graphics, while
defining a flexible graphics language.

* http://ggplot2.org
  * Documentation: http://docs.ggplot2.org/current/index.html
* Pros
  * Rich graphics language, intuitive by some measures
  * Extensive defaults are generally "what you want"
  * Plots may be constructed in steps, allowing sequential alterations
* "Grammar of graphics" = Nouns, verbs and adjectives to define and
  describe plots.
  * `aes()` = Aesthetics, how things appear (size, color, shape, etc)
  * `geom_*()` = Geometry, how things are organized and laid out
  * Works with labels to reference data components. Columns and other
    subsets should be named!

### Argument Management in ggplot ###

ggplot heavily leverages lexical scoping. Normally, the expression
`mpg$hwy` is a 234 integer vector from the built-in "mpg" data
set. However, if you pass `p <- qplot( mpg$displ, mpg$hwy )` and
inspect p with str(), you'll find that the Y axis is not being stored
as a vector but rather as a "language" object "mpg$hwy". When that
information is needed, ggplot will recover the data it needs with
`eval( mapping$y, envir = data)` (I'm presuming here); That is, the
string expression you provided will be evaluated in the context of the
"data" argument you provided; If that was not given, then the
environment is the current one.

I presume this is occuring many places "naked" identifiers are being
used. This means the longer you hold onto a ggplot object where "data"
has not been defined, the more likely it is that something in the
lexical environment has changed without you noticing it.

The "data" argument is not stored as dynmaically-evaluated languge: It
is held as a full copy of the input. From a memory management
perspective this might be undesirable for very large data sets.

### Geom ###

* `geom_smooth()` = smoothed conditional mean
  * `geom_density()` = smooth density estimate

### ggplot2 Functions ###

* `qplot( myXlabel, myYlabel, data = myData)` = Quick Plot.
  * First two arguments are the unquoted names of the x and y variables.
  * `data` = Optional data.frame, where information is extracted
    from. If not provided, then the labels will be lexically searched
    in the environment (and a fatal error thrown if not found)
  * Other data sources can be used to configure aspects of the
    plot. For example, `color = vendor` will look for a data source
    labeled "vendor" (in the provided data or the environment),
    factorize it if needed, and assign the color palette to the points
    based on that factor.
  * `color` (aka `colour`) = sets the point color
  * `shape` = sets the point shape
    * `scale_shape()`
  * `geom` = Define one or more geoms to structure the plot. If left
    blank defaults are chosen based on your input.
  * `facets` = Easily define faceting with a formula that defines `col
    ~ row` faceting variables, with a `.` used if only rows or columns
    are needed.
      
  ```R
  qplot(displ, hwy, data = mpg, geom = c("point", "smooth")) # XY plot
  qplot(hwy, data = mpg, fill = as.factor(cyl), facets = drv ~ .) # Histogram
  ```

## <a name='networkD3'></a>D3 Networks ##

"Creates D3 JavaScript network, tree, dendrogram, and Sankey graphs"

* Installation: `install.packages("networkD3")`
* Usage: `library("networkD3")`

## <a name='images'></a>Image Packages ##

* JPEG images
  * `library(jpeg)`
    * Install: `install.packages("jpeg")`
  * `readJPEG( source = myImagePath)`
    * `native` Default FALSE, which results in a 3D numeric array with
      dimensions corresponding to channels * height * width. If TRUE,
      then a 2D numeric array (W x H) with class "nativeRaster" is
      created.
  
# <a name='functions'></a>Functions #

* R will fail if you access a function with fewer arguments than it
  was defined with, unless the missing arguments have a default set:
  `argument "arg1" is missing, with no default`
* It will always fail if you pass more arguments than expected,
  or use argument names that are not explicitly specified in the
  function (`unused argument`).
* `class(myFunc)` will return "function"
* Arguments to functions have both a position in the argument list
  (first parameter, second parameter, etc) and a name (`length = 5`).
  * Named parameters can be passed in any position in the function call.
  * If an un-named parameter is encountered, R will slot that into the
    first "unclaimed" argument. So for example in `matrix()`, "nrow"
    can be passed as the second argument and "ncol" as the third:
    
    ```R
    # From the documentation:
    # matrix(data = NA, nrow = 1, ncol = 1, byrow = FALSE,
    #        dimnames = NULL)
    > x <- matrix(ncol = 3, 1:6, nrow = 2 )
    > y <- matrix(1:6, 2, 3)
    > identical(x,y)
    [1] TRUE
    ```
    
  * This is crazy goofy and looks prone to all kinds of hard-to-catch
    bugs. Probably best to not mess with the order and used named
    arguments as much as possible.
    * Arguments also use partial name matching. Inside a function call
      this is probably not as dangerous as in a data frame, but
      likewise if the function changes over time a unique partial
      argument name might become non-unique later.
  * <a name='dotdotdot'></a>`...` is used to pass arbitrary,
    unspecified arguments onwards to sub-functions. That is,
    specifying `...` in a function definition then allows use of `...`
    again internally to pass on the "extra" arguments to internal
    function calls:
    
    ```R
    myFuncTwo <- function( a = NULL, b = 2, ... ) {
        myFunc( a, b, c = 4, ... )
    }
    ```

    * `...` is also used in "generic functions" for argument dispatch.
    * `...` is used when the number of arguments in a call can not be
      predicted, like in `paste()` or `cat()`, where 1-or-more
      arguments are concatenated together.
      * In these situation any "other" arguments *must* be named when
        calling the function (otherwise they will be gathered into the
        "general" arguments specified by the initial `...`).
* R calls the defined argument list **formals**
  * The arguments specified for a function can be found with the
    `formals()` function, which returns a "dotted pair" list of
    key/values.
  
    ```R
    formals(fun = vector)
    $mode
    [1] "logical"
    
    $length
    [1] 0
    ```


```R
myFunc <- function( arg1, arg2 = optionalDefaultValue) {
   # Stuff happens
   returnValue # Last operation gets returned
}
myFunc <- function( arg1, arg2 = 2) {
    arg1 + arg2
}
```


### <a name='scoping'></a>Symbol Scoping ###

* ... is quite weird
* Scott recommends: [How R Searches and Finds Stuff][RScopeSearching]
* All objects in R reside in an **environment** (which is also an
  object). Each environment has two<sup>&dagger;</sup> things:
  1. A **frame**, which is a structure holding all the environment's
     objects
  2. The **owner** (parent) environment. <sup>&dagger;</sup>*The "Empty
     Environment" is the ultimate top-level environment, it does not
     have an owner.*
* Environments can be manually created with `new.env()`
* The owner can be found or changed with `parent.env( envObject )`
* Your current environment is returned with `environment()`
* `ls()` reports objects held by the current environment. You can use
  `ls( name = envID )` to find objects in an arbitrary
  environment. The name argument is flexible and can use an
  environment object, name or search position.
* `rm()` removes objects from an environment
  * Ron recommends that scripts begin with `rm(list=ls())`. This will
    clear all variables held by the current environment and assure
    that you're starting with a clean slate.
* The `assign()` function allows an object to be created in a
  particular environment. Conversely, `get()` recovers objects, and
  allows an environment to be specified.
* There are some special environments:
  * **.GlobalEnv*, which can be accessed with either `.GlobalEnv` (no
    quotes) or `globalenv()`
  * **R_EmptyEnv**, the top-level empty environment, accessed via
    `emptyenv()`
  * **base**, accessed via `baseenv()`
* A package in R is associated with 3 environments. These are formally
  represented with '<environment>:<package>', eg `namespace:stats`
  1. The **package: environment** holds the "exported" objects you
     presumably want from the package; Functions, constants, data,
     etc.
  2. The **namespace: environment** has the same (sort-of kind-of)
     objects from the package environment, plus (optionally?) hidden
     "support" objects
  3. The **imports: environment** holds package dependencies; Other
     packages that this package is going to need to be loaded as well.
    * On CRAN, direct dependencies are shown under "Imports", while
      indirect dependencies appear under "Depends". Apparently this
      distinction can be consequential.
    * The imports environments are enclosed by the namespace:base
      environment.
* *For the love of all that's holy this is confusingly complex*
* A function "resides" in an environment, and runs in one. By
  default these are the same, but the running environment can be
  changed with the function's environment property.
* Scoping and environments are important to R in order to facilitate
  the location of named objects. Once R has an object in hand, it no
  longer really cares what environment is associated with it. So
  getting an environment from a (non-function) object is hard.
* [Graphical view of search pattern][RScopeMap]. As far as I can tell,
  the search is:
  1. Try local environment
  2. Recursively try enclosing environments. For package functions in
     a package called myPackage, this would generally
     be:
    1. `namespace:myPackage`
    2. `imports:myPackage`
    3. `namespace:dependencyPackage` / `imports:dependencyPackage`
       recursion for any packages that myPackage has specified as an
       Imports dependency.
  4. `namespace:base` if not found in package or Imports
  5. `R_GlobalEnv`
  6. `namespace:dependencyPackage` / `imports:dependencyPackage`
     recursion for any packages that myPackage has specified as an
     Depends dependency.
* An object can be explicitly defined with `::`, eg `myPackage::myFunc`
  * <a name='internalscope'></a> `::` works for exported objects;
    Three colons can by used for internal objects, eg
    `myPackage:::someThing`
* Lexical scoping works allows construction of closures. In the
  example below, "make.dice" returns a function that "remembers" the
  caller parameter:

  ```R
  make.dice <- function (sides) {
      dice <- function( rolls ) {
          # runif() is the uniform distribution = "random number"
          # My attempt at generating random numbers:
          # floor(sides * runif(1:rolls)) + 1
          # Scott's suggestion for a more R-ish way:
          sample.int(n = sides, size = rolls, replace = TRUE)
      }
      dice # Return the closure
  }
  sixSided <- make.dice(6)
  sixSided(5)
  [1] 1 3 6 3 2
  ```

  * Closures are useful for operations that take parameterized
    functions as input. For example, `optimize()` finds the minimum or
    maximum of a function within a range. A closure allows some
    parameters to be fixed while varying only the arguments being
    "scanned" by optimize.
  * Scott recommends limiting usage of closures to where they're
    really needed, because they make following code flow more
    difficult.
* The scoping paradigm is apparently why R objects must all be kept in
  memory, as opposed to cached to disk.
* `<<-` is a special gets method that will assign a variable "above"
  the current environment:
  1. If the assignment is occuring in a function's environment, R will
     first "look up" into the function's environment (and recursively
     through any nested parents) to try to find the variable. As soon
     as it locates the variable, it assigns the value within that
     environment.
  2. If R fails to find the variable in any of the nested function
     environments (or if the assignment occurs outside a function)
     then assignment will occur at the global environment, whether the
     symbol exists there or not.
    

Great scoping example from [O Beautiful Code][RScopeSearching]:
```R
age <- 32 
MyFunction <- function() {
   age <- 22 
   FromLocal <- function() { print(paste("Local", age + 1 )) } 
   FromGlobal <- function() { print(paste("Global", age + 1 )) } 
   NoSearch <-  function() { age <- 11; print(paste("NoSearch", age + 1 )) } 
   environment( FromGlobal ) <- .GlobalEnv 
   FromLocal() 
   FromGlobal() 
   NoSearch() 
} 
MyFunction() 
[1] "Local 23"
[1] "Global 33"
[1] "NoSearch 12"
```

This is a good example from a quiz:
```R
f <- function(x) {
  g <- function(y) {
    y + z
  }
  z <- 4
  x + g(x)
}
z <- 10
f(3)
```
Compare to Perl:
```Perl
use strict;
my $z = 10;

# Prints 16:
print &f(3);

sub f {
    my $x = shift;
    my $g = sub {
        my $y = shift;
        # This closure has not encountered the local $z below
        # So the closure "holds on to" the global value of $z defined above
        return $y + $z;
    };
    
    # This $z is not going to be used!
    my $z = 4;
    return $x + &{$g}($x);
}
```

The difference here is that **R is not using references**. It is
parsing everything by name. So when `g()` is evaluated inside `f()`,
and it encounters an expression using `z`, it will start following the
environment search pattern to find the "nearest" instance of z, which
it quickly finds inside function f's environment.

* `with()` and `within()` = Methods to "apply a function to data"
  inside a controlled environment. For example, in this sample from
  `?rug`, the column `faithful$eruptions` can be referenced just as
  `eruptions`:

```R
require(stats)
with(faithful, {
    plot(density(eruptions, bw = 0.15))
    rug(eruptions)
    rug(jitter(eruptions, amount = 0.01), side = 3, col = "light blue")
})
```

# <a name='packages'></a>Packages #

* `a <- available.packages()` = puts list of all packages in `a`
* `head(rownames(a), 3)`
* `install.packages("<PACKAGE_NAME_HERE>")` = install the package from CRAN
 * First install asks you to specify a local mirror
 * Can pass a list of package names as well
 * RStudio : Menu option Tools > Install Packages
 * Code to install only as needed:
   * `require(myPackage) || install.packages("myPackage")`
* `source("http://bioconductor.org/biocLite.R")` = loads a remote
  script for installing the Bioconductor packages
  * `biocLite()` = Installs **ALL** of Bioconductor
  * `biocLite(c("<PACKAGE_1>","<PACKAGE_2>",...))` = Installs just a
    subset of packages
  * AFAICT this is not a general loading mechanism but is rather a
    specific helper script for managing large-scale package
    installations. Probably common to other resources as well.
* `library(ggplot2)` = load the ggplot2 package into your current R session
  * `install()` downloads code to your local machine. Needs to be
    done once (per machine)
  * `library()` makes it available for your current session /
    script. Will automatically **load** dependencies, but I don't
    think it will automatically **install** them. Needs to be done
    every session. Should not use quotes.
* Rtools is needed for building packages in Windows.
  * Also want to `install.packages("devtools")` after downloading
* `search()` lists the currently installed namespaces, and the
  precedence order for when a symbol exists in two or more namespaces.
  * `library` by default will insert packages at position [2] (highest
    level excepting `.GlobalEnv`), bumping down all other packages.

# <a name='oop'></a>Object Oriented Programming #

R had OOP "cobbled on" late in its history. It actually has been added
*twice*; When you see references to "S3" or "S4", they are referring
to OOP paradigms from versions 3 / 4 of the [S language][S], which are
quite different. You may use both simultaneously, but it is highly
disadvised.

## <a name='s3'></a>S3 OOP ##

* S3 is based on the [1988 version of S][sHistory]
  * You get it "for free", no need to load additional libraries
  * CONS: Kludgy, weird implementation. Aspects of the syntax are
    oddly defined and may be difficult to predict behavior.
  * PROS: Simple but still powerful. Scott says it is much harder to
    maintain code when using the S4 paradigm; Changes tend to require
    propogation across all dependent scripts and libraries, whereas
    changes in S3 are less likely to break reliant code.
* The `.` character has a special role in S3 - It defines
  class-specific dispatch methods (see below)

#### <a name='s3classes'></a>Importance of Classes in S3 ####

Classes play a central role in S3. They are used for "method
dispatch", where a function can call different code depending on the
class(es) of the passed argument.
    
* `UseMethod( "baseMethodName" )` = Generally (always?) used inside a
  "stub" to set up a method as being S3 dispatched:
  
  ```R
  twiddle <- function( x ) {
      UseMethod( "twiddle" )
      # If x's first class is 'foo', then R will look for a function
      # called "twiddle.foo"
      # If a function can not be found, R will look for "twiddle.default"
      # If it can't find either you get an error:
      # no applicable method for 'twiddle' applied to an object of class ...
  }
  ```
  
  * Note that the "inherent" classes of an object can have methods,
    too. So you could make a method "twiddle.logical" or
    "twiddle.double".
* `NextMethod()` = Dispatches functions on the next class (if any)
  held by the object.
  * Should be called from inside the function *that was called by* the
    previous UseMethod/NextMethod block.
  * Keep in mind that return values from NextMethod will end up
    "trapped" inside the previous calling function unless you do
    something with them. NextMethod would presumably be most useful
    for configuring aspects of the analysis done by the "primary"
    class' function.
* `analyze.info.detail` = This function could be dispatched by either
  `analyze.info()` on a "detail" object, or by `analyze()` on an
  "info.detail" object.

Scott indicates that the use of NextMethod below is highly unusual. I
do not think I am comprehending it properly.

```R
speak <- function( ... ) {
    # This is the "dispatcher" function. It will look for a function named
    # "speak.<firstClassName>" or "speak.default"
    UseMethod( "speak" )
}
saySomething <- function (animal, what) {
    sprintf("%s says %s", animal, what)
}
speak.cow <- function(animal, vehemently = FALSE, alert = FALSE, ... ) {
    # speak() method for class "cow"
    # First see if there are any other classes to deal with:
    NextMethod( )
    what <- if (vehemently) {
        if (alert) {
            # Vehemently alert!
            "I said MOO WHO?!?!"
        } else {
            "MOOOO!!!"
        }
    } else if (alert) {
        "Moo who?"
    } else {
        "Moo"
    }
    saySomething( animal, what );
}
speak.dog <- function(animal, alert = FALSE, ...) {
    # speak() method for class "dog"
    # First see if there are any other classes to deal with:
    NextMethod( )
    what <- if (alert) {
        "BARK BARK BARK!!!"
    } else {
        "Woof"
    }
    saySomething( animal, what );
}
speak.singer <- function(animal, ...) {
    # This method is designed to be called as a secondary (NextMethod)
    # function to the primary class. It is called before the primary
    # class' dispatched function returns. If we return a value here,
    # it will be quietly "absorbed" inside the primary block, and we
    # will never see it. So we print the value to STDOUT here so the
    # cow can sing.
    print(sprintf("%s sings La la la laaaaa!", animal))
    return()
}
speak.default <- function(animal, ...) {
    # Stay silent. This is needed to catch NextMethod calls when the
    # class stack has no more classes available.  To avoid having NULL
    # returned and cluttering up our output, we explicitly return
    # https://stackoverflow.com/a/25719114
    return()
}

# Make some animals:

pet <- "Fido"
class(pet) <- "dog"
# Dogs behavior not affected by vehement, so the flag is quietly ignored
speak(pet, vehemently = TRUE)
[1] "Fido says Woof"

bovine <- "Bessie"
class(bovine) <- c("cow","singer")
# Cows can be both alert and vehement
# This cow is also a singer
speak(bovine, alert = TRUE)
[1] "Bessie sings La la la laaaaa!"
[1] "Bessie says Moo who?"
```

* Scott recommends testing auto-dispatched methods with a direct call
  to them (`speak.singer("Oscar")`) to verify that they're behaving as
  anticipated.

## <a name='s4'></a>S4 OOP ##

* [StackExchange S4 Useful Links][SeUsefulLinks]
* S4 is recommended when
  [developing with Bioconductor pacakages][s4bioconductor].
* My [Bioconductor notes](./bioconductorNotes.md)
* [Useful S4 / slots description][SeS4slots]
* `typeof( someS4object )` &rarr; `S4`

### <a name='s4setclass'></a>setClass ###

* `setClass(className, representation)` is used to define an S4 class.

   ```R
   setClass("dog", representation = representation(
             color = "character",
             age   = "integer",
             friendly = "logical"
             ))
   ```
   
 * Arguments:
   * `representation` = Defines the **slots** that the objects will
     have. A slot is a named container that can hold arbitrarily
     complex data, including other S3/S4 objects. If a "foo" object
     has a "bar" slot, it will have one and only one value for bar,
     but that value can be anything *(except perhaps another foo
     object?)*
   * `contains` = optional, defines superclasses of this class. Used
     to extend existing objects, or to inherit functionality from
     them. Can be "VIRTUAL".
   * `prototype` = optional default values for the object
   * `validity` = optional sanity-checking method
   * `where` = optional specification of the environment
   * `sealed` = if TRUE, prevents the class from being redefined by
     subsequent setClass calls.

### <a name='s4new'></a>new ###

### <a name='s4rle'></a>Rle ###

* "Run-length encoding"
* Compact mechanism to represent a vector with repetition.
  * "Runs" of values are collapsed into the "values" property, with
    the length of each run stored in a separate "lengths" property
* Construction
  * Directly with `Rle( someVector )`
  * Explicitly with `Rle( values = valueVector, lengths = lenVector)`
  * Coerced via `as( compatibleObject, "Rle")`
* Conversion
  * `as.vector( myRle )`
  * `as.factor( myRle )`
* `getOption("dropRle")` = default false. For some operations using
  Rles, affects if you get an Rle back (FALSE) or a vector (TRUE).

```R
myVec <- c('c','a','a','a','a','b','b','c','b','b')
myRle <- Rle( myVec );
myRle

character-Rle of length 10 with 5 runs
  Lengths:   1   4   2   1   2
  Values : "c" "a" "b" "c" "b"
# That is: 1 c, 4 a, 2 b, 1 c, 2 b

as.factor(myRle)
 [1] c a a a a b b c b b
Levels: a b c

identical(as.vector(myRle), myVec)
[1] TRUE

# Can also build with a list of values and repeat lengths:
anRle <- Rle( values = c("c","a","b","c","b"), lengths = c(1,4,2,1,2))
identical(anRle, myRle)
[1] TRUE
```

# <a name='rdeveloping'></a>Developing in R #

* Some coding style suggestions
  * [Google R Style Guide][GoogleStyle]
  * [R Coding Conventions][RCC]

### <a name='errors'></a>Errors and Error Handling ###

* Four levels of error:
  * `message` = informational
    * `suppressMessages( boolean )` = If true, turn off messages
  * `warning` = Possible problem, no impact on code flow
    * `suppressWarnings( boolean )` = turn of warnings if true
  * `error` = Fatal problem, code halts (via `stop`)
  * `condition` = Programmer-defined event
* Funtions that throw errors. The first arguments in the calls will be
  coerced into strings then joined together to make the message.
  * `message()` = print a message.
  * `warning()` = throw a warning.
  * `stop()` = throw an error.
    * `stopifnot( cond1, cond2, ... )` = conditional stop, will halt
      unless all passed arguments are true. If a stop is triggered,
      the first condition that caused it is reported.

### <a name='debugging'></a>Debugging ###

* `traceback()` = Prints the call stack of the last uncaught error
  * Errors within `try` and `tryCatch` are not considered
* `debug( aFunction )` = Flag a function for step-wise debugging
  * `undebug( aFunction )` to turn off
* `browser()` = Break out of execution and allow inspection of the
  environment. Generally included within code that's being
  developed. Special commands when in browser mode:
  * `help` = show browser commands
  * `c` aka `cont` = return to execution
  * `f` = finish execution of current loop or function
  * `n` = next statement, stepping over functions
  * `s` = next statement, stepping into functions
  * `where` = stack trace at current point
  * `Q` = exit browser *and* evaluation
* `trace` = Inject debugging code into any function
  * Arguments define what the code is, and where it should go
  * `tracingState()` = turns tracing on and off
* `recover()` = Can change error behavior so a browser is invoked
  rather than throwing a message.
  * Set `options(error = recover)` to automatically drop into recover
    mode when an error is thrown. You will be presented with a stack
    trace and can choose the frame you wish to inspect.

### <a name='profiling'></a>Profiling ###

* `system.time( arbitraryExpression )` = calculates the number of
  seconds for arbitraryExpression to evaluate
  * Useful when you want to time a specific block of code
  * Returns an object of class `proc_time`
  * Holds `system` (CPU), `user` and `elapsed` components.
  * System and user times are broken into "self" and "child" components
* `Rprof( profileFile )` = Deep profiling mode
  * R must have been *compiled* with profiling support
  * Do **not** mix system.time with Rprof
  * Works by sampling the call stack
    * Default sampling frequency is 20 msec
    * Fast-running code will need shorter inteval (or may not benefit
      from profiling in the first place)
  * Raw output is not really useful, needs to be fed to `summaryRprof()`
    * Data can be normalized `by.total` or `by.self`
  * Will not profile C or Fortran code

```R
Rprof( filename = "myBenchmarks.txt" )
slowCode()
moreCode()
Rprof( NULL ) # Turns off profiling
dontCareCode()
# Turn profiling on again, append to previous file:
Rprof("myBenchmarks.txt", append = TRUE)
otherSlowCode()
# It looks like you need to turn it off to write final data to file?
Rprof(NULL)
# Show all details:
summaryRprof("myBenchmarks.txt")
# Or just the "self" level:
summaryRprof("myBenchmarks.txt")$by.self

```

* `install.packages("microbenchmark")` = Provides sub-millisecond
  precision for timing.
  * `library(microbenchmark)`; `microbenchmark( someExpression )`
  * Note that the `unit` argument is set to dynamically adjust for the
    actual benchmarks. To fix it, set `unit = "us"` (or some other
    unit).

### <a name='dataobject'></a>Programmer-like Objects ###

That is, structuring complex objects as one might in other
langauages. Scott recommends using lists, but warns that it becomes
unweildy for large amounts of data.

```R
fido <- list( name = "Fido", toys = c("ball", "stick"), color = "brown")
butch <- list( name = "Butch", food = c("cats", "small children"), friendly = F)
# Yeesh - fido = fido.
kennel <- list( dogs = list(fido = fido, butch = butch), clean = TRUE)
kennel
$dogs
$dogs$fido
$dogs$fido$name
[1] "Fido"

$dogs$fido$toys
[1] "ball"  "stick"

$dogs$fido$color
[1] "brown"

$dogs$butch
$dogs$butch$name
[1] "Butch"

$dogs$butch$food
[1] "cats"           "small children"

$dogs$butch$friendly
[1] FALSE

$clean
[1] TRUE

# If I passed the kennel's dogs as an un-named list:
kennel <- list( dogs = list(fido, butch), clean = TRUE)

# ... then I'd need to use array subsetting rather than names to
# get information on its $dogs:

kennel$dogs[[1]]$toys
[1] "ball"  "stick"
```

# <a name='texthandling'></a>Text Handling #

* `print()` = Basic STDOUT, includes newline
  * Objects can define an internal print method, which will be invoked here
    * Objects can also have a `toString()` method - I'm not sure if
      that's how print gets implemented, or if it is different
  * Assured output to terminal; Other methods are manipulation
    methods, that will output in an interactive session but only if
    not captured by another object.
* `cat()` = concatenates character data into a single string
  * `fill` = Default FALSE, does not automatically include newlines,
    either set to TRUE or terminate with "\n" if desired
* `format()` = fine-grained control over object representation.
  * Significant digits, scientific notation, date/time
* `sprintf()` = Exposed C function.
  * Will implicitly loop! If multiple vectors are provided to multiple
    bind locations, then they **must** be of the same modulus or
    sprintf will fail to execute.

  ```R
  > sprintf("%s %d", "test", 1:3)
  [1] "test 1" "test 2" "test 3"
  ```

* `prettyNum()` = Fairly extensive numeric formatting options

# <a name='probability'></a>Probability #

## <a name='distributions'></a>Distributions ##

* Nearly all distributions have four functions associated them, keyed
  by the first letter of the function name: `d`, `p`, `q` and `r`. For
  example, the Uniform Distribution has four functions:
  * `dunif()` = The [probability density function][WpDensityFunc], "the
    relative likelihood for this random variable to take on a given
    value"
    * The first argument, `x`, is a vector of quantiles
  * `punif()` = The
    [cumulative distribution function][WpCumulativeFunc], "the
    probability that a real-valued random variable X with a given
    probability distribution will be found to have a value less than
    or equal to x"
    * The first argument, `q`, is a vector of quantiles
  * `qunif()` = The [quantile function][WpQuantileFunc], "the value at
    which the probability of the random variable being less than or
    equal to this value is equal to the given probability"
    * The first argument, `p`, is a vector of probabilities
  * `runif()` = A function generating random values selected from the
    distribution
    * The first argument, `n`, is the number of observations

#### Specific Distributions ####

* `<*>` = one of d/p/q/r
* Logical flag `log` (for `d...`) and `log.p` (for `p...` or `q...`)
  will report logarithms of probabilities when true. Default is
  (always?) FALSE.
* Logical flag `lower.tail` will report probabilites for `P[X <= x]`
  when TRUE (default), while FALSE reports `P[X > x]`

* `<*>unif()` = The [uniform distribution][WpUniformDist]
  * All functions have `min` and `max` arguments
* `<*>norm()` = The [normal distribution][WpNormalDist]
  * All functions have `mean` and `sd` arguments
* `<*>pois()` = The [Poisson distribution][WpPoissonDist]
  * All functions have the `lambda` argument
* `<*>bionom()` = The [binomial distribution][WpBinomialDist]
  * All functions have the `size` and `prob` arguments
* `<*>gamma()` = The [gamma distribution][WpGammalDist]
  * All functions have the `shape`, `rate` and `scale` arguments
* `<*>exp()` = The [exponential distribution][WpExponentialDist]
  * All functions have the `rate` argument

## <a name='rng'></a>Random Number Generator ##

Random numbers in R are not meant to be cryptographic, but rather to
fall as randomly as practical along a specified distribution. Multiple
[pseudo random number generator][WpPRNG] (PRNG) algorithms can be
chosen.

* `set.seed( mySeed )` = Sets the RNG seed. mySeed is an integer. The
  seed will "auto advance" as the code executes.
  * If you know your code has a random component (or suspect it might)
    it is a good idea
* `RNGkind(kind = rngType)` = Choose alternate PRNG algorithms. The
  default is [Mersenne-Twister][WpMersenneTwister], which has an
  insanely large period.
* `sample( x )` picks random values from x
  * The second argument `size` specifies the number of values to
    select. If left out, will equal the length of x. If only x is
    provided, then the returned value will be a random permutation of
    x.
  * When argument `replace` is FALSE (default) then the sample is
    chosen without replacement (each value in x can only be chosen
    once), otherwise replacement is used.

## <a name='lecture89'></a>Random Linear Model ##

*This code is just transcribed from [lecture 89](https://class.coursera.org/rprog-033/lecture/89)*

Working with model:

*y = &beta;<sub>0</sub> + &beta;<sub>1</sub>x + &epsilon;*

```R
set.seed(20)
# x is a numeric vector generated from the normal distribution:
x <- rnorm(n = 100)
e <- rnorm(n = 100, mean = 0, sd = 2)
# y will also be a 100-element numeric vector:
y <- 0.5 + 2 * x + e
summary(y)
plot(x,y)
```

X is binary:

```R
set.seed(10)
x <- rbinom( n = 100, size = 1, prob = 0.5 )
e <- rnorm(n = 100, mean = 0, sd = 2)
y <- 0.5 + 2 * x + e
summary(y)
plot(x,y)
```

Simulate from Poisson:

Y ~ Poisson(&mu;)

log&mu; = *&beta;<sub>0</sub> + &beta;<sub>1</sub>x*

```R
set.seed(1)
x <- rnorm(n = 100)
logMu <- 0.5 + 0.3 * x
y <- rpois( n = 100, lambda = exp(logMu) )
summary(y)
plot(x,y)
```

# <a name='clustering'></a>Clustering #

Finding things that are "near" to each other

* Clustering can be unstable - small changes to the input data can
  result in different final clusters being generated
* Turning the analysis into finite clusters requires a decision of
  "where to cut", which will generally be at least partly arbitrary.

#### Requirements

* Distance metric
  * If the metric is poorly defined the results will be as well
  * Continuous metrics
    * [Euclidean][Euclidean_distance]
    * Correlation similarity
  * Binary metric
    * [Manhattan distance][Taxicabgeometry]
* Merging approach = When clusters of two or more points are merged,
  how do you calculate the cluster-cluster distance?

## <a name='HierarchicalClustering'></a>Hierarchical Clustering ##

Begin by inspecting the closest two entries, then the next closest,
etc.

* Will generate a dendogram.
* Deterministic

### Functions

* `dist(myDF, method = "euclidean")` = Calculates the distance for
  the data.frame "myDF", using the specified calculation method
  * Methods:
    * "euclidean" = [Euclidean distance][Euclidean_distance]
    * "manhattan" = [Manhattan distance][Taxicabgeometry]
      * "canberra" = [Canberra distance][Canberra_distance] (weighted Manhattan)
    * "maximum"
    * "binary"
    * "minkowski" = [Minkowski distance][Minkowski_distance]
      * p = 1 &rarr; Manhattan
      * p = 2 &rarr; Euclidean
      * ... plus any other value of p
  * Returns a double vector of class `dist`
* `myHier <- hclust(myDist)` = Generates hierarchical clustering of
  the provided distance information
  * `plot(myHier, frame.plot = TRUE)` = Plots the dendrogram, saavy to
    the hierarchical data.
    * "frame.plot" = Default FALSE, set to TRUE to see height values
    * "hang" = fraction of total plot height to draw "leaf"
      lines. These terminal lines are aesthetic only - the
      relationship (distance) between groups is represented by the
      relative positions of the horizontal connecting bars, which
      represent the groupings.
      * "0" = No leaves at all
      * "-1" = drops all leaves to the x-axis.
  * `cutree( myHier, k = 4 )` = Cuts the provided tree object into "k" groups.
    * Returns a named integer vector specifying group membership
    * "h" = Alternatively, provide an explicit height to cut at
* `heatmap(myMatrix)`

## <a name='kmeans'</a>K-Means Clustering ##

Groups a set of objects into a requested number of clusters.

1. Specify the number of clusters
1. Determine initial centroid positions
   * Random assignment initially?
1. For each object, assign it to the closest centroid'
   * Using whatever distance metric you've chosen
   * How to break ties?
1. Recalculate centroid positions based on the positions of their members
   * What happens if a centroid has no members?
1. Repeat steps 3-4
   * Presumably have a convergence threshold to stop the process.

* Returns:
  * Estimated location of each centroid
  * Objects assigned to each centroid

### Functions

* `kmeans( x = myData, centers = clusters )`
  * "x" = a data set that can be coerced into a numeric matrix
  * "centers" = Either an integer representing the number of clusters,
    or a set of cluster starting points
    * If an integer is provided, the centers will be randomly picked
      from your data points.
      * "nstart" = Default 1. If using integer centers, the nstart
        parameter specifies how many random starting combinations to try.
  * "iter.max" = Default 10, maximum number of allowed iterations
  * "algorithm" = Choose the specific algorithm.
    * "Hartigan-Wong" = Default, 'generally does a better job'
    * Two other options: "Lloyd" === "Forgy", or "MacQueen"
  * Returns a List of class "kmeans"
    * `$centers` = Matrix of centroid positions, named by cluster
      number and dimension names.
    * `$cluster` = Integer vector assigning cluster number to each
      input object.
    * `$size` = Integer vector of the count of objects in each cluster
    * `$totss` = total sum of squares
    * `$withinss` = Vector with sum of squares for each individual cluster
    * `$iter` = Number of iterations performed

#### Plotting Example ####

```R
## From Coursera lecture
## https://www.coursera.org/learn/exploratory-data-analysis/lecture/6hOqi/k-means-clustering-part-2
set.seed(1234)
## Generate 30 points randomly assorted around 3 centroids: (1,1), (2,2), (3,1)
numPoints <- 30
x <- rnorm(numPoints, mean = rep(1:3, each = numPoints/3), sd = 0.2)
y <- rnorm(numPoints, mean = rep(c(1, 2, 1), each = numPoints/3), sd = 0.2)
## Organize as DF
dataFrame <- data.frame(x = x, y = y, row.names = 1:numPoints)

## Look for the clusters. Because we generated three clusters above,
## unambiguous results should be found when looking for 3. Trying
## other cluster numbers (searchFor) will result in different
## outcomes each time you re-run the analysis
clusterAndPlot <- function( searchFor ) {
    kmeansObj <- kmeans(dataFrame, centers = searchFor)
    ## Plot and label the input
    plot(x, y, col = kmeansObj$cluster, pch = 1, cex = 2)
    text(x + 0.05, y + 0.05, labels = as.character(1:numPoints), col = "blue")
    ## Add the centroids
    points(kmeansObj$centers, col = 1:searchFor, pch = 3, cex = 3, lwd = 3)
}
clusterAndPlot(3)
```

# <a name='randomstuff'></a>Random Things #

* `R.Version()` = show the software version information for current
  R session
* There have been requests for
  [emacs keybindings in RStudio][RstudioEmacs] but they are no plans
  to implement it.
* `vignette(all = FALSE)` = Get a list of all vignettes in attached
  packages.
  * `vignette(all = TRUE)` = List **ALL** vignettes

### <a name='makeexamp'></a>Generating Examples ###

```R
# Ten random letters a-c
sprintf("x <- c('%s')", paste(letters[runif(10, min = 1, max = 4)],
                              collapse = "','"))
# Ten random integers 1-100
sprintf("x <- c(%s)", paste(as.integer(runif(10, min = 1, max = 101)),
                             collapse = ","))
```

## <a name='rinternals'></a>Internal R Stuff ##

* `match.fun( functionRequest )` = Helper method that pulls out a
  function based on polymorphic input. functionRequest can be a
  function, a symbol, or string, and R will try to locate and return
  the most appropriate function matching the request. This function is
  utilized by many other methods that take functions as input (eg
  `apply`)
* `as.environment()` = Used both to coerce and as a helper function
  when an argument requires an environment. Can take an environment
  object (returns it), a positive integer (takes from search() list),
  a character string (match by name) or -1 (calling environment).

## <a name='lexicalscoping'></a>Lexical Scoping ##

Still collecting thoughts here.

* `match.call( definition = myFunc )`
  * Returns a 'language' object of class 'call'
  * "definition" = a function, by default the container your calling within
  * "expand.dots" = Default TRUE, if `...` arguments should be expanded
  * `sys.call(  )` = TODO
* `eval( myExpression, envir = myEnv )` = Evaluates the provided
  expression in the specified environment
  * Expression can be a `call` or `expression` object, or a named
    object in the *current* scope.

## <a name='serialization'></a>Serialization ##

* `parse()` can be used to process a character string to an R
  'expression'. That expression can then be fed to `eval`, which
  will then execute the code represented by the initial text. So
  `eval( parse( text = "some R code" ) )` is the same as eval("some
  R code") in many other languages:
  
  ```R
  > myExp <- parse(text = "z <- 3")
  > myExp
  expression(z <- 3)
  > eval(myExp)
  > z
  [1] 3
  ```

* See also the [Data Import](#import) section above.

## <a name='wisdom'></a>Scott Wisdom ##

* <a name='masking'></a>Ariella pointed out that identically-named
  functions from one library will "overwrite" each other (the most
  recently loaded package wins).
  * R calls this "masking" and you can identify such situations with
    `conflicts()` to show the function names and `find()` to show the
    packages the function resides in. If it's a "gets" method, you may
    need to use `help()` explicitly (rather than `?`) to call up
    documentation.

    ```R
    > conflicts()
    [1] "body<-"    "kronecker"
    > find("body<-")
    [1] "package:methods" "package:base"
    > help("body<-", package = "methods")
    No documentation for ‘body<-’ in specified packages and libraries:
    you could try ‘??body<-’
    ```

  * Scott notes that the functions are not "overwritten"; All
    same-named functions still exist within the R session, and can all
    be accessed as long as you specify the package you want a particular from:

    ```R
    somePackage::someFunction( color = "lizzard" )
    get("someFunction", pos = "somePackage")( color = "lizzard" )
    ```

## <a name='weird'></a>Weird Things ##

#### <a name='aliases'></a>Aliases, Aliases, Aliases ####

R seems very fond of aliases. These are commands or variables that
have different names but do the same thing. It also "figures out what
you really want" in some cases. Here are the examples I've been able to
find:

* Mode `double` is the same as mode `numeric`
* `isTRUE(x)` is an abbreviation of `identical(TRUE, x)`
* When a function is on the left side, it's called the "replacement
  form", and even though you'd write it as `foo()`, it's really
  `"foo<-"()`:
  
  ```R
  y <- "Red"
  x <- "Blue"
  attr(y, "name") <- "Rachel"
  x <- "attr<-"(x, "name", "Barry")
  
  x
  [1] "Blue"
  attr(,"name")
  [1] "Barry"
   y
  [1] "Red"
  attr(,"name")
  [1] "Rachel"
  ```

#### <a name='dataclassweird'></a>data.class() vs class() ####

R documentation: *For compatibility reasons ...  When ‘x’ is ‘integer’,
the result of ‘data.class(x)’ is ‘"numeric"’ even when ‘x’ is
classed.*
     
```R
> x <- vector('integer', length = 10)
> inherits(x, what = 'integer')
[1] TRUE
> inherits(x, what = 'numeric')
[1] FALSE
> data.class(x)
[1] "numeric"
> class(x)
[1] "integer"
```

#### <a name='markdown'></a>Markdown Notes ####

Not R *per se*, but these have been useful in making this document...
* [Daring Fireball][daringfireball] - Original Markdown specification
* [Internal document anchors][NamedAnchors] - `<a
  name='anchorname'></a>` - You must use single quotes!
* [GitHub Markdown][githubmd] - Note that the "extra" features may not
  port to tools like knittr
  * Within code blocks you can use `` ```R `` to specify R
    [syntax highlighting][githubsyntax]. Unfortunately this
    [does not work](https://stackoverflow.com/a/25058886) with inline
    code blocks.
    * [Code blocks in lists][CodeBlockInList] are quite finicky.
      * The leading and trailing backticks need to be at the same
        indent as the list item content. If you don't do this, leading
        whitespace will be removed from each line.
      * Also, it seems like you need a newline before the first set of
        backticks, otherwise the code gets shoveled into an inline
        block. If you have newlines, they *must* be indented to the
        list, too!
        * If the indententing on a code-block-in-a-list is messed up,
          it will mess up list nesting at weird points later in the
          document. If you find
  * [HTML sanitization][GitHubSanitization] - You can use raw HTML
    tags, but GitHub will strip out many of the attributes for
    security. The link shows the WHITELIST structure used. Both
    attributes and tags are excluded. For example, a `style` attribute
    is not allowed, but `color` is. `span` tags are not allowed, but
    `b` is.
    * It is apparently
      [impossible to colorize text](https://stackoverflow.com/q/23904274)

[BigNumbersInR]: https://stackoverflow.com/questions/2053397/long-bigint-decimal-equivalent-datatype-in-r
[CranImportExport]: https://cran.r-project.org/doc/manuals/R-data.html
[NamedAnchors]: https://stackoverflow.com/questions/6695439/how-do-you-create-link-to-a-named-anchor-in-multimarkdown
[SObignumbers]: https://stackoverflow.com/questions/17724382/display-exact-value-of-a-variable-in-r
[WickhamDataStructures]: http://adv-r.had.co.nz/Data-structures.html
[daringfireball]: https://daringfireball.net/projects/markdown/syntax
[githubmd]: https://help.github.com/articles/github-flavored-markdown/
[githubsyntax]: https://help.github.com/articles/github-flavored-markdown/#syntax-highlighting
[CodeBlockInList]: https://stackoverflow.com/questions/6235995/markdown-github-syntax-highlighting-of-code-block-as-a-child-of-a-list
[GitHubSanitization]: https://github.com/jch/html-pipeline/blob/master/lib/html/pipeline/sanitization_filter.rb
[RScopeSearching]: http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/
[RScopeMap]: http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/#map-of-the-world-follow-the-purple-line-road
[RstudioEmacs]: https://support.rstudio.com/hc/communities/public/questions/200757977-Emacs-key-bindings-again-
[ESS]: http://ess.r-project.org/
[Rinferno]: http://www.burns-stat.com/pages/Tutor/R_inferno.pdf
[AdvancedR]: http://adv-r.had.co.nz/
[s4bioconductor]: https://stackoverflow.com/questions/3602154/when-does-it-pay-off-to-use-s4-methods-in-r-programming
[S]: https://en.wikipedia.org/wiki/S_%28programming_language%29
[sHistory]: http://ect.bell-labs.com/sl/S/history.html
[GoogleStyle]: http://google-styleguide.googlecode.com/svn/trunk/Rguide.xml\
[RCC]: http://www.aroma-project.org/developers/RCC
[SeS4slots]: https://stackoverflow.com/a/4714080
[SeUsefulLinks]: https://stackoverflow.com/questions/4143611/sources-on-s4-objects-methods-and-programming-in-r
[WpDensityFunc]: https://en.wikipedia.org/wiki/Probability_density_function
[WpCumulativeFunc]: https://en.wikipedia.org/wiki/Cumulative_distribution_function
[WpQuantileFunc]: https://en.wikipedia.org/wiki/Quantile_function
[WpUniformDist]: https://en.wikipedia.org/wiki/Uniform_distribution_%28continuous%29
[WpNormalDist]: https://en.wikipedia.org/wiki/Normal_distribution
[WpPoissonDist]: https://en.wikipedia.org/wiki/Poisson_distribution
[WpBinomialDist]: https://en.wikipedia.org/wiki/Binomial_distribution
[WpGammalDist]: https://en.wikipedia.org/wiki/Gamma_function
[WpExponentialDist]: https://en.wikipedia.org/wiki/Exponential_function
[WpMersenneTwister]: https://en.wikipedia.org/wiki/Mersenne_Twister
[WpPRNG]: https://en.wikipedia.org/wiki/Pseudorandom_number_generator
[ReadCsvForcedChar]: https://stackoverflow.com/questions/6616020/problem-with-specifying-colclasses-in-read-csv-in-r/6616047#6616047
[xmlInR]: http://www.stat.berkeley.edu/%7Estatcur/Workshop2/Presentations/XML.pdf
[jsonlite]: http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/
[datatable]: https://www.datacamp.com/courses/data-analysis-the-data-table-way
[datatablegithub]: https://github.com/Rdatatable/data.table
[dfVSdt]: https://stackoverflow.com/questions/13618488/what-you-can-do-with-data-frame-that-you-cant-in-data-table
[advmanip]: https://github.com/raphg/Biostat-578/blob/master/Advanced_data_manipulation.Rmd
[rodbcDebian]: https://superuser.com/questions/283272/problem-with-rodbc-installation-in-ubuntu
[mysqlDebian]: https://superuser.com/questions/283272/problem-with-rodbc-installation-in-ubuntu
[OdbcWP]: https://en.wikipedia.org/wiki/Open_Database_Connectivity
[SqliteWP]: https://en.wikipedia.org/wiki/SQLite
[SqlPlaceholders]: https://stackoverflow.com/questions/2186015/bind-variables-in-r-dbi
[ButtreyApply]: http://faculty.nps.edu/sebuttre/home/R/apply.html
[NobelGraphics]: http://rpubs.com/neilfws/118756
[rblogmelt]: http://www.r-bloggers.com/melt/
[reshape2github]: https://github.com/hadley/reshape/blob/master/README.md
[pcre]: http://www.pcre.org/
[posixre]: http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap09.html
[atomicstr]: https://stackoverflow.com/questions/5809017/what-is-the-second-column-of-str-report-in-r-and-what-does-atomic-in-this-co
[codepoint]: https://en.wikipedia.org/wiki/Code_point
[charencode]: https://en.wikipedia.org/wiki/Character_encoding
[lubridatetutorial]: http://www.r-statistics.com/2012/03/do-more-with-dates-and-times-in-r-with-lubridate-1-1-0/
[pchLookup]: https://gist.github.com/maptracker/e23b3f3712065677ba24
[rgraphgallery]: http://gallery.r-enthusiasts.com/
[showsettings]: http://www.magesblog.com/2012/12/changing-colours-and-legends-in-lattice.html
[npslattice]: https://science.nature.nps.gov/im/datamgmt/statistics/r/graphics/lattice.cfm
[gotchas]: https://github.com/maptracker/GeneralUtility/blob/master/GotchasInR.md
[Taxicabgeometry]: https://en.wikipedia.org/wiki/Taxicab_geometry
[Euclidean_distance]: https://en.wikipedia.org/wiki/Euclidean_distance
[Canberra_distance]: https://en.wikipedia.org/wiki/Canberra_distance
[Minkowski_distance]: https://en.wikipedia.org/wiki/Minkowski_distance
[ElStatLearn]: http://statweb.stanford.edu/~tibs/ElemStatLearn/download.html
