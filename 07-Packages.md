7. Packages
===========
Scala *packages* can contain *classes*, *objects* and *traits*, but not the definitions of *functions* or *variables* 
(due to a limitation of the Java virtual machine). *Package objects* (see below) address this limitation.


Nested Package Notation
-----------------------
Scala *packages* can be nested and the package names do not have to match the directory structure of the containing file: 
```Scala
package com {
  package myco {
    class Employee
    ...
  }
}
```

In the example above, class `Employee.scala` does not need to be in a directory named `com/myco` and in fact the package 
`com.myco` can be defined in multiple files in different directories. A single file may contain more than one package.


Top-of-File Package Notation
----------------------------
Instead of the nested notation, package clauses can be placed at the top of the file, without braces. For example:
```Scala
package com.myco
package people
class Person
...
```

This is equivalent to:
```Scala
package com.myco {
  package people {
    class Person
     ...
  }
}
```

*Top-of-File* is the preferred notation if all the code in the file belongs to the same package (which is the usual case).


Package Scope
-------------
Names from the encosing scope are accessible without needing a package prefix:
```Scala
package com {
  package myco {

    object Utils {
      def percentOf(value: Double, rate: Double) = value * rate / 100
    }

    package people {
      class Employee {
        var salary = 0
        def giveRaise(rate: scala.Double) =
        {
          salary += Utils.percentOf(salary, rate)          // Utils from parent package myco scope
        }
      }
    }
  }
}
```

If there is a name collision then the absolute package name can be given:
```Scala
val employees = new _root_.scala.collection.mutable.ArrayBuffer[Employee]
```


Chained Package Clauses
-----------------------
*Chained package clauses* limits the scope of visible names:
```Scala
package com.myco {
  // Members of com are not visible here
  package people {
    class Person
    ...
  }
}
```


Package Objects
---------------
Every package can have one package object and it is defined in the parent package with the same name as the child 
package:
```Scala
package com.myco
  package object people {
    val defaultName = "John Smith"
  }
  package people {
    class Person {
    var name = defaultName                       // A constant from the package
  }
  ...
}
```

This makes `com.myco.people.defaultName` accessible. Behind the scenes, the package object gets compiled into a JVM class 
with static methods and fields, called `package.class`, inside the package.


Package Visibility
------------------
*Qualifiers* can be used to control package visibility:
```Scala
package com.myco.people
  class Person {
    private[people] def description = "A person with name " + name   // only visible to people package or below
    ...
  }
}
```
You can extend the visibility to an enclosing package:
```Scala
private[myco] def description = "A person with name " + name
```


Imports
-------
*Imports* allow short names to be used:
```Scala
import java.awt.Color                            // can write Color instead of java.awt.Color
import java.awt._                                // import all members of the java.awt package
import java.awt.{_}                              // same as java.awt._
import java.awt.Color._                          // import all members of the Color class (like Java static import)
import java.awt.{Color, Font}                    // Selector to import specific package members 
import java.util.{HashMap => JavaHashMap}        // Selector to rename a member (rename HashMap to JavaHashMap)
import java.util.{HashMap => _, _}               // Selector to hide a member (java.util.HashMap)
```


Implict Imports
---------------
Every Scala program implicitly imports:
```Scala
import java.lang._                               // As in Java programs
import scala._                                   // Unlike other imports can override preceding imports 
import Predef._                                  // Useful functions (predates Package Objects)
```
