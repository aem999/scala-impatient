2. Control Structures and Functions
===================================

Conditional Expressions
-----------------------
Scala *if/else* expressions have a value which can be assigned to a variable:
```Scala
val largest = if (x > y) x else y
```
    
this is equivalent to:
```Scala
var largest
if (x > y) largest = x else largest = y
```
    
However, the former is better because it can be used to initialise a *val* whereas the latter needs to use a variable.


*Unit* Type
-----------
A data type that only holds one value () which means "no value". The *Unit* type is a subtype of *scala.AnyVal*.
Scala methods with return type *Unit* are analogous to java methods declared *void*:
```Scala
val c = if (x > 1) 1
```

is equivalent to:
```Scala 
val c = if (x > 1) 1 else ()
```


Block Expressions
-----------------
Java -  a block contains a sequence of *statements* enclosed in {}
Scala - a block contains a sequence of *expressions* enclose in {}, and the result is also an expression. The value of 
        the block is the value of the last expression or a *Unit* value () if the last expression is an assignment:
```Scala 
val a = {1 + 3}          // a: Int = 4
val b = {c = 5}          // b: Unit = ()
```
        
*while* Loop
----------
```Scala 
while (i > 0) {
  n += 1
}
```

*for* Loop
----------
The Java `for (initialize; test; update)` loop does nor exist in Scala, instead Scala uses a *generator* of the form 
`variable <- expression`:
```Scala 
for (i <- 1 to s.length) {                  // Equivalent to RichInt 1.to(s.length)
  sum += s(i-1)
}
```

or to stop at s.length - 1 
```Scala 
for (i <- 0 until s.length) {               // Equivalent to RichInt 1.until(s.length)
  sum += s(i)
}
```

`i` traverses all the values of the expression to the right of the `<-` and `i` will be of the same type as the collection.
A more succinct solution is:
```Scala 
for (c <- s)
  sum += c
```

Scala `for` loops support multiple *generators* of the form `variable <- expression` separated by semicolons:
```Scala 
for (i <- 1 to 3; j <- 1 to 3) 
  print((10 * i + j) + " ")                // Prints 11 12 13 21 22 23 31 32 33 (equivalent to a nested loop)
```

Each *generator* can have a *guard* (a `Boolean` condition preceded by `if`):
```Scala 
for (i <- 1 to 3; j <- 1 to 3 if i != j)    // Note - no semicolon before the if
  print((10 * i + j) + " ")                 // Prints 12 13 21 23 31 32
``` 

Braces and newlines can be used instead of brackets and semicolons:
```Scala 
for { i <- 1 to 3
      j <- 1 to 3 }
  printf("%d%d ", i, j)                     // Prints 11 12 13 21 22 23 31 32 33
```

*for* Comprehensions
-----------------------
A *for comprehensions* is a *for expression* that creates a new collection by using a `yield` clause to create an element 
of the new collection on each iteration. A *for comprehension* has the form `for (enums) yield e`, where enums refers to
a semicolon-separated list of *enumerators*. An *enumerator* is either a *generator* which introduces new variables, or 
it is a *filter*:
```Scala 
for (i <- 1 to 10) 
  yield i % 3       // res0: scala.collection.immutable.IndexedSeq[Int] = Vector(1, 2, 0, 1, 2, 0, 1, 2, 0, 1)
```  
```Scala
val d = Array(2, 3, 5, 7, 11)
for (elem <- d) yield 2 * elem                   // Array(4, 6, 10, 14, 22)
for (elem <- d if elem % 2 == 0) yield 2 * elem  // Array(4)
```

The Scala compiler actually rewrites the *for* comprehensions as *map* and *filter* operations. Using the command:
```Scala
scalac -Xprint:typer <filename>.scala:
```

you can see that the above *for comprehensions* are rewritten by the compiler as (simplified):
```Scala
d.map(2 * _)                                     // Array(4, 6, 10, 14, 22)
d.filter(_ % 2 == 0).map(2 * _)                  // Array(4)
```

 
Breaking out of a loop
----------------------
Scala provides no mechanism for breaking out of a loop. Instead:
- use nested functions and exit early
- use boolean variables to conditionally apply loop body (not recommended, not very functional)
- use the `break` method in the `Breaks` object to create a breakable block (not recommended - throws/catches an exception)


Functions
---------
*Functions* are *methods* that do not operate on an object:
```Scala
def abs(x: Double) = if (x >= 0) x else -x
```
The types of all the parameters must be specified but the return type can be omitted if the function is non recursive and
it will be inferred by the compiler. The last expression of the function body is returned.

Note - the `return` keyword is used in Scala to break out of a function early *not* to return a value as in Java.


Procedures
----------
*Procedures* are functions that return no value. A function body enclosed in braces without a preceding = symbol will 
have a `Unit` return type and thus will be a procedure which is only called for it's side effect:
```Scalas
def abs(x: Double) = {if (x >= 0) x else -x}        // abs: abs[](val x: Double) => Double (function)               
def abs(x: Double) {if (x >= 0) x else -x}          // abs: abs[](val x: Double) => Unit (procedure)
def abs(x: Double): Unit = {if (x >= 0) x else -x}  // abs: abs[](val x: Double) => Unit (explicit procedure)
```
Note - forgetting to use = is a big gotcha and a common source of 'Unit is not acceptable at this location' errors!


Default Arguments
-----------------
Functions can have *default arguments* that are used when explicit values are not specified:
```Scala
def decorate(str: String, left: String = "[", right: String = "]") =
  left + str + right
decorate("Hello")                                // res0: String = [Hello]
decorate("Hello", "<<<", ">>>")                  // res1: String = <<<Hello>>>  
```


Named Arguments
---------------
*Named* arguments can make a function call more readable. They are also useful if a function has many default parameters:
```Scala
def decorate(str: String, left: String = "[", right: String = "]") =
  left + str + right
decorate(left="<<<", str="Hello", right=">>>")   // res0: String = <<<Hello>>>
decorate("Hello", right=">>>")                   // res1: String = [Hello>>>
```


Variable Arguments
------------------
Functions can take a variable number of arguments:
```Scala
def sum(args: Int*) = {
  var result = 0
  for (arg <- args)
    result += arg
  result
}
val s = sum(1, 4, 9)                             // s: Int = 14
```

The arguments are passed as a single argument of type `Seq` (a sequence). If the argument is already a sequence then the
call will fail. Instead the argument must be converted to an *argument sequence* via a \_\* type annotation:
```Scala
val s = sum(1 to 5)                              // Error
val s = sum(1 to 5: _*)                          // Consider 1 to 5 as an argument sequence
def recursiveSum(args: Int*) : Int = {           // Recursive function with variable number of arguments
 if (args.length == 0) 0
 else args.head + recursiveSum(args.tail : _*)   // Tail must be passed as an argument sequence
}
```
