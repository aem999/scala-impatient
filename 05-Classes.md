5. Classes
==========
Scala classes are not declared as public, they automatically have public visibility. A Scala source file can contain 
multiple classes. The constructor and methods can be called with or without parenthesis. *Convention* is to use `()` with 
mutator methods and to drop the `()` for accessor methods:
```Scala
val myCounter = new Counter()                    // Or new Counter
myCounter.increment()                            // Mutator method uses ()
println(myCounter.current)                       // Accessor method does not use ()
```

This convention can be enforced so that the class user must use `myCounter.current`, without parentheses:
```Scala
class Counter {
 ...
 def current = value                             // No () in definition
}
```


Class-private and Object-private Fields
---------------------------------------
In Scala (as well as in Java), a method can access the private fields of all objects of its class. For example:
```Scala
class Counter {
 private var value = 0
 def increment() { value += 1 }
 def isLess(other : Counter) = value < other.value // Can access private field of other object
}
```

However, if a field is marked as *class-private* (`private[ClassName]`) then only methods of that class or object can 
access the field and a private getter/setter is generated.

If a field is marked as *object-private* (`private[this]`) then only methods of that object instance can access the field
and a getter/setter is *not* generated.


Getters and Setters
-------------------
A *field* in Scala consists of a private field and accessor/mutator methods. Scala provides *getter* and *setter* methods 
for every field. Getters have the form `fieldname()` and setters have the form `fieldname_=(param)`. The visibility of 
these methods is determined by how the fields have been declared:
    
    var               - public getter and setter, field made private
    private var       - private getter/setter, field made private
    val               - public getter only, field made private final
    private val       - private getter only, field made private final
    private[this] var - no getter or setter, field made private
    private[this] val - no getter or setter, field made private final

For example:
 ```Scala
class MyClass {
  var field1: Int = 0
  private var field2: Int = 0
  val field3: Int = 0
  private val field4: Int = 0
  private[this] var field5 : Int = 0
  private[this] val field6 : Int = 0
}
```

is compiled to:
```Scala
$ javap -private MyClass.class
Compiled from "MyClass.scala"
//
public class MyClass {
  private int field1;
  private int field2;
  private final int field3;
  private final int field4;
  private int field5;
  private final int field6;
  public int field1();
  public void field1_$eq(int);         // The JVM does not allow = in methodname so compiles to _$eq in bytecode
  private int field2();
  private void field2_$eq(int);
  public int field3();
  private int field4();
  public MyClass();
}
//
println(myClass.field1)                          // Calls the method myClass.field1()
myClass.field1 = 21                              // Calls myClass.field1_=(21)
```

To redefine the methods:
```Scala
class Person {
  private var privateAge = 0                     // Declare private and rename
  def age = privateAge
  def age_=(newValue: Int) {
    if (newValue > privateAge) 
      privateAge = newValue;                     // Can’t get younger
  }
}
```

To expose a var field with only a getter, create another method and remove the `()` in the method definition:
```Scala
class Counter {
  private var value = 0
  def increment() = value += 1
  def current = value                            // No () in declaration
}
//
val myCounter = new Counter()
val n = myCounter.current                        // Calling myCounter.current() is a syntax error
```


JavaBean Properties
-------------------
JavaBean `getFoo()` `setFoo()` methods can be generated by using the `@BeanProperty` annotation:
```Scala
import scala.beans.BeanProperty
class Person {
    @BeanProperty var name: String = _           // Initialise to the default value
}
//
val p = new Person()                             // p: Person = Person@9e16761
p.getName                                        // res0: String = null
p.setName("Fred")                                // res1: Unit = ()
p.getName                                        // res2: String = Fred
```

Fields declared as primary constructor parameters can also be annotated:
```Scala
class Person(@BeanProperty var name: String)
```


Primary Constructor
-------------------
A Scala class has one *primary constructor* and zero or more *auxiliary constructors*. Every Scala class has a *primary 
constructor* and it is supplied with the class definition or if you don’t define one the class will have a *primary 
constructor* with no arguments. The parameters of the *primary constructor* are placed immediately after the class name 
and they turn into fields that are initialized with the construction parameter values. The *primary constructor* executes 
*all statements in the class definition* (the `println` statement in the example below:
```Scala
class Person(val name: String, val age: Int) {
  println(s"Constructed $name")
  def description = s"$name is $age years old"
}
val p = new Person("Fred", 42)                   // Constructed Fred
                                                 // p: Person = Person@5471c860
```

Construction parameters without `val` or `var` will be turned into *object-private* final fields if they are referenced 
in a class definition statement otherwise they are treated like normal method parameters. To make the primary constructor
private, use the `private` keyword:
```Scala
class Person private(val id: Int) { ... }
```
A class user must then use an auxiliary constructor to construct a `Person` object.
 

Auxiliary Constructors
----------------------
The *auxiliary constructors* are called `this`. Each *auxiliary constructor* must start with a call to a previously 
defined *auxiliary constructor* or the *primary constructor*.
```Scala
class Person {
  private var name = ""
  private var age = 0
  def this(name: String) {                       // An auxiliary constructor
    this()                                       // Calls the primary constructor
    this.name = name
  }
  def this(name: String, age: Int) {             // Another auxiliary constructor
    this(name)                                   // Calls the previous auxiliary constructor
    this.age = age
  }
}
//
val p1 = new Person                              // Primary constructor
val p2 = new Person("Fred")                      // First auxiliary constructor
val p3 = new Person("Fred", 42)                  // Second auxiliary constructor
```


Nested Classes
--------------
Like Java, Scala supports nested classes (i fact you can nest almost anything), however there is a difference - in Scala,
each outer class *instance* has it's own inner class i.e. they are not the same, just like they have their own fields:
```Scala
import scala.collection.mutable.ArrayBuffer
class Network {
  class Member(val name: String) {
    val contacts = new ArrayBuffer[Member]
  }
  private val members = new ArrayBuffer[Member]
  def join(m: Member) = {
    members += m
    m
  }
}
//
val chatter = new Network
val myFace = new Network
//
chatter.join(new chatter.Member("Fred"))         // res0: chatter.Member = Network$Member@7f4c29d2
myFace.join(new myFace.Member("Tom"))            // res1: myFace.Member = Network$Member@7598f6ac
myFace.join(new chatter.Member("Alice"))         // Type Mismatch error
```

In this example `chatter.Member` and `myFace.Member` are treated as different classes so `Member` instances of `chatter`
are not compatible with `Member` instances of `myFace`. To make the different Member classes compatible, you can either 
move it somewhere else (e.g. to the Network *companion* object) or use a *type projection* `Network#Member`, which means 
*a Member of any Network.*:
```Scala
class Network {
  class Member(val name: String) {
    val contacts = new ArrayBuffer[Network#Member]         // type projection
  }
  ...
}
```

Nested classes can access the `this` reference of the enclosing class as `EnclosingClass.this`, like in Java. However, 
you can also create an alias for that reference (*outer* or *self* are common choices for the alias name):
```Scala
class Network(val name: String) { outer =>                 // outer will refer to Network.this
  class Member(val name: String) {
    ...
    def description = name + " inside " + outer.name
  }
}
```



