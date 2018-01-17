# FunProg week 4: Types & pattern matching

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Type parameters & Generics

Imagine we want to create a little class hierarchy to work with Cons-Lists in Scala.

A Cons-List is a common data structure consisting in an immutable linked list constructed from two building blocks: `Nil` (the empty list) and `Cons` (a cell containing an element and the remainder of the list).

In code, we can see it this way:

```scala
trait IntList ...
class Nil extends IntList ...
class Cons(val head: Int, val tail: IntList) extends IntList ...
```

Please note the special syntax `val head: Int` in the definition of `Cons`. It defines at the same time a parameter and a field of a class.

But this class hierarchy is only valid to work with Cons-Lists of `Int` elements. We would need a different one for any type. To avoid that, we use type parameters:

```scala
trait List[T] ...
class Nil[T] extends List[T] ...
class Cons[T](val head: T, val tail: List[T]) extends List[T] ...
```

This is the complete implementation of our Cons-List using type parameters:

```scala
trait List[T] {
  def isEmpty: Boolean
  def head: T
  def tail: List[T]
}

class Cons[T](val head: T, val tail: List[T]) extends List[T] {
  def isEmpty = false
}

class Nil[T] extends List[T] {
  def isEmpty = true
  def head: Nothing = throw new NoSuchElementException("Nil.head")
  def tail: Nothing = throw new NoSuchElementException("Nil.tail")
}
```

Functions can also have type parameters. For instance, this is a function that creates a list consisting of a single element:

```scala
def singleton[T](elem: T) = new Cons[T](elem, new Nil[T])
```

Which we can use now this way:

```scala
singleton[Int](1)
singleton[Boolean](true)
```

But as long as the Scala compiler is usually able to deduce the correct type parameter from the value arguments, we often can left it out and just write:

```scala
singleton(1)
singleton(true)
```

In Scala, like in Java or Haskell,  type erasure is used. That means that type parameters and type arguments are removed before evaluating the program. So the types are only important for the compiler. This is different from C++, C# or F#, that keep the type parameters around at run time.

## Polymorphism

Polymorphism means that a function type comes in many forms. In programming it means that the function can be applied to arguments of many types, or the type can have instances of many types. 

We have seen two principal forms of polymorphism: subtyping and generics. Using subtyping, instances of a subclass can be passed to a base class. Using generics, instances of a function or class are created by type parameterization.

## Example: Nat / Peano numbers

Let's play a little bit with objects in Scala with an example based on the [Peano axioms][41].

