8. Inheritance
==============

Class Inheritance
-----------------
The `extends` and `final` keywords are as in Java:
```Scala
class Employee extends Person {
  var salary = 0.0
  ...
}
```
Marking a class `final` prevents it from being extended. Making a field or method `final` prevents them from being 
overridden, this differs from Java where final fields are constants like `val` fields in Scala.


Overriding Methods
------------------
In Scala, the `override` modifier must be used when overriding a method that isn�t abstract:
```Scala
class Person {
  override def toString = getClass.getName + "[name=" + name + "]"
  ...
}
```

The override modifier can give useful compilation error messages when e.g:
- misspelling the name of the method that you are overriding
- providing a wrong parameter type in the overriding method
- introducing a new method in a superclass that clashes with a subclass method

The `super` keyword can be used to access methods in the parent class:
```Scala
class Employee extends Person {
  override def toString = super.toString + "[salary=" + salary + "]"
}
```


Type Checks and Casts
---------------------
Scala provides the following methods for type checking:
```Scala
obj.getClass == classOf[Class]              // obj is an instance of Class                          
obj.isInstanceOf[Class]                     // obj is an instance of Class or a Subclass (returns false if obj is null)
obj.asInstanceOf[Class]                     // cast obj to an instance of Class
```

However, pattern matching is usually a better alternative to using type checks and casts:
```Scala
p match {
  case e: Employee => ... // Process e as an Employee
  case _ => ...           // p is not an Employee
}
```


Protected Fields and Methods
----------------------------
Fields and methods can be declared as `protected`. Such a member is accessible from any subclass, but not from other 
locations (unlike in Java, where protected members are visible throughout the package to which the class belongs). There 
is also a `protected[this]` variant that restricts access to the current object.


Superclass Construction
-----------------------
Only the *primary constructor* can call a superclass constructor. An auxiliary constructor can never invoke a superclass 
constructor directly. The auxiliary constructors of the subclass eventually call the primary constructor of the subclass. 
In the following example, the Employee class primary constructor `Employee(name: String, age: Int, val salary : Double)` 
calls the superclass constructor `Person(name, age)` and passes two of its parameters. 
```Scala
class Employee(name: String, age: Int, val salary : Double) extends Person(name, age)
```

A Scala class can extend a Java class. Its primary constructor must invoke one of the constructors of the Java superclass:
```Scala
class Square(x: Int, y: Int, width: Int) extends 
  java.awt.Rectangle(x, y, width, width)
```

Overriding Fields
-----------------
A val filed (or a parameterless def) can be overridden with another val field of the same name. The subclass has a 
private field and a public getter, and the getter overrides the superclass getter (or method):
```Scala
class Person(val name: String) {
  override def toString = getClass.getName + "[name=" + name + "]"
}
class SecretAgent(codename: String) extends Person(codename) {
  override val name = "secret"                   // Don�t want to reveal name...
  override val toString = "secret"               // ...or class name
}
```
A more typical use case is to override an abstract `def` with a `val`:
```Scala
abstract class Person { 
  def id: Int                                         // Each person has an ID that is computed in some way
  ...
}
class Student(override val id: Int) extends Person    // A student ID is simply provided in the constructor
```

Note the following restrictions:
- A def can only override another def.
- A val can only override another val or a parameterless def.
- A var can only override an abstract var


Anonymous Subclasses
--------------------
You can make an instance of an anonymous subclass if you include a block with definitions or overrides:
```Scala
val alien = new Person("Fred") {
  def greeting = "Greetings, Earthling! My name is Fred."
}
```

This creates an object of a *structural type* which is denoted as `Person{def greeting: String}` and it can be used as a 
parameter type:
```Scala
def meet(p: Person{def greeting: String}) {
  println(p.name + " says: " + p.greeting)
}
```


Abstract Fields
---------------
An *abstract field* is simply a field without an initial value (the generated Java class has no fields). Concrete 
subclasses must provide concrete fields.
```Scala
abstract class Person {
  val id: Int                                // No initializer, this is an abstract field with an abstract getter
  var name: String                           // Another abstract field, with abstract getter and setter methods
}
class Employee(val id: Int) extends Person { // Subclass has concrete id property
  var name = ""                              // and concrete name property
} 
```

