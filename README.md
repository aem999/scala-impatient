Scala
=====

[Scala](http://www.scala-lang.org/) notes and code.


Style Guide
-----------
http://docs.scala-lang.org/style


Sections
--------
1. [Basics (Variables, Data Types, Methods)](01-Basics.md)
2. [Control Structures and Functions](02-ControlStructures.md)
3. [Arrays](03-Arrays.md)
4. [Maps and Tuples](04-MapsTuples.md)


REPL
----
The Scala interpretor reads an expression, compiles it to bytecode, then executes the bytecode using the JVM. This is called 
the *read-evaluate-print* loop, or REPL. To enter the following multi-line code into the REPL:
```no-highlight
if (x > 0) 1
else if (x == 0) 0 else -1
```

Option 1 - use braces:
```no-highlight
if (x > 0) { 1
} else if (x == 0) 0 else -1
```

Option 2 - use *:paste*:
```no-highlight
1. Type *:paste*
2. Paste in the code block
3. Type *Ctrl+K*
```