Peano numbers are a simple way of representing the natural numbers using only a zero value and a successor function. Only with that, we can provide an implementation of the abstract class Nat that represents non-negative integers (and of course doesn't use the standard primitive classes):

```scala
abstract class Nat {
  def isZero: Boolean
  def predecessor: Nat
  def successor: Nat
  def + (that: Nat): Nat
  def - (that: Nat): Nat
}

// Sub-object for number zero
object Zero extends Nat {
  def isZero = true
  def predecessor = throw new Error("0.predecessor")
  def successor = new Succ(this)
  def + (that: Nat) = that
  def - (that: Nat) = if (that.isZero) this else throw new Error("0 - number")
}

// Sub-class for strictly positive numbers
class Succ(n: Nat) extends Nat {
  def isZero = false
  def predecessor = n
  def successor = new Succ(this)
  def + (that: Nat) = new Succ(n + that)
  def - (that: Nat) = if (that.isZero) this else n - that.predecessor
}
```

As we can see, for both `Zero` and `Succ` class, the implementation of the `successor` method is actually the same. So we could (and should) refactor it moving that definition to the abstract class `Nat`.

## Functions as objects

Functions are actually treated as objects in Scala. For example, the function type `A => B` (a function that takes a type `A` and returns a type `B`) is just an abbreviation for this class:

```scala
package scala
trait Function1[A, B] {
  def apply(x: A): B
}
```

So we can see that functions are actually objects with apply methods. There are also traits `Function2`, `Function3` and so on (up to 22 parameters).

And when we have an anonymous function as `(x: Int) => x * x` it's actually an abbreviation for this:

```scala
{ class AnonFun extends Function1[Int, Int] {
	def apply(x: Int) = x * x
  }
  new AnonFun
}
```

Taking this into account, in our previously defined `List` class we can define some functions that allow us to create lists using the form `List(1, 2)`: 

```scala
object List {
  // List() = List.apply()
  def apply[T](): List[T] = new Nil
  // List(1) = List.apply(1)
  def apply[T](x1: T): List[T] = new Cons(x1, new Nil)
  // List(1, 2) = List.apply(1, 2)
  def apply[T](x1: T, x2: T): List[T] = new Cons(x1, new Cons(x2, new Nil))
}
```

## Subtyping and generics

When we work with subtypes and generics, we have to consider two important concepts: type bounds and variance.

Type bounds help us to to be more concrete with the types involved in a function. Let's imagine a class hierarchy with an abstract superclass `A` and two concrete subclasses `B` and `C`. We then could have a function like this:

```scala
def myFunction(x: A): A
```

We will be passing objects of type `B` and `C` to the function, and maybe getting the same types as a result, but we are just expressing that all involved objects will be of type `A`.

Using type bounds we can be more concrete:

```scala
def myFunction[S <: A](x: S): S
```

The part `[S <: A]` expresses an upper bound for the type parameter `S`. It means that `S` can be instantiated only to types that conform to `A`. This way we have more information about the types going around in this function.

Generally, the notation `S <: T` means that `S` is a subtype of `T`, while `S >: T` means that `S` is a supertype of `T`.

It's actually possible to mix an upper and a lower bound simultaneously. For instance this would restrict `S` any type on the interval between `T` and `Z` in any given class hierarchy:

```scala
[S >: T <: Z]
```

## Variance

Variance is totally related with generics. We've seen that we can create lists using this form:

```scala
List[B]
```

And in previous paragraphs we've imagined a class hierarchy where `B` is a subtype of `A`. 

In general, we know that the standard definition says that "if `A <: B`, then everything one can do with a value of type `B` one should also be able to do with a value of type `A`".

Can we then say that `List[B]` is actually a subtype of `List[A]`?

Well, not always. We call types for which this relationship holds covariant, because their subtyping relationship varies with the type parameter. In our example, `List` is covariant, meaning that `List[B]` really is a subtype of `List[A]`. 

But sometimes, covariance can cause problems, so there are some types that are not covariant (for instance, `Array`).

Roughly speaking, a type that accepts mutations of its elements should not be covariant. On the other hand, immutable types can be covariant if some conditions on methods are met.

If `B` is a subtype of `A`:
* if `C[B] <: C[A]` then `C` is covariant,
* if `C[B] >: C[A]` then `C` is contravariant,
* if neither `C[A]` nor `C[B]` is a subtype of the other, `C` is nonvariant.

Scale lets us declare the variance of a type by annotating the type parameter:

```scala
class C[+T] { ... } // C is covariant
class C[-T] { ... } // C is contravariant
class C[T] { ... } // C is nonvariant
```

But the Scala compiler will check that there are no problematic combinations when compiling a class with variance annotations. Roughly:

* covariant type parameters can only appear in method results,
* contravariant type parameters can only appear in method parameters,
* invariant/nonvariant type parameters can appear anywhere.

So functions are contravariant in their argument type(s) and covariant in their result type.

## Object oriented decomposition

Imagine we want to write an interpreter for arithmetic expressions. These expressions will be only numbers and additions and will be represented with a hierarchy with `Expr` as superclass and `Number` and `Sum` as its subclasses.

This could be an implementation of this hierarchy (in the code `Expr` will be shown as `ExprN` to work easily with different versions):

```scala
trait Expr1 {
  def isNumber: Boolean  // classification
  def isSum: Boolean     // classification
  def numValue: Int      // accessor
  def leftOp: Expr1      // accessor
  def rightOp: Expr1     // accessor
}

class Number1(n: Int) extends Expr1 {
  def isNumber: Boolean = true
  def isSum: Boolean = false
  def numValue: Int = n
  def leftOp: Expr1 = throw new Error("Number.leftOp")
  def rightOp: Expr1 = throw new Error("Number.rightOp")
}

class Sum1(e1: Expr1, e2: Expr1) extends Expr1 {
  def isNumber: Boolean = false
  def isSum: Boolean = true
  def numValue: Int = throw new Error("Sum.numValue")
  def leftOp: Expr1 = e1
  def rightOp: Expr1 = e2
}
```

And now we could write an evaluation function as follows:

```scala
def eval(e: Expr1): Int =
  if (e.isNumber) e.numValue
  else if (e.isSum) eval(e.leftOp) + eval (e.rightOp)
  else throw new Error("Unknown expression " + e)
```

Imagine now that we want to add two new subclasses of `Expr`:

```scala
class Prod(e1: Expr, e2: Expr) extends Expr // e1 * e2
class Var(x: String) extends Expr           // variable 'x'
```

We would be forced to add new classification methods (`isProduct` and `isVar`) in all the classes and a new accessor (`name`) also in all the classes, throwing exceptions where they make no sense. So, creating more subclasses would force us to add new methods to preexisting classes and end up with a huge amount of methods. This is clearly non scalable.

One possible solution to this problem would be to use type tests and casts to create an evaluation function this way:

```scala
def eval(e: Expr): Int =
  if (e.isInstanceOf[Number]) e.asInstanceOf[Number].numValue
  else if (e.isInstanceOf[Sum]) eval(e.asInstanceOf[Sum].leftOp) + eval(e.asInstanceOf[Sum].rightOp)
  else throw new Error("Unknown expression " + e)
```

That would allow us to remove all the classification methods in all classes and implement the access methods only when they make sense. But this is a low-level and potentially unsafe solution. So we actually need a better approach.

A first valid solution is to use object oriented decomposition. Instead of creating an external eval function, we add it as a method inside our hierarchy:

```scala
trait Expr2 {
  def eval: Int
}

class Number2(n: Int) extends Expr2 {
  def eval: Int = n
}

class Sum2(e1: Expr2, e2: Expr2) extends Expr2 {
  def eval: Int = e1.eval + e2.eval
}
```

This works pretty fine but, again, we'll have to define new methods in all the subclasses if we'd like to not only evaluate expressions (`eval`) but also display them (`show`). And if we'd have a `Prod` class and we want to create a function that works together with `Sum` and `Prod` elements (i.e. `simplify`) we can't get it with methods inside those classes. 

So, in some cases we'll need a different type of solution, one based on pattern matching.

## Pattern matching

Pattern matching is a generalization of `switch` from C/Java to class hierarchies. In Scala it's expressed using the keyword `match`.

We use it when functions contain very complicated or nested switch statements. In this case, we can split the function definition into multiple ones, and put patterns in place of some arguments. When the function is called, the compiler compares the arguments with the definitions at runtime, and picks the correct one (the most specific definition available).

An important benefit of pattern matching is that if we need to add or modify conditions, we don't need to go into one huge function, but only add or modify appropriate definitions.

So, in our `Expr` example we want functions to `eval`, `show` and `simplify` (but maybe in the future will be more, so it has to be easily scalable).

We achieve it using pattern matching. And for it, we need case classes. A case class definition is similar to a normal class definition, except that it's preceded by the modifier `case`:

```scala
trait Expr3

case class Number3(n: Int) extends Expr3

case class Sum3(e1: Expr3, e2: Expr3) extends Expr3
```

This implicitly defines companion objects with `apply` methods, so we can automatically write `Number3(1)` instead of `new Number3(1)`:

```scala
object Number3 {
  def apply(n: Int) = new Number3(n)
}

object Sum3 {
  def apply(e1: Expr3, e2: Expr3) = new Sum3(e1, e2)
}
```

How do we access the members in each class if we don't have any accessor? This way:

```scala
def eval(e: Expr3): Int = e match {
  case Number3(n) => n
  case Sum3(e1, e2) => eval(e1) + eval(e2)
}
```

Or defining the evaluation function as a method of the base `trait`:

```scala
trait Expr3 {
  def eval: Int = this match {
	case Number3(n) => n
	case Sum3(e1, e2) => e1.eval + e2.eval
  }
}
```

The part after the `case` is called the pattern. If no pattern matches the value of the selector, a `MatchError` exception is thrown.

So, when to choose between object oriented and pattern matching? As a general rule, if we are going to frequently add new subclasses to our hierarchy, the object oriented decomposition approach will be better. On the other hand, if our hierarchy is going to be quite stable but we are often going to add new methods, the pattern matching option is easier.

[01]: https://www.coursera.org/course/progfun
[41]: http://en.wikipedia.org/wiki/Peano_axioms
