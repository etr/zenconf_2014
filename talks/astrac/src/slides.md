name: inverse
layout: true
class: center, middle, inverse
---
#Introduction to Scala and Functional Programming
[Aldo Stracquadanio - Senior Developer @ Footfall123]
---
layout: false
.left-column[
  ## Agenda
]

.right-column[

- What is Scala?

  - Type inference
  - Boilerplate reduction in class definitions
  - Variable definitions
  - Function invocations
  - Pure OO
  - Classes
  - Traits
  - Singleton objects
  - Implicit conversions

- What is Functional Programming?

  - Why to use the functional paradigm?
  - Everything is an expression
  - Being expressive with higher order functions
  - Case classes & pattern matching
  - Typeclasses
  - An example of purity

- References
]
---
.left-column[
  ## What is Scala?
]
.right-column[
  Scala is a concise, modern, multi-paradigm, statically-typed language

  - Created by Martin Odersky (main contributor for the JVM generics support)

  - Supported by Typesafe and the EPFL (École polytechnique fédérale de Lausanne)

  - Runs on the JVM

  - Seamlessly interoperate with existing Java libraries

  - Blends Object Oriented and Functional programming

  - Advanced support for concurrency and distributed systems

  - Still young but gaining traction in the industry
]
---
.left-column[
  ## What is Scala?
  ### Type inference
]
.right-column[
  Type inference reduce the noise in your code by making optional obvious type annotations:

  In Java:
  ```java
  int n = 42;
  String s = "The answer";

  Map<String, Integer> m =
    new HashMap<String, Integer>();
  l.put(s, n);
  ```

  In Scala using the type inference:
  ```scala
  val n = 42
  val s = "A string"
  val m = Map(s -> n)
  ```

  In Scala with explicit type annotations:
  ```scala
  val n: Int = 42
  val s: String = "A string"
  val m: Map[String, Int] = Map(s -> n)
  ```
]
---
.left-column[
  ## What is Scala?
  ### Boilerplate reduction in class definitions
]
.right-column[
  A typical Java "Person" class

  ```java
  public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
      this.name = name;
      this.age = age;
    }

    public String getName() {
      return name;
    }

    public int getAge() {
      return age;
    }

    public void setName(String name) {
      this.name = name;
    }

    public void setAge(int age) {
      this.age = age;
    }
  }
  ```
]
---
.left-column[
  ## What is Scala?
  ### Boilerplate reduction in class definitions
]
.right-column[
  Same example in Scala

  ```scala
  class Person(var name: String, var age: Int)
  ```

  With custom getter/setter:
  ```scala
  class Person(var name: String, private var _age: Int) {

    def age = _age // Getter

    def age_=(newAge: Int) { // Setter
      println(s"Setting age to $newAge")
      _age = newAge
    }
  }
  ```

  In general this is not how value-object modelling works in Scala.

  As we are going to see when talking about Functional Programming immutability
  and *case classes* are preferred to getters and setters:

  ```scala
  case class Person(name: String, age: Int)
  ```
]
---
.left-column[
  ## What is Scala?
  ### Variable definitions
]
.right-column[
  Variables can be defined as val or var:

  - *val* are constants.

    ```scala
    val answer = 42
    // ...
    answer = 43 // Compile error
    ```

    - vals can also be *lazy*, in which case their value will be computed only
      when accessed the first time.

      ```scala
      lazy val x = "Some big computation"
      ```

  - *var* are variables

    ```scala
    var x = "Initialization value"
    // ...
    x = "Some other value" // Compiles
    ```

    - vars are usually discouraged in public APIs as immutability is always
      preferred.
    - They are useful when correctly encapsulated to simplify otherwise
      complex logic or when they make it easier to achieve better performances.
]
---
.left-column[
  ## What is Scala?
  ### Function invocations
]
.right-column[
  Basic function definition:

  ```scala
  def foo(bar: String, baz: String) {
    println(s"Bar: $bar - Baz: $baz")
  }

  foo("A", "B") // prints "Bar: A - Baz: B"
  ```

  Default parameters and named parameter invocations:

  ```scala
  def foo(bar: String = "A", baz: String = "B") {
    println(s"Bar: $bar - Baz: $baz")
  }

  foo()    // prints "Bar: A - Baz: B"
  foo("C") // prints "Bar: C - Baz: B"
  foo(baz = "D") // prints "Bar: A - Baz: D"
  ```
]
---
.left-column[
  ## What is Scala?
  ### Pure OO
]
.right-column[
  Everything is an object, no primitive types

  ```scala
  val one = 1.toString // -> "1"
  ```

  Every operation is a method call, so these two statements are equivalent:

  ```scala
  val six = 1 + 2 + 3 // -> 6
  ```

  ```scala
  val six = 1.+(2).+(3) // -> 6
  ```

  Syntactic sugar allow omission of **.** and **( )**, so these two statements
  are equivalent as well:

  ```scala
  val c = "Something".charAt(1) // Returns 'o'
  ```

  ```scala
  val c = "Something" charAt 1 // Returns 'o'
  ```
]
---
.left-column[
  ## What is Scala?
  ### Classes
]
.right-column[
  Classes in Scala are purely dynamic (i.e. no static keyword) and can be abstract.
  Class definition works pretty much in the same way as in Java:

```scala
abstract class Animal(val name: String) {
  def speak(): Unit // abstract method
  override def toString = name // override is required
}

class Cat extends Animal("cat") {
  override def speak() { // override here is optional
    println("Meow!")
  }
}

val cat = new Cat // Instantiation
val dog = new Animal("dog") { // Anonymous class
  override def speak() { println("Woof!") }
}

cat.speak() // prints Meow!
dog.speak() // prints Woof!
```

  The equivalent of Java's **Object** is **Any**: all classes inherit from it. There is also
  a type named **Nothing** which inherits from all the classes (more on this later).
]
---
.left-column[
  ## What is Scala?
  ### Traits
]
.right-column[
  Traits are the Scala equivalent of interfaces, but they allow for concrete
  implementations as well and it is still possible to mix multiple traits in the
  same class:

```scala
trait Animal {
  def call: String
  def speak() {
    println(call)
  }
}

trait Flying {
  def fly() {
    println("I'm flying!")
  }
}

class Bird extends Animal with Flying {
  def call = "cheep"
}

val b = new Bird

b.speak() // prints "cheep"
b.fly() // prints "I'm flying!"
```
]
---
.left-column[
  ## What is Scala?
  ### Singleton objects
]
.right-column[
  As classes do not have a static part Scala has a special keyword for singleton objects.

  A singleton object can have the same name as a class, in which case it is defined
  as **companion object**.

  The main difference between a singleton object and a static class in Java is that
  singleton objects can extend classes and traits.

```scala
trait SayHello {
  def name: String
  def message = "Hello " + name + "!"
}

object Hello extends SayHello {
  override def name = "world"

  def world() {
    println(message)
  }
}

Hello.world() // prints "Hello world!"
```
]
---
.left-column[
  ## What is Scala?
  ### Implicit conversions
]
.right-column[
  Scala supports *implicit conversions* to enrich types by importing definitions
  in scope.

  An implicit conversion is a function that takes an argument of a type and returns
  an argument of another type.

  Implicit conversions will be applied at compile-time when referring to a method
  or a property that doesn't exist in the base type but exists in the converted one.

```scala
class RichString(s: String) {
  def twice = s + s
}

object RichString {
  implicit def toRichString(s: String) =
    new RichString(s)
}

object HelloTwice {
  import RichString._

  def main(args: Array[String]) {
    println("Hello World!\n".twice)
  }
}
```
]
---
template: inverse

