Scala
=====

[Scala](http://www.scala-lang.org/) notes and code.


Style Guide
-----------
http://docs.scala-lang.org/style



REPL
----
The Scala interpretor reads an expression, compiles it to bytecode, then executes the bytecode using the JVM. This is called the *read-evaluate-print* loop, or REPL.

*Tip* - to paste a block of code into the REPL:
```no-highlight
1. Type *:paste*
2. Paste in the code block
3. Type *Ctrl+K*
```

Variables
---------
```Scala
val xmax = 100        // constant (type is inferred)
val xmax: Int = 100   // constant of type String
var xmax = 100        // mutable value (type is inferred)    
val xmax, ymax = 100  // declare multiple values
var x, y = 0          // declare multiple variables
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
    
Operator Overloading
--------------------
Method calls can be written as:
```Scala
a method b
```

which is shorthand for:
```Scala
a.method(b)
```

Operators in Scala are actually methods:
```Scala
a.+(b)
```

which can be written as:
```Scala
a + b
```
i.e. Scala supports operator overloading. Thus we can use mathematcial operators with `BigDecimal` and `BigInt`:
```Scala
val x: BigInt = 1234567890
val cube = x * x * x
```

Static Methods
--------------
Scala does not have static methods, instead it has *singleton objects*. This allows classes to have a *companion object* whose methods act like static methods in Java, e.g.:
```Scala
BigInt.probablePrime(100, scala.util.Random)
```    
`probablePrime` is a method on the `BigInt` companion object to the `BigInt` class.       


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

