6. Objects
==========
Scala does not support *static methods*. Instead, the `object` construct in Scala is used to provide a class with a 
single instance, or to find a home for miscellaneous values or functions. A Scala `object` has essentially all the 
features of a class, it can even extend other classes or traits, however there is one exception - you cannot provide 
constructor parameters. Scala `objects` are constructed *lazily* i.e. on first use or not at all if never used.


Singletons
----------
Scala ensures that only one `object` instance will be constructed:
```Scala
object Accounts {
  private var lastNumber = 0
  def newUniqueNumber() = { lastNumber += 1; lastNumber }
}
val nextNumber = Accounts.newUniqueNumber()
```

Calling `Accounts.newUniqueNumber()` for the first time causes the `Accounts` constructor to be called.


Companion Objects
-----------------
A *companion object*  is a singleton object that shares the same name with a class defined in the same source file. 
*Companion objects* and classes have access to each other’s private members. In addition, any implicit conversions 
defined in the companion object will be in scope anywhere the class is used.
```Scala
class Account {
  val id = Account.newUniqueNumber()
  private var balance = 0.0
  def deposit(amount: Double) { balance += amount }
 ...
}
object Account {                                 // The companion object
  private var lastNumber = 0
  private def newUniqueNumber() = { lastNumber += 1; lastNumber }
}
```


Objects Extending a Class or Trait
----------------------------------
An `object` can extend a `class` and/or one or more `traits`. The result is an object of a class that extends the 
given class and/or traits, and in addition has all of the features specified in the object definition. This is useful 
for specifying default objects that can be shared:
```Scala
// a class for undoable actions in a program
abstract class UndoableAction(val description: String) {
  def undo(): Unit
  def redo(): Unit
}
// “do nothing” action is a useful default
object DoNothingAction extends UndoableAction("Do nothing") {
  override def undo() {}
  override def redo() {}
}
```

The `DoNothingAction` object can be shared across all places that need this default:
```Scala
val actions = Map("open" -> DoNothingAction, "save" -> DoNothingAction, ...)
 ```
 
 
The *apply* method
------------------
Placing an `apply` method on the companion object is a very convenient way of creating instance of the companion class:
```Scala
class Account private (val id: Int, initialBalance: Double) {
  private var balance = initialBalance
  ...
}
object Account {                                 // The companion object
  def apply(initialBalance: Double) =
  new Account(newUniqueNumber(), initialBalance)
  ...
}
```

Now you an account can be constructed as:
```Scala
val account = Account(1000.0)
```

Note the following can easily be confused:
```Scala
val a = Array(100)                // calls apply(100), yielding an Array[Int] with a single element 100
val b = new Array(100)            // calls the constructor this(100), yielding an Array[Nothing] with 100 null elements
```


Application object
------------------
Scala programs start with an object’s main method of type `Array[String] => Unit`:
```Scala
object Hello {
  def main(args: Array[String]) {
    println("Hello, World!")
  }
}
```

Alternatively, instead of providing a main method for the application, you can extend the `App` trait and place the 
program code into the constructor body. The `App` trait extends another trait, `DelayedInit`, that gets special handling 
from the compiler. All initialization code of a class with that trait is moved into a delayedInit method. The main of 
the App trait method captures the command-line arguments, calls the `delayedInit` method, and optionally prints the 
elapsed time.
```Scala
object Hello extends App {
  if (args.length > 0)
    println("Hello, " + args(0))                 // args property contains the command line arguments
  else
    println("Hello, World!")
}
```

To print the elapsed program time:
```Shell
$ scalac Hello.scala
$ scala -Dscala.time Hello Fred
Hello, Fred
[total 4ms]
```


Enumerations
------------
Scala does not have enumerated types, it provides an Enumeration helper class that can be used to produce enumerations. 
Define an object that extends the `Enumeration` class and initialize each value in your with a call to the `Value` 
method:
```Scala
object TrafficLightColor extends Enumeration {
  val Red, Yellow, Green = Value
}
//
val t = TrafficLightColor.Red                    // t: TrafficLightColor.Value = Red
```

Each call to the `Value` method returns a new instance of an inner class, also called `Value`. Alternatively, you can 
pass `id`s, `name`s, or both to the `Value` method:
```Scala
object TrafficLightColor extends Enumeration {
  val Red = Value(0, "Stop")
  val Amber = Value(10)                         // name will default to Amber
  val Green = Value("Go")                       // id defaults to 11 (previous one plus 1 or zero)
}
//
TrafficLightColor                               // 
TrafficLightColor.Red                           // res0: TrafficLightColor.Value = Stop
TrafficLightColor.Red.id                        // res1: Int = 0
TrafficLightColor.Amber                         // res2: TrafficLightColor.Value = Amber
TrafficLightColor.Green                         // res3: TrafficLightColor.Value = Go
TrafficLightColor(0)                            // res0: TrafficLightColor.Value = Red      Calls Enumeration.apply
TrafficLightColor.withName("Red")               // res1: TrafficLightColor.Value = Red
//
for (c <- TrafficLightColor2.values) println(s"${c.id}: $c")  // 0: Stop
                                                              // 10: Amber
                                                              // 11: Go
```

You can add a *type alias* to change the type of the enumeration from `Value` to `TrafficLightColor`:
```Scala
object TrafficLightColor2 extends Enumeration {
  type TrafficLightColor2 = Value
  val Red, Yellow, Green = Value
}
```

This then allows a less verbose syntax:
```Scala
import TrafficLightColor2._
val t1: TrafficLightColor.Value = TrafficLightColor.Red
val t2: TrafficLightColor2 = Red
```