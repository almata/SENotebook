# FunProg week 3: Data & abstraction

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Classes

Using classes, we can encapsulate functions and data structures. In Scala we define them with this syntax:

```scala
class Rational(x: Int, y:Int) {
  private def gcd(a: Int, b: Int): Int =
	if (b == 0) a else gcd(b, a % b)
  private val g = gcd(x, y)

  def numer = x / g // numerator    
  def denom = y / g // denominator

  // Second constructor (using just a numerator)    
  def this(x: Int) = this(x, 1)

  def add(that: Rational) =
	new Rational(
	  this.numer * that.denom + that.numer * this.denom,
	  this.denom * that.denom

  def neg: Rational =
	new Rational(-this.numer, this.denom)

  def sub(that: Rational) = 
	this.add(that.neg)

  def less(that: Rational) =
	this.numer * that.denom < that.numer * this.denom

  def max(that: Rational) =
	if (this.less(that)) that else this

  // Useful to customize how an object has to be shown    
  override def toString =
	this.numer + "/" + this.denom
}
```

This definition introduces a new type (`Rational`) and a primary constructor to create elements of this type. We also have some definitions inside, being private the first two (`gcd` and `g`) so they can only be called from inside the `Rational` class.

Remember that the difference between `def` and `val` is that while `def` makes the right hand side be evaluated on each use, `val` only evaluates once, at the point of the definition itself.

It's also possible to create more constructors for a class with this syntax, in which we create a second constructor that just takes an argument and calls the primary constructor:

```scala
def this(x: Int) = this(x, 1)
```

Please note that the use of `this` to refer to the object on which a method is executed is optional, but can be useful for readability reasons.

As in standard object oriented programming, we call the elements of a class type objects, and we create them and call their members with the quite common syntax:

```scala
val x = new Rational(1, 2) // 1/2
val y = new Rational(3, 4) // 3/4
x.numer                    // 1
x.denom                    // 2
x.add(y)                   // 5/4
y.sub(x)                   // 1/4
x.less(y)                  // true
x.max(y)                   // 3/4
val z = new Rational(5)    // 5/1
```

## Preconditions and assertions

It's possible to force a precondition for our class. Let's say we want our `Rational` objects to have a positive denominator, we can do it adding this at the very beginning:

```scala
require(y != 0, "denominator cannot be zero")
```

This `require` function is a predefined function that takes a condition and an optional customized message string. If the precondition isn't fulfilled, an IllegalArgumentException with our customized error message is raised.

It's also possible to set assertions to check the code of a function. We do it this way:

```scala
assert(x >= 0)
```

If the assertion fails, an AssertionError exception will be raised.

## Operators

In our previous example, if we want to sum two `Rational` numbers we need to write `r.add(s)` but, in general, we are used to sum numbers just doing `r + s`. In Scala we can eliminate this difference in two steps.

The first step is introducing infix notation for methods. That means that any method with a parameter can be used like an infix operator this way:

```scala
r.add(s)  == r add s
r.less(s) == r less s
r.max(s)  == r max s
```

The second step is about relaxing the formal form of identifiers, so an identifier like `+` can be a valid one. So in Scala `*`, `+`, `+?%&` are valid identifiers.

With all this in mind, we can rewrite our previous `Rational` class into this new `RationalOperator` class (the class does exactly the same, we are using a different name just to avoid conflicts ant make it clear):

```scala
class RationalOperator(x: Int, y:Int) {
  require(y != 0, "denominator cannot be zero")

  // Useful to show 1/4 instead of 2/8
  private def gcd(a: Int, b: Int): Int =
	if (b == 0) a else gcd(b, a % b)
  private val g = gcd(x, y)

  def numer = x / g // numerator
  def denom = y / g // denominator

  // Second constructor (using just a numerator)
  def this(x: Int) = this(x, 1)

  def + (that: RationalOperator) =
	new RationalOperator(
	  this.numer * that.denom + that.numer * this.denom,
	  this.denom * that.denom)

  def unary_- : RationalOperator =
	new RationalOperator(-this.numer, this.denom)

  def - (that: RationalOperator) =
	this + -that

  def < (that: RationalOperator) =
	this.numer * that.denom < that.numer * this.denom

  def max(that: RationalOperator) =
	if (this < that) that else this

  // Useful to customize how an object has to be shown
  override def toString =
	this.numer + "/" + this.denom
}
```

And now we check it works:

```scala
val x = new Rational(1, 2) // 1/2
val y = new Rational(3, 4) // 3/4
x.numer                    // 1
x.denom                    // 2
x + y                      // 5/4
y - x                      // 1/4
x < y                      // true
x max y                    // 3/4
```

