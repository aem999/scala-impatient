Scala
=====

[Scala](http://www.scala-lang.org/) notes and code.


Style Guide
-----------
http://docs.scala-lang.org/style



REPL
----
The Scala interpretor reads an expression, compiles it to bytecode, then executes the bytecode using the JVM. This is called the *read-evaluate-print* loop, or REPL.


Variables
---------
    val xmax = 100        // constant (type is inferred)
    val xmax: Int = 100   // constant of type String
    var xmax = 100        // mutable value (type is inferred)    
    val xmax, ymax = 100  // declare multiple values
    var x, y = 0          // declare multiple variables


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
    
    "abc".interesect("cde")  // returns "c"
    1.to(10)                 // 1 is converted to a RichInt containing the to() method
