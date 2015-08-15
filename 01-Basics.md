1. The Basics
=============


Variables
---------
```Scala
val xmax = 100             // constant (type is inferred)
val xmax: Int = 100        // constant of type Int
var xmax = 100             // mutable value (type is inferred)    
val xmax, ymax = 100       // declare multiple values (type is inferred)
val xmax, ymax: Int = 100  // declare multiple values of type Int
var x, y = 0               // declare multiple variables (type is inferred)
```


Lazy Vals
---------
When a *val* is declared as *lazy*, its initialization is deferred until it is accessed for the first time or never if 
the val is never accessed. Lazy values are useful to delay costly initialization statements:
```Scala
val words = scala.io.Source.fromFile("/usr/share/dict/words").mkString         // Evaluated as soon as words is defined
lazy val words = scala.io.Source.fromFile("/usr/share/dict/words").mkString    // Evaluated the first time words is used
def words = scala.io.Source.fromFile("/usr/share/dict/words").mkString         // Evaluated every time words is used
```

Note - there is a small cost associated with lazy values. Every time a lazy value is accessed, a method is called that 
checks, in a threadsafe manner, whether the value has already been initialized.
                        
                                                           
Semicolons
----------
Semicolons are only required for terminating statements when they appear on the same line:
```Scala
if (x > 0) y3 = x; z3 = x * x;
if (x > 0) { y3 = x; z3 = x * x }  // no semicolon required for 2nd statement due to the }
```


Line Continuation
-----------------
Long statements can be continued over multiple lines as long as the lines end in a symbol that *cannot* be the end of the statement,
e.g. ending on an operator:
```Scala 
val sum = 3 +
          2 + 
          5          
```

e.g. using curly braces:
```Scala
if (x > 0) {
  y3 = x
  z3 = x * x
}
```


Data Types
----------
Scala data types are implemented as *classes* instead of primitive types:

    Numeric - Byte, Char, Short, Int, Long, Float, Double 
    Boolean - Boolean
    
The basic data types are augmented with functionality from additional Scala classes, e.g.:

    String - StringOps    
    Int    - RichInt
    Double - RichDouble
    Char   - RichChar
    etc.
            
This allows additional operations to be performed than are available on the the base java class, e.g.:
```Scala    
"abc".intersect("cde")   // returns "c"
1.to(10)                 // 1 is converted to a RichInt containing the to() method
```    


Converting Between Data Types
-----------------------------
Scala uses methods, not *casts*, to convert between numeric types, e.g.:
```Scala    
99.44.toInt              // is 99
99.toChar                // is 'c'
```


Method Calls
------------
Method calls can be written as:
```Scala
a method b
```

which is shorthand for:
```Scala
a.method(b)
```

Methods that take no arguments and do not modify the object are called without parenthesis:
```Scala
"aaabc".distinct         // Returns "abc" 
```


The *apply* Method
------------------
The Scala compiler converts the shorthand
```Scala
object()
```    
into
```Scala
object.apply()
```
as a special syntax case.

```Scala
BigInt("1234567890")
```
is a shortcut for
```Scala
BigInt.apply("1234567890")
```

Note - this creates a new `BigInt` object, without having to use the `new` keyword. Using the `apply` method of a companion
object is a common Scala idiom for constructing objects. For example, `Array(1, 4, 9, 16)` returns an array, using the
`apply` method of the `Array` companion object.


Operator Overloading
--------------------
Operators in Scala are actually methods:
```Scala
a.+(b)
```

which can be written as:
```Scala
a + b
```
i.e. Scala supports operator overloading. Thus we can use mathematical operators with `BigDecimal` and `BigInt`:

```Scala
val x: BigInt = 1234567890
val cube = x * x * x
```


Importing packages
------------------
When importing packages from the Scala library, the `scala` prefix can be omitted:
```Scala
import scala.math._      // The _ character is a wildcard, like * in Java
import math._            // Equivalent to import scala.math._ 
```


Static Methods
--------------
Scala does not have static methods, instead it has *singleton objects*. This allows classes to have a *companion object* whose methods act like static methods in Java, e.g.:
```Scala
BigInt.probablePrime(100, scala.util.Random)
```    
`probablePrime` is a method on the `BigInt` companion object to the `BigInt` class.       


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


Semicolons
----------
Semicolons are only required for terminating statements when they appear on the same line:
```Scala
if (x > 0) y3 = x; z3 = x * x;
if (x > 0) { y3 = x; z3 = x * x }  // no semicolon required for 2nd statement due to the }
```


Line Continuation
-----------------
Long statements can be continued over multiple lines as long as the lines end in a symbol that *cannot* be the end of the statement,
e.g. ending on an operator:
```Scala 
val sum = 3 +
          2 + 
          5          
```
e.g. using curly braces:
```Scala
if (x > 0) {
  y3 = x
  z3 = x * x
}
```


Exceptions
----------
Scala exceptions are not *checked* i.e. do not have to be caught or rethrown. To be thrown, the exception must be a subclass 
of `java.lang.Throwable`. The `throw` expression has the special type `Nothing` which means when used in `if/else`
expressions, the expression type is that of the other branch. In the example below the expression type is `Double`:
 ```Scala
 if (x >= 0)
   sqrt(x)
 else 
   throw new IllegalArgumentException("x should not be negative")
 ```

Catching exceptions uses pattern matching syntax:
```Scala
val url = new URL("http://hostname.com/does_not_exist.gif")
try {
   process(url)
} catch {
   case _: MalformedURLException => println("Bad URL: " + url)
   case ex: IOException => ex.printStackTrace()
}
```
`_` can be used for the exception variable if it is not used
 