In Scala the precedence of different operators is determined by its first character. But anyway we always can use parenthesis to make it clear.

## Abstract classes

We are going to see this topic working with an example based on binary trees.

Abstract classes can contain members which are missing an implementation, so no instances of an abstract class can be created with the operator `new`.

```scala
abstract class IntSet {
  def contains(x: Int): Boolean
  def incl(x: Int): IntSet
}
```

So in order to create actual objects we first need some class or classes that extend the abstract one (and are not abstract themselves):

```scala
class Empty extends IntSet {
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, new Empty, new Empty)
  override def toString = "."
}

class NonEmpty(elem: Int, left: IntSet, right: IntSet) extends IntSet {
  def contains(x: Int): Boolean =
	if (x < elem) left contains x
	else if (x > elem) right contains x
	else true
  def incl(x: Int): IntSet =
	if (x < elem) new NonEmpty(elem, left incl x, right)
	else if (x > elem) new NonEmpty(elem, left, right incl x)
	else this
  override def toString = "{" + left + elem + right + "}"
}
```

We can now create empty and non empty sets and play with them:

```scala
val t1 = new NonEmpty(3, new Empty, new Empty)  //> t1  : NonEmpty = {.3.}
val t2 = t1.incl(4)                             //> res0: IntSet = {.3{.4.}}
```

One important aspect here is that we are still purely functional, there is no mutation. When we use `incl` method to include a new element in a tree, we are actually creating a new tree that includes that element (and not modifying a previous existing tree).

Both the type `Empty` and `NonEmpty` conform to the type `IntSet`, which means that any object of type `Empty` or `NonEmpty` can be used whenever an object of type `IntSet` is required. Scala implements dynamic method dispatch, which means that the code invoked by a method call depends on the runtime type of the object that contains the method.

Any user-defined class extends another class (when no superclass is given, the standard java.lang.Object class is assumed). 

## Object definitions

In the previous example, all `Empty` objects will actually be equal. To avoid creating many different instances we should use a Singleton patern. In Scala we can do it in a very simple way, expressing `Empty` as an object instead of as a class:

```scala
object Empty extends IntSet {
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, new Empty, new Empty)
  def toString = "."
}
```

## Classes organization

In Scala, classes and objects can be organized using packages, similar to what happens in Java.

To do that, we simply add a `package` sentence at the beginning of the file:

```scala
package week3
```

After that, we can refer to entities in a package using either their fully qualified name or an `import` sentence. This `import` sentence come in several forms:

```scala
import week3.Class1
import week3.{Class1, Object1}
import week3._
```

The first form imports only one entity. The second, some. The last, all entities in that package.

In any Scala program there are some entities that are always automatically imported:

* all members of package `scala`
* all members of package `java.lang`
* all members of the singleton object `scala.Predef`

That's why we can use `Int`, `Boolean`, `Object`, `require`, `assert` and so on. They all are types or functions included in this automatically imported entities.

## Traits

In Scala, as it happens in Java and in many object oriented languages, a class can only inherit from one superclass. If we want to kind of simulate inheritance from more than one superclass, however, we can use traits, as classes, objects and traits can inherit from at most one class but arbitrary many traits.

Traits have some similarities with interfaces in Java, although they aren't exactly the same. For instance, traits in Scala can contain fields and fully implemented methods.

```scala
trait Planar {
  def height: Int
  def width: Int
  def surface = height * width
}

class Square extends Shape with Planar with Movable ...
```

## Scala's class hierarchy

![Scala's class hierarchy][31]

At the top of the type hierarchy we find `Any`, `AnyRef` and `AnyVal`:

* `Any` is the one that gives us the methods `==`, `!=`, `equals`, `hashCode`, `toString`.
* `AnyRef` is just an alias for `java.lang.Object`.
* `AnyVal` is the base type of all primitive types (`Int`, `Boolean`, etc.).

At the bottom of the type hierarchy we find `Nothing` and `Null`:

* `Nothing` is a subtype of every other type, but there is no value of type `Nothing`, it's just useful to signal abnormal termination and as an element type of empty collections.
* `Null` is a subtype of every subclass of `AnyRef` type. It's also the type of the `null` value. It's incompatible with subtypes of `AnyVal`.

For example, the expression `throw Exc` aborts evaluation raising an exception. The type of this expression is `Nothing`.

[01]: https://www.coursera.org/course/progfun
[31]: funprog-week-3-data-abstraction.png
