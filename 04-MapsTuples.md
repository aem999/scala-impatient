4. Maps and Tuples
==================
A *Tuple* is an *immutable* sequence of heterogeneous values. In Scala *tuples* are surrounded by brackets and their 
components can be accessed by _1, _2, etc.:
```Scala
val t1 = new Tuple3(1, 3.14, "Fred")             // t1: (Int, Double, String) = (1,3.14,Fred)
val t2 = Tuple3(1, 3.14, "Fred")                 // Using the companion object instead of the constructor
val t3 = (1, 3.14, "Fred")                       // Syntactic sugar for new Tuple3(1, 3.14, "Fred")
t1._1                                            // res0: Int = 1
t1 _1                                            // res1: Int = 1
t1._2                                            // res2: Double = 3.14
t1._3                                            // res3: String = Fred
```

Pattern matching can be used to access tuple elements and \_ can be used if not all components are needed:
```Scala
val (id, username, _) = (0, "Fred", 36)          // id: Int = 0; username: String = Fred 
id                                               // res1: Int = 0
username                                         // res2: String = Fred
```

*Tuples* are useful for grouping together related values so that they can be processed together e.g. returning more than
one value from a function:
```Scala
"abcDEF".partition(_.isUpper)                    // res0: (String, String) = (DEF,abc)
val pairs = Array("a","b","c").zip(Array(1,2,3)) // pairs: Array[(String, Int)] = Array((a,1), (b,2), (c,3))
val map = pairs.toMap                            // map: Map[String,Int] = Map(a -> 1, b -> 2, c -> 3)
```


Maps
----
Maps are collections of key/value *pairs* (i.e. Tuple2 objects):
```Scala
val scores1 = Map("Alice" -> 10, "Bob" -> 3)                              // immutable map
val scores2 = Map(("Alice", 10), ("Bob", 3))                              // -> creates a pair (tuple2)
val scores3 = scala.collection.mutable.Map("Alice" -> 10, "Bob" -> 3)     // mutable map
val scores4 = scala.collection.mutable.Map[String, Int]()                 // empty mutable map
```


Accessing Maps
--------------
Use `()`, `get()` or `getOrElse()`:
```Scala
val bobsScore = scores1("Bob")                   // bobsScore: Int = 3
val error = scores1("Fred")                      // java.util.NoSuchElementException: key not found: Fred
val FredsScore = scores1.getOrElse("Fred", 0)    // FredsScore: Int = 0
val bobsScore2 = scores1.get("Bob")              // bobsScore2: Option[Int] = Some(3)
val FredsScore2 = scores1.get("Fred")            // FredsScore2: Option[Int] = None
```


Updating Maps
-------------
*Mutable* maps can be updated:
```Scala
scores3("Bob") = 10                              // res0: Unit = ()        Updates the existing value for the key "Bob"
scores3("Fred") = 4                              // res1: Unit = ()        Adds a new key/value pair
scores3 += ("Bob" -> 12, "Fred" -> 7)            // res2: mutable.Map[String,Int] = Map(Bob -> 12, Fred -> 7, Alice -> 10)
scores3 -= "Alice"                               // res3: mutable.Map[String,Int] = Map(Bob -> 12, Fred -> 7)
```

*Immutable* maps cannot be updated but operations can be applied to them to create new maps. If the immutable map is
assigned to a `var`, then the `var` can be updated instead of creating a new value:
```Scala
var newScores = scores1 + ("Bob" -> 10, "Fred" -> 7) // immutable.Map[String,Int] = Map(Alice -> 10, Bob -> 10, Fred -> 7)
newScores = newScores + ("Bob" -> 2, "Fred" -> 3)    // immutable.Map[String,Int] = Map(Alice -> 10, Bob -> 2, Fred -> 3)
newScores = newScores - "Fred"                       // immutable.Map[String,Int] = Map(Alice -> 10, Bob -> 2)                                                        
```

It may apear to be inefficient to keep constructing new maps, but that is not the case. Because the old and new maps are
immutable, they can share most of their structure.


Iterating over Maps
-------------------
Use pattern matching in a for loop to iterate over all the key/value pairs of a map:
```Scala
for ((k, v) <- map)                              // process key and value
for ((k, v) <- map) yield (v, k)                 // reverse key and value
```

To visit just the keys or values, use the `keys` and `values` methods, as you would in Java. These methods return
an Iterable that can be used in a for loop:
```Scala
for (v <- newScores.keys) println(v)            // res0: Unit = ()         Prints all keys
for (v <- newScores.values) println(v)          // res1: Unit = ()         Prints all values
```


Sorted Maps
-----------
By default, Scala gives you a hash table when requesting a Map. If the map needs to be sorted e.g. to visit the keys in 
sorted order, you can request a tree map or to visit the keys in insertion order use a `LinkedHashMap`:
```Scala
val sortedMap = scala.collection.immutable.SortedMap("Alice" -> 10, "Fred" -> 7, "Bob" -> 3)
val linkedMap = scala.collection.mutable.LinkedHashMap("Alice" -> 10, "Fred" -> 7, "Bob" -> 3)
//
for (v <- sortedMap.keys) println(v)             // Alice Bob Fred
for (v <- linkedMap.keys) println(v)             // Alice Fred Bob
```

Note - there is (as of Scala 2.9) no mutable tree map. Instead adapt a Java `TreeMap`.



Converting between Scala and Java Maps
--------------------------------------
To convert from a Java Map to a Scala Map:
```Scala
import scala.collection.JavaConversions.mapAsScalaMap
val scalaMap: scala.collection.mutable.Map[String, Int] = javaMap
```

To convert from a Scala Map to a Java map:
```Scala
import scala.collection.JavaConversions.mapAsJavaMap
val font = new java.awt.Font(scalaMap)           // Expects a Java map
```

To convert from `java.util.Properties` to a Scala Map[String,String]:
```Scala
import scala.collection.JavaConversions.propertiesAsScalaMap
val props: scala.collection.Map[String, String] = System.getProperties()
```