## What is Functional Programming?
---
.left-column[
  ## What is Functional Programming?
]
.right-column[
Functional Programming is a style of programming which models computations as
the evaluation of expressions. The building blocks of Functional Programming are
*pure functions*.

A function is *pure* when given the same input arguments it will always return
the same value and perform no side effects. Side effects can be anything like:

- Reassigning a variable
- Modifying a data structure in place
- Setting a field on an object
- Throwing an exception or halting with an error
- Printing to the console or reading user input
- Reading from or writing to a file
- Drawing on the screen
]
---
.left-column[
  ## What is Functional Programming?
  ### Why tu use the functional paradigm?
]
.right-column[
Even with this limitations functional languages are still Turing complete and can
express everything that the imperative paradigm can.

**Functional programming is a restriction on how we write programs, but not on what
programs we can express.**

The reason for this paradigm reside in what we get when we give up on side effects
and mutable state:

- Functions are first-class values
- High expressiveness through higher order functions
- No mutable state and no side effects mean no critical areas when designing
  concurrent systems

This last point is the main reason for this return of Functional Programming in
the industry: as the Moore law is not applying any more to single core systems
computation needs to be handled in parallel!
]
---
name: last-page
template: inverse

## Questions?

.footnote[Slideshow created using [remark](http://github.com/gnab/remark).]