As with methods, no override keyword is required in the subclass when you define a field that was abstract in the 
superclass. You can always customize an abstract field by using an anonymous type:
```Scala
val fred = new Person {
  val id = 1729
  var name = "Fred"
}
```


Construction Order and Early Definitions
----------------------------------------
When you override a val in a subclass *and* use the value in a superclass constructor, the resulting behavior is unintuitive:
```Scala
class Creature {
  val range: Int = 10
  val env: Array[Int] = new Array[Int](range)
}
class Ant extends Creature {
  override val range = 2
}
```
In this example, `env` is created as an empty array of length 0 because the range value is used in the superclass 
constructor, and the superclass constructor runs before the subclass constructor:
  1. The Ant constructor calls the Creature constructor before doing its own construction.
  2. The Creature constructor sets its range field to 10.
  3. The Creature constructor, in order to initialize the env array, calls the range() getter.
  4. The range() getter is overridden to yield the (as yet uninitialized) range field of the Ant class.
  5. The range method returns 0. (That is the initial value of all integer fields when an object is allocated.)
  6. env is set to an array of length 0.
  7. The Ant constructor continues, setting its range field to 2
  
This can be solved by using the early definition syntax in the subclass:


Early Definition or Pre-initialized Fields
------------------------------------------
The *early definition* syntax lets you initialize val fields of a subclass before the superclass is executed. Place the 
val fields in a block after the extends keyword:
```Scala
class Ant extends {
  override val range = 2
} with Creature
```

This is more commonly used with traits. The code below will throw a `NullPointerException` when `MyFoo` is created because 
`bar` will not yet be defined when `bar.length()` is invoked:
```Scala
trait Foo {
  val bar:String
  val barLength = bar.length()
}
object MyFoo extends Foo{
  val bar = "test"
}
```

But this can be resolved by using early initialization:
```Scala
object MyFoo extends {
  val bar = "test"
} with Foo {
  ...
}
```


The Scala Inheritance Hierarchy
-------------------------------
![Scala Inheritance Hierarchy](classhierarchy.png?raw=true)

    Any          - the root of the hierarchy. Defines methods *isInstanceOf*, *asInstanceOf*, and the methods for 
                   equality and hash codes
    AnyVal       - classes that correspond to Java primitives plus *Unit()*. Just a marker interface, does not add any 
                   new methods
    AnyRef       - synonym of Java *Object* class. Adds the monitor methods *wait/notify/notifyAll* from the *Object* 
                   class. It also provides a synchronized method with a function parameter 
                   `account.synchronized { account.balance += amount }`
    ScalaObject  - marker interface with no methods and is implemented by all Scala classes
    Null type    - the type whose sole instance is the value *null*. You can assign null to any reference, but not to 
                   one of the value types. For example, setting an *Int* to *null* is not possible.
    Nothing type - the *Nothing* type has no instances. It is occasionally useful for generic constructs, e.g. the empty 
                   list *Nil* has type *List[Nothing]*, which is a subtype of *List[T]* for any *T*.
 
 
Object Equality
---------------
In Scala, the `eq` method of the `AnyRef` class checks whether two references refer to the same object. The `equals` 
method in `AnyRef` calls `eq`. An example equals:
```Scala
class Item(val description: String, val price: Double) {
  final override def equals(other: Any) = {
    val that = other.asInstanceOf[Item]
    if (that == null) false
    else description == that.description && price == that.price
  }
}
```
The equals method parameter type must be `Any`. The following would be wrong `final def equals(other: Item) = {...}`.
This is a different method that does not override the equals method of `AnyRef`.

We defined the method as `final` because it is generally very difficult to correctly extend equality in a subclass. The 
problem is symmetry. You want a.equals(b) to have the same result as b.equals(a), even when b belongs to a subclass.

In an application program, you don’t generally call `eq` or `equals`. Simply use the `==` operator. For reference types, 
it calls equals after doing the appropriate check for null operands.

When you define equals, remember to define hashCode as well. The hash code should be computed only from the fields that 
you use in the equality check. In the Item example, combine the hash codes of the fields:
```Scala
final override def hashCode = 13 * description.hashCode + 17 * price.hashCode
```

 