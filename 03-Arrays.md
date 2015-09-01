3. Arrays
=========
Scala supports 2 types of arrays:
- `Array` for fixed length arrays
- `ArrayBuffer` for variable length arrays


*Array* - Fixed Length Array
----------------------------
Inside the JVM, a Scala Array is implemented as a Java array. An array of Int, Double, or another equivalent of the Java
primitive types is implemented as a primitive type array in the JVM:
```Scala
val n = new Array[Int](10)             // An array of ten integers, all initialized with zero
val a = new Array[String](10)          // A string array with ten elements, all initialized with null
val s = Array("Hello", "World")        // An Array[String] of length 2—the type is inferred
                                       // Note: No new when you supply initial values
```

Use () instead of [] to access elements
```Scala
val s = Array("Hello", "World")        // s: Array[String] = Array(Hello, World)
s(0)                                   // res0: String = Hello
```

*ArrayBuffer* - Variable Length Array
-------------------------------------
The Scala `ArrayBuffer` is equivlent to a Java `ArrayList` and is used for arrays that grow and shrink on demand. Adding 
or removing elements at the end of an array buffer is an efficient (“amortized constant time”) operation.
```Scala
import scala.collection.mutable.ArrayBuffer
val b  = ArrayBuffer[Int]()             // An empty array buffer, ready to hold integers
val b2 = new ArrayBuffer[Int]           // Same as above
b += 1                                  // ArrayBuffer(1)                        += adds element(s) at the end
b += (1, 2, 3, 5)                       // ArrayBuffer(1, 1, 2, 3, 5)
b ++= Array(8, 13, 21)                  // ArrayBuffer(1, 1, 2, 3, 5, 8, 13, 21) ++= adds any collection to the end
b.trimEnd(5)                            // res0: Unit=()                         removes last 5 elements: ArrayBuffer(1, 1, 2)
```

Inserting elements into an arbitrary position in the array is less efficient because the following elements need to be 
shifted up:
```Scala
b.insert(2, 6)                          // res0: Unit=()    insert before index 2:    ArrayBuffer(1, 1, 6, 2)
b.insert(2, 7, 8, 9)                    // res1: Unit=()    insert multiple elements: ArrayBuffer(1, 1, 7, 8, 9, 6, 2)
b.remove(2)                             // res2: Int=7      remove 1 element:         ArrayBuffer(1, 1, 8, 9, 6, 2)
b.remove(2, 3)                          // res3: Unit=()    remove 3 elements:        ArrayBuffer(1, 1, 2)
```

To convert between Arrays and ArrayBuffers:
```Scala
val a = Array(1,2,3)                    // a: Array[Int] = Array(1, 2, 3)                       
val b = ArrayBuffer(4,5,6)              // c: ArrayBuffer[Int] = ArrayBuffer(4, 5, 6)
a.toBuffer                              // res0: Buffer[Int] = ArrayBuffer(1, 2, 3)
b.toArray                               // res1: Array[Int] = Array(4, 5, 6)
```


Traversing Arrays and Array Buffers
-----------------------------------
To traverse an array or array buffer with a for loop:
```Scala
val myArray = Array(1,2,3)

for (e <- myArray)                     // traverse each element
  println(e)

for (i <- 0 until a.length)            // traverse each element with an index
  println(i + ": " + a(i))

for (i <- 0 until (a.length, 2))       // traverse every second element
  println(i + ": " + a(i))

for (i <- (0 until a.length).reverse)  // traverse each element starting at the end of the array
  println(i + ": " + a(i))
```


Transforming Arrays
-------------------
Arrays can be transformed into *new* collections of the same type or a compatible type using a *for comprehension* as 
previously seen in [Control Structures and Functions](02-ControlStructures.md):
```Scala
val d = Array(2, 3, 5, 7, 11)                    // d: Array[Int] = Array(2, 3, 5, 7, 11)
for (elem <- d) yield 2 * elem                   // res0: Array[Int] = Array(4, 6, 10, 14, 22)
for (elem <- d if elem % 2 == 0) yield 2 * elem  // res1: Array[Int] = Array(4)
```


Array Functions
---------------
The methods for the `Array` class are listed under `ArrayOps` as arrays are converted to `ArrayOp` objects before any of
the operations are applied. Some common methods:
```Scala
val e = Array(1, 7, 2, 9)                        // e: Array[Int] = Array(1, 7, 2, 9)
val f = ArrayBuffer(1, 7, 2, 9)                  // f: ArrayBuffer[Int] = ArrayBuffer(1, 7, 2, 9)
e.sum                                            // res0: Int = 19
f.sum                                            // res1: Int = 19
e.max                                            // res2: Int = 9
e.sorted                                         // res3: Array[Int] = Array(1, 2, 7, 9)                 e is unchanged
e.sortWith(_ > _)                                // res4: Array[Int] = Array(9, 7, 2, 1)                 e is unchanged
scala.util.Sorting.quickSort(e)                  // res5: Unit = ()            e is sorted in place to Array(1, 2, 7, 9) 
e.mkString(" and ")                              // res6: String = 1 and 2 and 7 and 9                                                    
e.mkString("<", ",", ">")                        // res7: String = <1,2,7,9>
e.toString                                       // res8: String = [I@276ed499               Useless java Array toString
f.toString()                                     // res9: String = ArrayBuffer(1, 7, 2, 9)   Scala ArrayBuffer toString 
```


Multidimensional Arrays
-----------------------
To create a multidimensional array use the `Array.ofDim` method:
```Scala
val matrix = Array.ofDim[Int](2, 2)              // matrix: Array[Array[Int]] = Array(Array(0, 0), Array(0, 0))
matrix(0)(0) = 1                                 // res0: Array[Array[Int]] = Array(Array(1, 0), Array(0, 0))
```

It is possible to make ragged arrays, with varying row lengths:
```Scala
val ragged = new Array[Array[Int]](2)            // ragged: Array[Array[Int]] = Array(null, null)
ragged(0) = new Array[Int](2)                    // ragged: Array[Array[Int]] = Array(Array(0, 0), null)
ragged(1) = new Array[Int](3)                    // ragged: Array[Array[Int]] = Array(Array(0, 0), Array(0, 0, 0))
```

Converting between Scala ArrayBuffers and Java Arrays
-----------------------------------------------------
To pass a Scala `ArrayBuffer` to a method expecting a Java array, import the implicit conversion methods in 
`scala.collection.JavaConversions`. Then you can use Scala buffers in your code, and they automatically get wrapped into
Java lists when calling a Java method:
```Scala
import scala.collection.JavaConversions.bufferAsJavaList
import scala.collection.mutable.ArrayBuffer
val command = ArrayBuffer("ls", "-al", "/home/cay")
val pb = new ProcessBuilder(command)             // Scala to Java
```

Conversely, when a Java method returns a `java.util.List`, you can have it automatically converted into a `Buffer` 
(you can't use an `ArrayBuffer` as the wrapped object is only guaranteed to be a `Buffer`):
```Scala
import scala.collection.JavaConversions.asScalaBuffer
import scala.collection.mutable.Buffer
val cmd: Buffer[String] = pb.command()           // Java to Scala
```

