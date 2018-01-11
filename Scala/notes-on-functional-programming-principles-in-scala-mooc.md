# Notes on Functional Programming Principles in Scala MOOC

In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course.

## Week 1: Functions & evaluations

### Programming Paradigms

In general terms, we can distinguish three different programming paradigms:

* imperative programming (i.e. C, Java)
* functional programming (i.e. Scala or Haskell)
* logic programming (i.e. Prolog)

In imperative programming, we are used to modify mutable variables, use assignments and use control structures as if-then-else, loops, break, continue and return. In functional programming, nonetheless, we avoid mutations and totally concentrate on functions, which is why we say that in functional programming languages functions are first-class citizens. That means, for instance, that functions can be defined anywhere, including inside other functions, and that they can be passed to other functions as parameters or returned as results. 

One of the important benefits of functional programming is that it's specially good for exploiting parallelism and concurrency for multicore and cloud computing.

### Evaluation of expressions: call-by-value and call-by-name

For the first steps using Scala, we use a REPL (Read–Eval–Print–Loop) tool which allows us to write some code and expressions and immediately get the result. There are some console tools available, but we can also use an Eclipse Scala Worksheet. 

The evaluation process is done as usual: firstly, the leftmost operator is taken (subject to the rules of operators precedence); secondly, its operands are evaluated (left before right); finally, the operator is applied to the operands. Any name is evaluated by replacing it with the right hand side of its definition:

```scala
def pi = 3.1415
pi + 3 // pi will be evaluated to 3.1415
```

Please note expressions in Scala may finish or not with a semi-colon (`;`). So, every line shows a different expression unless the `+` operator is used at the end of a line, which specifies that the expression continues on the next line.

But definitions can have parameters:

```scala
def square(x: Double) = x * x
square(4) // will be evaluated to 4 * 4
```

And we can also specify the returned type:

```scala
def square(x: Double): Double = x * x
```

Actually with `def` we are defining our functions. And in these functions the same evaluation process applies: firstly, arguments are evaluated from left to right; secondly, the function definition applies to the arguments. It's important to note that this evaluation process in some cases may not reduce to a value, for instance this function does not:

```scala
def loop: Int = loop
```

But this is just one way an expression can be evaluated (call-by-value). There is another one called call-by-name. They both reduce to the same final values as long as the reduced expression consists of pure functions and both evaluations terminate, but it's possible that that doesn't happen. Let's see how they both work with an example:

```scala
// Note this function doesn't use the second parameter
def test(x: Int, y:Int) = x * x
```

If we use call-by-value we first have to evaluate the arguments, while if we use call-by-name we first apply the function definition:

```scala
// Using call-by-value
test(3+4, 2*4)
test(7, 2*4)
test(7, 8)
7 * 7
49

// Using call-by-name
test(3+4, 2*4)
(3+4) * (3+4)
7 * (3+4)
7 * 7
49
```

In this example they both reduce to the same value and in the same number of steps, but it could be very different:

```scala
// Using call-by-value
test(3+4, loop)
test(7, loop)
test(7, loop)
...
test(7, loop) // it'd never end!

// Using call-by-name
test(3+4, loop)
(3+4) * (3+4)
7 * (3+4)
7 * 7
49
```

So, if a CBV evaluation of an expression terminates, then CBN evaluation will always terminate too. But that's not necessarily true in the other direction.

In Scala the default is call-by-value, even when it's less guaranted that it will terminate (the reason for that is because CBV is very often more efficient). But we can force Scala to use call-by-name. To do so, we specify the type of a function parameter starting with `=>` (arrow):

```scala
// Note this function doesn't use the second parameter
def test(x: Int, y: => Int) = x * x
```

And now the evaluation for the previous example is as follows:

```scala
test(3+4, loop)
// First parameter is called by value
test(7, loop)
// Second parameter is called by name, so it's not reduced
// There aren't any parameters left, so we can call the function now
7 * 7
49
```

### Conditional expressions

We have conditional expressions if-else in Scala, but it's important to note that they are used for expressions, not for statements. So an if-else expression in Scala always returns an expression:

```scala
def abs(x: Int) = if (x >= 0) else -x
```

The part `x >= 0` is called a predicate and needs to be of type `Boolean`. It can use the standard operators `!`, `&&`, `||`, `<=`, `==`, `!=`, etc.

### def vs val

Both `def` and `val` are used to set definitions. But while `def` makes the right hand side be evaluated on each use, `val` only evaluates once, at the point of the definition itself. Let's see that with an example:

```scala
val x = 2 // x will always be 2
def y = square(x)
val z = square(x) // z will always be 4
```

From now on, every time we use `y` it will evaluate `square(x)` to get a value. On the other side, every time we use `z` it will directly refer to 4, without evaluating anything.

In functional programming we don't use variables the same way we do in imperative programming. Variables have to be understood simply as aliases for expressions (so we don't have to type everything on one line when writing a long expression). But these variables can't be modified, so in some sense they can be considered as final or constants.

### Example: Newton's method to calculate square roots

There are many methods of computing square roots, Newton's method is just one of them. It consists on taking an initial guess (i.e. y = 1, but any positive number is OK) and iteratively improve that guess by taking the mean of y and x/y (being x the number for which we want to calculate the square root). So if we want to get the square root for 10, we start by 1 and calculate the mean of 1 and 10/1, which is 5.5. We then calculate the mean of 5.5 and 10/5.5, which is 3.659. We keep doing the same until we get a result that we consider good enough. 

More information on the Newton's method to calculate square roots can be found [on the Wikipedia][11].

So we'll clearly need two functions: one to calculate that mean and get a new guess and another to check if a result is good enough or not. We'll call those functions `getNewGuess` and `isGoodEnough`. Once we have them, the square root can be calculated with this function:

```scala
def squareRootIter(guess: Double, x: Double): Double =
  if (isGoodEnough(guess, x)) guess
  else squareRootIter(getNewGuess(guess, x), x)
```

We have a recursive function here (a function that calls itself). Recursive functions are a really important part in functional programming, so we'll see lots of them from now on. 

The `getNewGuess` function can easily be defined according to our previous explanation:

```scala
def getNewGuess(guess: Double, x: Double): Double =
  (guess + x / guess) / 2
```

The `isGoodEnough` function requires some extra consideration: when are we actually going to decide that a value is good enough? Well, we could say that it's good enough if `guess * guess < 0.1`. That would work pretty fine for large numbers, but of course it wouldn't for small ones. A better approach is saying that `abs((guess * guess) - x) < x * 0.05`, which gives us an error below 5% (abs is a function with obvious meaning). Let's go with that:

```scala
def isGoodEnough(guess: Double, x: Double): Boolean =
  abs((guess * guess) - x) < x * 0.05
```

With all that we can now ask for `squareRootIter(1, 10)` and get a result of 3.196, which is a quite good approximation. But requiring an initial guess is not clean, as any positive initial guess will actually be OK, so we can write a new function like this:

```scala
def squareRoot(x: Double) =
  squareRootIter(1, x)
```

And so we now can get the same 3.196 result just by asking `squareRoot(10)`.

### Implicit or explicit return type

In the previous example there are some functions (like `isGoodEnough` that have an explicit return type, while others (like `squareRoot` just have it implicitly). This is because some functions are recursive (they call themselves) while others are not. Recursive functions always need to explicitly declare the return type. Non-recursive functions can declare it as well (and it's a good idea as it works as source code documentation), but it's not a requirement.

### Nested functions

In the previous example, we only need `squareRoot` function to be available, but instead we have four different functions that can be called by anyone. To prevent this, we can use blocks and nested functions to leave it this cleaner way:

```scala
def abs(x: Double) =
  if (x > 0) x else -x

def squareRoot(x: Double) = {
  def squareRootIter(guess: Double, x: Double): Double =
	if (isGoodEnough(guess, x)) guess
	else squareRootIter(getNewGuess(guess, x), x)

  def getNewGuess(guess: Double, x: Double): Double =
	(guess + x / guess) / 2

  def isGoodEnough(guess: Double, x: Double): Boolean =
	abs((guess * guess) - x) < x * 0.05

  squareRootIter(1, x)
}
```

Note that although the `abs` function could also be nested, it's the only one that has some real meaning by itself and so it's useful to leave it unnested so it can be used from any other functions.

### Blocks

These are some important notions about blocks:

* A block has to finish with a final expression so it can be evaluated.
* Blocks are themselves expressions in Scala, so a block may appear everywhere an expression can.
* The definitions inside a block are only visible from within the block.
* The definitions of outer blocks are visible inside a block unless they are shadowed.
* The definitions inside a block shadow definitions of the same names outside the block.

Taking this into account, we can rewrite previous definition for `squareRoot` so the `x` value is not passed as a parameter all the time:

```scala
def squareRoot(x: Double) = {
  def squareRootIter(guess: Double): Double =
	if (isGoodEnough(guess)) guess
	else squareRootIter(getNewGuess(guess))

  def getNewGuess(guess: Double): Double =
	(guess + x / guess) / 2

  def isGoodEnough(guess: Double): Boolean =
	abs((guess * guess) - x) < x * 0.05

  squareRootIter(1)
}
```

### Functional programming and unit testing

In functional programming, no function can ever cause side effects. That means a function can never modify a value outside of its scope and so the only effect of evaluating a function is its return value, and the only things that affect that return value are the function's arguments.

As a consequence of this, we can test every function in a program only worrying about its arguments. We don't need any `setUp` or `tearDown`. All we need to do is pass arguments that represent edge cases and check the results. If every function in a program passes all unit tests we can be a lot more confident about quality of our software than doing the same when using an imperative language.

## Week 2: Higher order functions

### Tail recursion

Let's see two examples of recursive functions. The first one is the Euclidean algorithm to calculate the greatest common divisor of two numbers (more information can be found [on the Wikipedia][21]). The second is just a trivial way to calculate the factorial for a number.

```scala
def gcd(a: Int, b: Int): Int =
  if (b == 0) a else gcd(b, a % b) 

def factorial(n: Int): Int =
  if (n == 0) 1 else n * factorial(n - 1)
```

There is an important difference between these two simple recursive functions. The `gcd` function calls itself in an isolated way, which means that every time it calls itself it does with different arguments but keeping the final expression to evaluate small (in each recursive call, a simple `gcd(a, b)` with nothing else added is evaluated). On the other hand, the `factorial` function grows bigger and bigger in each recursive call. Graphically:

```scala
gcd(a, b)
// after many steps
gcd(c, 0)

factorial(a)
// after many steps
a * b * c * … * factorial(0)
```

The `gcd` function uses tail recursion, while `factorial` does not. When a function uses tail recursion, the function's stack frame of memory can be reused and so the function is executed in constant space. When it doesn't, the risk of getting a stack overflow exception increases, as each recursive call uses a different and new stack frame of memory.

In Scala we can require that a function is tail recursive using a `@tailrec` annotation:

```scala
@tailrec
final def gcd(a: Int, b: Int): Int =
  if (b == 0) a else gcd(b, a % b)
```

But if we give this annotation and the implementation of the function isn't actually tail recursive, an error will be raised, as here:

```scala
@tailrec
final def factorial(n: Int): Int =
  if (n == 0) 1 else n * factorial(n - 1)
// This will generate this compile time error:
// could not optimize @tailrec annotated method factorial: 
// it contains a recursive call not in tail position
```

Would it be possible to rewrite the `factorial` function so it becomes a tail recursive function? Certainly, this way:

```scala
// Tail recursive factorial
def factorialTR(n: Int): Int = {
  @tailrec
  def loop(n: Int, acum: Int): Int =
	if (n == 0) acum else loop(n - 1, acum * n)
  loop(n, 1)
}
```

In this new `factorialTR` implementation, the only recursive function (`loop`) calls itself in an isolated way.

In general terms, it's a good idea to write functions in the cleanest possible way, be it or not tail recursive, and just work on a tail recursive rewriting when we actually face the risk of getting stack overflow exceptions.

### Higher order functions

In functional programming, functions can be passed as a parameter to another function and returned as a result as well, which provides a very flexible way to compose programs. The functions that take other functions as parameters or that return functions as results are called higher order functions (as opposite to first order functions, which are the standard functions that just work on simple data types). So, functions that operate on other functions (accept them as arguments) or return other functions are called higher order functions. 

The idea behind this is that when we see that a particular piece of code is repeated in different functions, we can break it out into a new function and then write higher order functions that use it. Or, seen from a different point of view, when a piece of logic within our function needs to behave differently in different situations, we can break it out into a higher order function that uses different functions in each situation. In fact,  we get a higher degree of granularity with less code.

Let's see how a function can take another function as a parameter:

```scala
def calculate(a: Int, f: Int => Int): Int =
  if (a == 0) 0
  else f(a) + calculate(a - 1, f)
```

The `calculate` function has two parameters: the first one is just an integer, nothing special; the second one is a function that gets and returns an integer. When `calculate` gets the two arguments, it sums the result of applying the `f` function to every number between 0 and the passed integer. 

Please note the `=>` notation to identify a function type parameter that maps a type to another.

Imagine that we have these three simple functions that get and return an integer:

```scala
def single(x: Int) = x * 1
def double(x: Int) = x * 2
def triple(x: Int) = x * 3
```

When we call the `calculate` function using this other functions, we get the sum of some numbers, the sum of the double of some numbers and the sum of the triple of some numbers.

```scala
calculate(3, single) // 6
calculate(3, double) // 12
calculate(3, triple) // 18
```

### Anonymous functions

The big benefit of previous code is that we are reusing the `calculate` function, but on the other side we've needed to create the `single`, `double` and `triple` functions. This can actually be avoided using anonymous functions:

```scala
calculate(3, (x: Int) => x * 1) // 6
calculate(3, (x: Int) => x * 2) // 12
calculate(3, (x: Int) => x * 3) // 18
```

And the types can be ommitted if they can be infered by the compiler:

```scala
calculate(3, x => x * 1) // 6
calculate(3, x => x * 2) // 12
calculate(3, x => x * 3) // 18
```

### Currying

[According to the Wikipedia][22], currying is the technique of transforming a function that takes multiple arguments in such a way that it can be called as a chain of functions each with a single argument. Currying is used very often to adapt functions to an interface that someone else expects. Since the interface to functions is its arguments, currying is used to reduce the number of arguments.

For example, we can rewrite `calculate` function so it takes a single parameter (a function) and returns another function (that takes an integer and returns an integer) as its result. To do that we need to create a new function inside the main one. Doing that we get this `newCalculate` function:

```scala
def newCalculate(f: Int => Int): Int => Int = {
  def newCalculateF(a: Int): Int =
	if (a == 0) 0
	else f(a) + newCalculateF(a - 1) 
  newCalculateF
}

newCalculate (triple) (3) // 18
newCalculate (x => x * 3) (3) // 18
```

The part `newCalculate (triple)` is a function that returns another function that takes an integer and returns another integer. We pass a number 3 as an argument to that second function and so we get the final result (18). It's important to note that generally function application associates to the left:

```scala
newCalculate (triple) (3) == (newCalculate (triple)) (3)
```

The definition of functions that return functions is so used and useful in functional programming that there is a special syntax for it in Scala. This `newCalculateShorter` is exactly equivalent to previous `newCalculate`:

```scala
def newCalculateShorter(f: Int => Int)(a: Int): Int =
  if (a == 0) 0
  else f(a) + newCalculateShorter(f)(a - 1)
```

And the type for this function can be expressed this way:

```scala
(Int => Int) => (Int => Int)
// or 
(Int => Int) => Int => Int
```

It's important to note that functional types associate to the right:

```scala
Int => Int => Int == Int => (Int => Int)
```

Higher order functions and currying are so important because they allow us to create little functions that are more reusable and can be combined to create more complex abstractions.

## Week 3: Data & abstraction

### Classes

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

### Preconditions and assertions

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

### Operators

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

### Abstract classes

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

### Object definitions

In the previous example, all `Empty` objects will actually be equal. To avoid creating many different instances we should use a Singleton patern. In Scala we can do it in a very simple way, expressing `Empty` as an object instead of as a class:

```scala
object Empty extends IntSet {
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, new Empty, new Empty)
  def toString = "."
}
```

### Classes organization

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

### Traits

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

### Scala's class hierarchy

![Scala's class hierarchy][31]

At the top of the type hierarchy we find `Any`, `AnyRef` and `AnyVal`:

* `Any` is the one that gives us the methods `==`, `!=`, `equals`, `hashCode`, `toString`.
* `AnyRef` is just an alias for `java.lang.Object`.
* `AnyVal` is the base type of all primitive types (`Int`, `Boolean`, etc.).

At the bottom of the type hierarchy we find `Nothing` and `Null`:

* `Nothing` is a subtype of every other type, but there is no value of type `Nothing`, it's just useful to signal abnormal termination and as an element type of empty collections.
* `Null` is a subtype of every subclass of `AnyRef` type. It's also the type of the `null` value. It's incompatible with subtypes of `AnyVal`.

For example, the expression `throw Exc` aborts evaluation raising an exception. The type of this expression is `Nothing`.

## Week 4: Types & pattern matching

### Type parameters & Generics

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

### Polymorphism

Polymorphism means that a function type comes in many forms. In programming it means that the function can be applied to arguments of many types, or the type can have instances of many types. 

We have seen two principal forms of polymorphism: subtyping and generics. Using subtyping, instances of a subclass can be passed to a base class. Using generics, instances of a function or class are created by type parameterization.

### Example: Nat / Peano numbers

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

### Functions as objects

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

### Subtyping and generics

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

### Variance

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

### Object oriented decomposition

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

### Pattern matching

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

## Week 5: Lists

### Lists

The list is a fundamental data structure in functional programming. Lists are homogeneous in the sense that all the elements of a list must have the same type.

Lists and arrays have two important differences:

* lists are immutable, the elements of a list cannot be changed,
* lists are recursive, while arrays are flat.

A list can be created these ways:

```scala
val fruit = List("apples", "oranges", "pears")
var diag3 = List(List(1, 0, 0), List(0, 1, 0), List(0, 0, 1))
val empty = List()
```

Or also:

```scala
val fruit: List[String] = List("apples", "oranges", "pears")
var diag3: List[List[Int]] = List(List(1, 0, 0), List(0, 1, 0), List(0, 0, 1))
val empty: List[Nothing] = List()
```

Or also:

```scala
val fruit = "apples" :: ("oranges" :: ("pears" :: Nil))
val empty = Nil
```

All lists are actually constructed from the empty list `Nil` and the construction operation (`::`) that adds an element at the beginning of a list.

### Right associativity of operators ending in ":"

In Scala, operators ending in "`:`" always associate to the right. So in the previous definitions we just omit the parenthesis and write this instead:

```scala
val fruit = "apples" :: "oranges" :: "pears" :: Nil
val nums = 1 :: 2 :: 3 :: 4 :: Nil
```

These operators ending in "`:`" are seen as method calls of the right-hand operand (instead of the left-hand, which is the standard). So, previous `num` expression is actually the same as:

```scala
Nil.::(4).::(3).::(2).::(1)
```

These are the three operations more often used when working with lists: 

* `head`, to get the first element of the list,
* `tail`, to get the list composed of all the elements except the first,
* `isEmpty`, to check if the list is empty or not.

### List patterns

We can use the pattern matching technique we saw in previous chapters with lists. A pattern `Nil` will match an empty list. A pattern `p :: ps` will match a list with a `head` matching `p` and a `tail` matching `ps`. For example:

```scala
1 :: 2 :: xs  // list starting with 1 and then 2
x :: Nil      // list of length 1
List(x)       // list of length 1
List()        // empty List
Nil           // empty list
List(2 :: xs) // list with only one element, which is another list that starts with 2
```

### Example: sorting lists

Let's create a new method to sort a list of integers in ascending order. The idea is that, given a list, we can sort it by adding the head element in the right place in the sorted tail. This sorted tail will have been sorted using same algorithm, so it's obviously a recursive algorithm. 

Here's the code for this O(N*N) implementation:

```scala
def isort(xs: List[Int]): List[Int] = xs match {
  case List() => List()
  case y :: ys => insert(y, isort(ys))
} 

def insert(x: Int, xs: List[Int]): List[Int] = xs match {
  case List() => List(x)
  case y :: ys => if (x <= y) x :: xs else y :: insert(x, ys)
}
```

### Some list methods

Let's see some useful methods for working with lists (a full list of methods can be found at [Scala Standard Library][51]):

* `xs.length` returns the number of elements in the list,
* `xs.last` returns the list's last element,
* `xs.init` returns a list with all the elements but the last,
* `xs take n` returns a list with the first n elements,
* `xs drop n` returns the rest of the collection after taking the first n elements,
* `xs(n)` (or `xs apply n`) returns the nth element in the list,
* `xs ++ ys` returns a list joining two (in the order they appear),
* `xs.reverse` returns the list with all the elements in reversed order,
* `xs updated (n, x)` returns the same list but changing the nth element for `x`,
* `xs indexOf x` returns the index of the first element in the list equal to `x` (-1 if `x` is not in the list),
* `xs contains x` says if the list contains the `x` element.

Some of these methods can throw exceptions if we ask for non existing elements (i.e. the last element of an empty list).

It's important to keep in mind the complexity of each method we use when working with lists. Some of them can ve very efficient while others maybe are not so much.

### Pairs and tuples

To introduce pairs and tuples, we're going to work with a different (and more efficient) sorting algorithm. In concrete, we're going to implement the [merge sort algorithm][52].

In it, we start with a long list and separate it into two sub-lists, each containing around half of the elements of the original list. Then we recursively sort the two sub-lists same way and finally we merge the two sorted sub-lists into a single sorted list. This well known algorithm works pretty fine. 

A first implementation of it is as follows:

```scala
def msort1(xs: List[Int]): List[Int] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
	def merge(xs: List[Int], ys: List[Int]): List[Int] = xs match {
	  case Nil => ys
	  case x :: xs1 => ys match {
		case Nil => xs
		case y :: ys1 => if (x < y) x :: merge(xs1, ys)
						 else y :: merge(xs, ys1)
	  }
	}
	val (fst, snd) = xs splitAt n
	merge(msort1(fst), msort1(snd))
  }
}
```

If we take a look at the code, we can see we've used the `splitAt` function. This function on lists returns two sublists: one with the elements up to the given index and another with the elements from that index. And this two lists are returned in a pair.

In Scala we can define pairs just by writing two values in parenthesis:

```scala
val pair1 = ("answer", 42)
val pair2 = ('a', 9)
val pair3 = (10, 30)

// pair1 type is (String, Int)
// pair2 type is (Char, Int)
// pair3 type is (Int, Int)
```

And pairs can also be used as patterns, meaning that we can now do this:

```scala
val (label, value) = pair1
```

And `label` will take "answer" as its value and `value` wil take 42.

Another way to access the values of a pair is as follows:

```scala
val label = pair1._1
val value = pair1._2
```

This all works analogously for tuples with more than two elements. Actually a pair is just a tuple with two elements.

Now that we know how pairs work, we can rewrite our merge sort algorithm using a pattern matching over pairs:

```scala
def msort2(xs: List[Int]): List[Int] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
	def merge(xs: List[Int], ys: List[Int]): List[Int] = (xs, ys) match {
	  case (Nil, ys) => ys
	  case (xs, Nil) => xs
	  case (x :: xs1, y :: ys1) => if (x < y) x :: merge(xs1, ys)
								   else y :: merge(xs, ys1)
	}
	val (fst, snd) = xs splitAt n
	merge(msort2(fst), msort2(snd))
  }
}
```

### Generics and type parameters

In our previous example we have this function signature:

```scala
def msort(xs: List[Int]): List[Int]
```

That, of course, only works with `Int` types. We could try this other signature using generics:

```scala
def msort(xs: List[T]): List[T]
```

But it wouldn't work because we use the comparison `<` inside the function, and this comparison is not defined for arbitrary types `T`. To make it work, we need to parameterize the function with the necessary comparison function.

```scala
def msort[T](xs: List[T])(lt: (T, T) => Boolean) = {
  ...
  merge(msort(fst)(lt), msort(snd)(lt))
}

def merge(xs: List[T], ys: List[T]) = (xs, ys) match {
  ...
  case (x :: xs1, y :: ys1) =>
	if (lt(x, y)) ...
	else ...
}
```

Roughly, we are introducing a new parameter with is a less-than function (`lt`) that takes two `T` elements and returns a `Boolean` saying if the first is less than the second. With that, now any kind of `T` element can be compared.

So, the full code for this function is as follows:

```scala
def msort1[T](xs: List[T])(lt: (T, T) => Boolean): List[T] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
	def merge(xs: List[T], ys: List[T]): List[T] = (xs, ys) match {
	  case (Nil, ys) => ys
	  case (xs, Nil) => xs
	  case (x :: xs1, y :: ys1) => if (lt(x, y)) x :: merge(xs1, ys)
								   else y :: merge(xs, ys1)
	}
	val (fst, snd) = xs splitAt n
	merge(msort1(fst)(lt), msort1(snd)(lt))
  }
}
```

Now we can compare any kind of types just giving a valid comparison function. For instance, for integers we can sort a list this way:

```scala
val myList = -4 :: 2 :: 5 :: 1 :: 3 :: -9 :: Nil
msort1(myList)((x: Int, y: Int) => x < y)
//> res0: List[Int] = List(-9, -4, 1, 2, 3, 5)
```

But if we wanted to sort from big to small, it would be trivial:

```scala
msort1(myList)((x: Int, y: Int) => x > y)        
//> res1: List[Int] = List(5, 3, 2, 1, -4, -9)
```

Or if we wanted to sort taking into account the absolute value:

```scala
msort1(myList)((x: Int, y: Int) => abs(x) < abs(y))
//> res2: List[Int] = List(1, 2, 3, -4, 5, -9)
```

And the functions works same way for other types of data:

```scala
val myFruits = List("apple", "pineapple", "orange", "banana")
msort1(myFruits)((x: String, y: String) => x.compareTo(y) < 0)
//> res3: List[java.lang.String] = List(apple, banana, orange, pineapple)
```

Finally, Scala can infere the data types from the first parameter of `msort` function, so the last expression can also be written as follows:

```scala
msort1(myFruits)((x, y) => x.compareTo(y) < 0)
```

That's actually possible because the function parameter is the last one. So it's generally a good idea to put function parameters after type parameters, so the Scala compiler can infere the type parameters for the function parameter.

### Parameterization with Ordering

There is already a class in the standard library that represents orderings: `scala.math.Ordering[T]`. It contains the `lt` function, but some other useful ones.

So we actually could rewrite our `msort` function this other way using it:

```scala
def msort2[T](xs: List[T])(ord: Ordering[T]): List[T] = { 
  val n = xs.length / 2
  if (n == 0) xs
  else {
	def merge(xs: List[T], ys: List[T]): List[T] = (xs, ys) match {
	  case (Nil, ys) => ys
	  case (xs, Nil) => xs
	  case (x :: xs1, y :: ys1) => if (ord.lt(x, y)) x :: merge(xs1, ys)
								   else y :: merge(xs, ys1)
	}
	val (fst, snd) = xs splitAt n
	merge(msort2(fst)(ord), msort2(snd)(ord))
  }
}
```

And then use it as follows:

```scala
msort2(myList)(Ordering.Int)
//> res5: List[Int] = List(-9, -4, 1, 2, 3, 5)
msort2(myFruits)(Ordering.String)
//> res6: List[java.lang.String] = List(apple, banana, orange, pineapple)
```

### Implicit parameters

But if we know that most of the times when we call `msort` function with integers or strings we are using the standard comparison function, wouldn't be better if we could just avoid that parameter? Yes, we can do it using an implicit parameter:

```scala
def msort3[T](xs: List[T])(implicit ord: Ordering[T]): List[T] = { 
  val n = xs.length / 2
  if (n == 0) xs
  else {
	def merge(xs: List[T], ys: List[T]): List[T] = (xs, ys) match {
	  case (Nil, ys) => ys
	  case (xs, Nil) => xs
	  case (x :: xs1, y :: ys1) => if (ord.lt(x, y)) x :: merge(xs1, ys)
								   else y :: merge(xs, ys1)
	}
	val (fst, snd) = xs splitAt n
	merge(msort3(fst), msort3(snd))
  }
}
```

Here we are telling the compiler to use our explicit comparison `Ordering` if we specify one, or an implicit one if we do not. So we can call this function in different ways and we'll get the same result we got before:

```scala
msort3(myFruits)
msort3(myFruits)(Ordering.String)
```

Only thing to take into account when working with implicit parameters is that a visible and type compatible definition has to be available in the point where it's required. Otherwise an error will be thrown.

### Higher order list functions

In previous chapters we talked about higher order functions and said that "in functional programming, functions can be passed as a parameter to another function and returned as a result as well, which provides a very flexible way to compose programs. The functions that take other functions as parameters or that return functions as results are called higher order functions (as opposite to first order functions, which are the standard functions that just work on simple data types). So, functions that operate on other functions (accept them as arguments) or return other functions are called higher order functions. The idea behind this is that when we see that a particular piece of code is repeated in different functions, we can break it out into a new function and then write higher order functions that use it. Or, seen from a different point of view, when a piece of logic within our function needs to behave differently in different situations, we can break it out into a higher order function that uses different functions in each situation. In fact,  we get a higher degree of granularity with less code."

Now that we are working with lists we can see several recurring patterns, like:

* transforming each element in a list in a certain way,
* retrieving a list of all elements satisfying a criterion,
* combining the elements of a list using an operator.

### Transformation of lists

We can write generic functions that implement patterns such as these using higher order functions.

For instance, to multiply each element of a list by the same factor we can write:

```scala
def scaleList1(xs: List[Double], factor: Double): List[Double] = xs match {
  case Nil => xs
  case y :: ys => y * factor :: scaleList1(ys, factor)
}
```

But as that is a quite common pattern, there is a `map` method in the `List` class to do exactly that same thing:

```scala
def scaleList2(xs: List[Double], factor: Double) =
  xs map (x => x * factor)
```

### Getting sublists

Another example is selecting all elements in a list satisfying a given condition (i.e. numbers greater than zero). We can create a function by ourselves:

```scala
def posElems1(xs: List[Int]): List[Int] = xs match {
  case Nil => xs
  case y :: ys => if (y > 0) y :: posElems1(ys) else posElems1(ys)
}
```

Or we can use the `filter` method in the `List` class:

```scala
def posElems2(xs: List[Int]): List[Int] =
  xs filter (x => x > 0)
```

There are some other methods to extract sublists based on a boolean predicate:

* `xs filterNot p` returns a sublist with the elements that don't satisfy the predicate,
* `xs partition p` returns a pair `(xs filter p, xs filterNot p)` but computed in a single traversal of the list (instead of two),
* `xs takeWhile p` returns the longest sublist at the beginning of the list such that the predicate is true (so all elements until the first not satisfying the predicate),
* `xs dropWhile p` returns the remainder of the list not taken by `takeWhile`,
* `xs span p` returns a pair `(xs takeWhile p, xs dropWhile p)`.

A full list of methods can be found at [Scala Standard Library][53].

### Reduction of lists

Another common operation on lists is to combine its elements using a given operator. For instance, for the sum:

```scala
def sum1(xs: List[Int]): Int = xs match {
  case Nil => 0
  case y :: ys => y + sum1(ys)
}
```

But we can also use the `reduceLeft` method that inserts a given binary operator between adjacent elements of a list:

```scala
def sum2(xs: List[Int]) = (0 :: xs) reduceLeft ((x, y) => x + y)
```

The `0 :: xs` is to be sure an empty list will give a correct result (0).

We're using a lot little functions like this:

```scala
((x, y) => x * y))
```

These functions are actually so used that there is a shorter way to write them:

```scala
(_ * _)
```

In this syntax, every `_` represents a new parameter, going from left to right.

So we could write previous function `sum` as follows:

```scala
def sum3(xs: List[Int]) = (0 :: xs) reduceLeft (_ + _)
```

The `reduceLeft` method is actually defined in terms of a more general function: `foldLeft`. This function is very similar, but takes an accumulator as an additional parameter and returns it when called on an empty list. 

So, using `foldLeft` in our `sum` function this would be the final code:

```scala
def sum4(xs: List[Int]) = (xs foldLeft 0) (_ + _)
```

### foldRight and reduceRight

As seen, `foldLeft` and `reduceLeft` unfold on trees that lean to the left. This means that the operands are taken beginning from the left. Analogously, we have `foldRight` and `reduceRight` that do the opposite: produce trees which lean to the right, or take the operands beginning from the right.

With sums and products both versions return the same result, as they are associative and commutative, but with other operations they do not. For instance:

```scala
val myList = List(10.0, 5.0, 2.0)
myList reduceLeft (_ / _)  // 1.0
myList reduceRight (_ / _) // 4.0
```

## Week 6: Collections

### Vectors

We have seen that lists are linear: access to the first element is much faster than access to the middle or end of a list. Vectors, on the other hand, have more evenly balanced access patterns.

A vector is constructed using an array of maximum 32 elements. If it needs to contain more than 32 elements, it becomes an array of 32 pointers to 32 arrays of 32 elements. And so on -adding levels- until there is space for all the elements. So a depth of six can handle around a billion elements.

Access to random elements in a vector is related to its depth, so obviously it's very efficient. Lists are a better choice when we use a pattern where we use a list's head and a tail. Otherwise vectors are generally a better choice.

We create vectors in a similar way to lists:

```scala
val nums = Vector(1, 2, 3, -4)
val people = Vector("Bob", "James", "Peter")
```

And they support the same operations as lists, with the exception of `::` (but we can use `x +: xs` or `xs :+ x` instead to create a new vector with a new element at the beginning or the end of previous vector). Please note that vectors are immutable, so when we say we modify them we are actually creating a new one. 

### Ranges

Ranges are another simple kind of sequence that represent a sequence of evenly spaced integers using `to` (inclusive), `until` (exclusive) and `by` (to determine step value). Ranges are represented as single objects with three fields: lower bound, upper bound and step value.

```scala
val r: Range = 1 until 5 // 1, 2, 3, 4
val s: Range = 1 to 5    // 1, 2, 3, 4, 5
1 to 10 by 3             // 1, 4, 7, 10
6 to 1 by -2             // 6, 4, 2
```

### Operations on sequences

Sequences have a lot of available methods that can be found at [Scala Standard Library][61], being these some of the most common:

* `xs exists p` returns true if the sequence contains any element satisfying the predicate,
* `xs forall p` returns true if all elements in the sequence satisfy the predicate,
* `xs zip ys` returns a sequence of pairs taking one element from each sequence for each pair (the sequence with less elements will set the length of the sequence sequence of pairs),
* `xs.unzip` returns two sequences from a sequence of pairs,
* `xs.flatMap f` applies function `f` to all elements in the sequence and concatenates the results,
* `xs.sum` returns the sum of all elements in the sequence,
* `xs.product` returns the product of all elements in the sequence,
* `xs.max` returns the maximum of all elements in the sequence (an `Ordering` is required),
* `xs.min` returns the maximum of all elements in the sequence (an `Ordering` is required).

### Some examples

Let's see how we could get all combinations of numbers `x` (from 1 to M) and `y` (from 1 to N):

```scala
val M = 3
val N = 5   
(1 to M) flatMap (x => (1 to N) map (y => (x, y)))
//> Vector((1,1), (1,2), (1,3), (1,4), (1,5), 
//>        (2,1), (2,2), (2,3), (2,4), (2,5), 
//>        (3,1), (3,2), (3,3), (3,4), (3,5))
```

And now how we could compute the scalar product of two vectors:

```scala
def scalarProduct1(xs: Vector[Double], ys: Vector[Double]): Double =
  (xs zip ys).map(xy => xy._1 * xy._2).sum
```

Or same thing using patter matching function value:

```scala
def scalarProduct2(xs: Vector[Double], ys: Vector[Double]): Double =
  (xs zip ys).map{ case (x, y) => x * y }.sum
```

And how we could say if a number is prime (that is, its only divisors are 1 and the number itself):

```scala
def isPrime(n: Int): Boolean = (2 until n) forall (n % _ != 0)

isPrime(7)  // true
isPrime(14) // false
```

### Collection hierarchy

`List` and `Vector` have a common base class, `Seq`, which is actually the class of all sequences. And `Seq` itself is a subclass of `Iterable` same way as `Set` and `Map` are.

`Array` and `String` support the same operations as `Seq` and can implicitly be converted to sequences where needed, but are not direct subclasses of `Seq` because they come directly from Java. Anyway we can do things like these:

```scala
val xs: Array[Int] = Array(1, 2, 3)
xs map (x => 2 * x) // Array(2, 4, 6)

val ys: String = "Hello World!"
ys filter (_.isUpper) // "HW"
```

This is a simplification of the hierarchy we've seen so far:

```scala
Iterable

Map -> Iterable
Set -> Iterable
Seq -> Iterable

List   -> Seq
Vector -> Seq
Range  -> Seq

String ~> Seq
Array  ~> Seq
```

`->` means "it's subclass of", `~>` means "it's not subclass of, but very works as if it was". 

### Combinatorial search

Higher order functions on collections often replace loops on imperative languages.

Let's see that with an example. Say we have a positive integer `n` and want to find all pairs of positive integers `i` and `j` such that `i + j` is prime and `1 <= j < i < n'. For example, for 7 the sought pairs are these ones:

```
i: 2  3  4  4  5  6  6
j: 1  2  1  3  2  1  5
```

In imperative languages we can use two nested loops, one for `i` and one for `j` and check their sum on each iteration to see if we need to store that combination in any kind of collection.

In a functional language we look for a more natural way to do the same: first we generate the sequence of all pairs of integers fulfilling the `1 <= j < i < n' condition; after that we filter the pairs for which`i + j` is prime.

For the first part we can use some tools on sequences we've seen before and write this:

```scala
(1 until n) map (i =>
  (1 until i) map (j => (i, j)))
```

If we try this (i.e. in a Scala Worksheet) we'll see we get a vector of vectors of pairs, but we actually just want a single collection of pairs.

To achieve this, we can use the `foldRight` method we saw in previous sessions and the concatenation operation:

```scala
(xss foldRight Seq[Int]())(_ ++ _)
```

We use the `xss` notation to show its a collection of collections. There's actually a built-in method to do exactly this same operation:

```scala
xss.flatten
```

So we can rewrite previous code as follows:

```scala
((1 until n) map (i =>
  (1 until i) map (j => (i, j)))).flatten
```

And we'll get a vector of pairs (so a simple collection of pairs, which is what we wanted).

There's another way to achieve the same, taking into account that:

```scala
xs flatMap f = (xs map f).flatten
```

So our code can be written this way:

```scala
(1 until n) flatMap (i =>
  (1 until i) map (j => (i, j)))
```

The first part of our problem is solved: generating the sequence of all paris of integers fulfilling the `1 <= j < i < n` condition. Now we want to filter the pairs for which `i + j` is prime. To do that we can simply use the `filter` method:

```scala
(1 until n) flatMap (i =>
  (1 until i) map (j => (i, j))) filter (pair =>
	isPrime(pair._1 + pair._2))
```

And we finally get a collection with all pairs which fulfil the condition.

OK, this already works, but it's not very easy to follow and understand, it's probably less clear than the imperative language approach in fact. That's why we have For expressions.

### For expressions

Higher order functions such as `map`, `flatMap` or `filter` provide powerful constructs for manipulating lists. But sometimes the level of abstraction required by these functions make the program difficult to understand. In these situations, the `for` expression can help.

Imagine we have a `persons` list of elements of a class as follows:

```scala
case class Person(name: String, age: Int)
```

To get the names of all persons over 20 years old we could write:

```scala
persons filter (p => p.age > 20) map (p => p.name)
```

But we can instead use a `for` expression:

```scala
for (p <- persons if p.age > 20) yield p.name
```

The `for` expression is similar to loops in imperative languages, except that it builds a list of the results of all iterations.

A `for` expressions is always of the form:

```scala
for (s) yield e
```

Where `s` is a sequence of generators and filters and `e` is an expression whose value is returned by an iteration (`yield` sets what we want to produce with the expression):

* a generator is of the form `p <- e` where `p` is a pattern and `e` an expression whose value is a collection,
* a filter is of the form `if f` where `f` is a boolean expression,
* the sequence must start with a generator,
* if there are several generators in the sequence, the last generators vary faster than the first.

Instead of `(s)`, we can write `{s}` and then the sequence of generators and filters can be written on multiple lines without requiring semicolons.

Let's see now how we can solve our previous problem (pairs where the sum of both numbers is prime) using a `for` expression:

```scala
for {
  i <- 1 until n
  j <- 1 until i
  if isPrime(i + j)
} yield (i, j)
```

This is obviously a much cleaner and easy to understand implementation.

Finally, this is a `for` expression for the `scalarProduct` we saw in previous session:

```scala
def scalarProduct(xs: Vector[Double], ys: Vector[Double]): Double =
  (for ((x, y) <- xs zip ys) yield x * y).sum
```

### Sets

In previous sessions we showed this basic collection hierarchy:

```scala
Iterable

Map -> Iterable
Set -> Iterable
Seq -> Iterable

List   -> Seq
Vector -> Seq
Range  -> Seq

String ~> Seq
Array  ~> Seq
```

So we're now taking a look at sets. We can construct them easily:

```scala
val fruit = Set("apple", "banana", "pear")
val s = (1 to 6).toSet
```

And most operations on sequences are also available on sets. Nevertheless, there are some principal differences between sets and sequences:

* sets are unordered (the elements of a set do not have a predefined order in which they appear in the set),
* sets do not have duplicate elements,
* the fundamental operation on sets is `contains`.

### N queens problem

A classical problem in computing is the eight queens problem (or, more in general, N queens problem) which consists in placing queens on a chessboard so that no one is threatened by another. For example, for N = 4:

```
: X : :    row 0 col 1
: : : X    row 1 col 3
X : : :    row 2 col 0
: : X :    row 3 col 2
```

This is just a valid solution, but our goal is to find a set with all the possible solutions.

We can solve this problem with a recursive algorithm:

1. Suppose that we have already generated all the solutions consisting of placing k-1 queens on a board of size n.
2. Each solution is represented by a list (of length k-1) containing the numbers of columns (between 0 and n-1).
3. The column number of the queen in k-1 row comes first in the list, followed by the column number of the queen in row k-2, etc. So, in our previous example, List(0, 3, 1).
4. The solution set is thus represented as a set of lists, with one element for each solution.
5. Now, to place the k-th queen, we generate all possible extensions of each solution preceded by a new queen.

So, in code:

```scala
def queens(n: Int): Set[List[Int]] = {
  def placeQueens(k: Int): Set[List[Int]] =
	if (k == 0) Set(List())
	else 
	  for {
		queens <- placeQueens(k - 1)
		col <- 0 until n
		if isSafe(col, queens)
	  } yield col :: queens
  placeQueens(n)
}
```

We need a function to know if a queen is safe in a concrete column with a current queens distribution:

```scala
def isSafe(col: Int, queens: List[Int]): Boolean = {
  val row = queens.length // (1)
  val queensWithRow = (row - 1 to 0 by -1) zip queens // (2)
  queensWithRow forall {
	case (r, c) => col != c && math.abs(col - c) != row - r // (3)  
  }
}
```

In (1) we give a row value to the new queen.

In (2) we convert a list like `List(0, 3, 1)` into a new list of pairs with rows included (row, column) `List((2,0), (1,3), (0,1))`.

In (3) we check that the new column is not the same as any other's queen's column and that there is no diagonal between the new queen and any other.

Finally we just need a function to show the results in a graphic representation:

```scala
def show(queens: List[Int]) = {
  val lines =
	for (col <- queens.reverse)
	yield Vector.fill(queens.length)(": ").updated(col,"X ").mkString 
  "\n" + (lines mkString "\n")
}
```

And for N = 4 we get:

```
: : X : 
X : : : 
: : : X 
: X : : 

: X : : 
: : : X 
X : : : 
: : X :
```

The second solution is the one we showed at the beginning of this problem. The first one is a new solution that our algorithm has found. If we increase the value of N we'll find more and more different solutions.    

### Queries with for

If we analyze `for` expressions we've seen so far, they look a little bit like database queries. For instance, imagine we have a class `Book` as follows:

```scala
case class Book(title: String, authors: List[String])
```

We could create a little database with a simple list:

```scala
val books: List[Book] = List(
  Book(title = "book1 cs", authors = List("author11", "author12")),
  Book(title = "book2 maths", authors = List("author21")),
  Book(title = "book3 cs", authors = List("author31", "author32", "author33")),
  Book(title = "book4", authors = List("author11")))
```

Which is exactly the same as this other expression, just clearer:

```scala
val books: List[Book] = List(
  Book("book1 cs", List("author11", "author12")), ...)
```

We can now ask for the titles whose author is "author32":

```scala
for (b <- books; a <- b.authors if a == "author32") 
yield b.title
```

Note that there is an iteration on the books and another on the authors of any book.

Or the books with the word "cs" in the title:

```scala
for (b <- books if b.title indexOf "cs" >= 0) 
yield b.title
```

A more complicated query could be this one, to get the names of all authors that have written at least two books:

```scala
for {
  b1 <- books
  b2 <- books
  if b1 != b2
  a1 <- b1.authors
  a2 <- b2.authors
  if a1 == a2
} yield a1
```

This gets the correct result, but duplicated, because it iterates all books both in `b1` and in `b2`, so it creates all combination of books twice (in reversed order).

To avoid this, a first way would be to require that `b1.title < b2.title` instead of just `b1 != b2`. We still will have all combinations duplicated, but just produce results for one of them. But this is not a good enough solution, as if an author writes three books, let's say A, B and C, the condition will be satisfied for A < B, A < C and B < C, so again it will be shown more than once.

Another way, better, to solve that little problem is to use `distinct` method:

```scala
( for {
	b1 <- books
	b2 <- books
	if b1 != b2
	a1 <- b1.authors
	a2 <- b2.authors
	if a1 == a2
  } yield a1
).distinct
```

And the probably best option would be to define `books` from the very beginning as a `Set` instead of a `List`, so when we use `for` expressions we would get sets as well, and as sets can't have duplicate values we'll solve the problem.

### For expressions and higher order functions

For expressions are very related to the higher order functions `map`, `flatMap` and `filter`. These functions can actually be defined in terms of `for`. But in realitiy, the Scala compiler does the opposite: it expresses for expressions in terms of higher order functions.

So when we write these expressions, the Scala compiler translates them into their equivalences:

```scala
(1) for (x <- e1) yield e2 
(1) e1.map(x => e2)

(2) for (x <- e1 if f; s) yield e2 
(2) for (x <- e1.withFilter(x => f); s) yield e2

(3) for (x <- e1; y <- e2; s) yield e3
(3) e1.flatMap(x => for (y <- e2; s) yield e3)
```

In (1) we translate a `for` expression into a `map` expression. In (2) and (3) we translate `for` expressions into new `for` expressions with less elements.

We actually saw this with a previous example where we wanted to get pairs of numbers whose sum was prime and got these two solutions:

```scala
for {
  i <- 1 until n
  j <- 1 until i
  if isPrime(i +j)
} yield (i, j)

(1 until n) flatMap (i =>
  (1 until i) map (j => (i, j))) filter (pair =>
	isPrime(pair._1 + pair._2))
```

Taking a look at them, we do check that for expressions can be expressed in terms of higher order functions `map`, `flatMap` and `filter`.

Or, another example, our previous query to get books written by an author:

```scala
for (b <- books; a <- b.authors if a == "author32") 
yield b.title
```

Can also be expressed using higher order functions as follows (process shows step by step substitution):

```scala
// Original query
for (b <- books; a <- b.authors if a == "author32")
yield b.title

// (3) for (x <- e1; y <- e2; s) yield e3
// (3) e1.flatMap(x => for (y <- e2; s) yield e3)
books flatMap (b =>
  for(a <- b.authors if a == "author32") yield b.title)

// (2) for (x <- e1 if f; s) yield e2
// (2) for (x <- e1.withFilter(x => f); s) yield e2
books flatMap (b =>
  for(a <- b.authors.withFilter(a => a == "author32")) yield b.title)

// (1) for (x <- e1) yield e2
// (1) e1.map(x => e2)
books flatMap (b =>
  b.authors.withFilter(a => a == "author32").map(a => b.title))
```

The translation of `for` is not limited to lists or sequences, but is based solely on the presence of the methods `map`, `flatMap` and `withFilter`. So we can use the `for` syntax with our own types as long as we define these three functions for them. These are similar ideas to those used in Microsoft's LINQ.

### Maps

A map of type `Map[Key, Value]` is an immutable data structure that associates keys of type `Key` with values of type `Value`. For example:

```scala
val romanLetters = Map("I" -> 1, "V" -> 5, "X" -> 10)
val romanNumbers = romanLetters map {
  case (x, y) => (y, x)
}
```

But maps can also be used everywhere functions can, as class `Map[Key, Value]` extends the function type `Key => Value`. So everywhere we need one, we can use the other.

We can retrieve a value using this syntax:

```scala
romanNumbers(5) // "V"
```

But if key doesn't exist it will thrown an exception. So it's probably a better idea to use this other syntax that returns the value or a `None` if there is no match:

```scala
romanNumbers(5)     //> res0: java.lang.String = V
romanNumbers get 5  //> res1: Option[java.lang.String] = Some(V)
romanNumbers get 6  //> res2: Option[java.lang.String] = None
```

### The Option type

Please note that previous code returns values of type `Option`. The `Option` type is defined as:

```scala
trait Option[+A]
case class Some[+A](value: A) extends Option[A]
object None extends Option[Nothing]
```

So our previous `map get key` expressions return `None` if there is no value for the given key or `Some(x)` if there is a value.

Since options are defined as case classes, they can be decomposed using pattern matching:

```scala
def showRoman(number: Int) = romanNumbers get(number) match {
  case Some(letter) => letter
  case None => "no Roman letter for this number"
}
showRoman(10)  //> res3: java.lang.String = X
showRoman(13)  //> res4: java.lang.String = no Roman letter for this number
```

### OrderBy and GroupBy

In SQL queries we often use "order by" and "group by" operations. In Scala we can get the same on collections.

For instance for "order by":

```scala
val fruit = List("apple", "pear", "orange", "pineapple")
fruit sortWith(_.length < _.length)
//> res5: List[java.lang.String] = List(pear, apple, orange, pineapple)
fruit.sorted
//> res6: List[java.lang.String] = List(apple, orange, pear, pineapple)
```

And for "group by":

```scala
fruit groupBy(_.head)
//> res7: scala.collection.immutable.Map[Char,List[java.lang.String]] = 
//> Map(a -> List(apple), o -> List(orange), p -> List(pear, pineapple))
```

Please note that as a result of a `groupBy` operation we get a `Map` of collections.

### Map example: Polynom

A polynomial can be seen as a map from exponents to coefficients:

```scala
// x^3 - 2x + 5
Map (0 -> 5, 1 -> -2, 3 -> 1)
```

So we can create a `Poly` class to represent polynomials using maps:

```scala
class Poly(val terms: Map[Int, Double]) {
  def + (other: Poly) = new Poly(this.terms ++ other.terms)
  override def toString =
	(for ((exp, coeff) <- terms.toList.sorted.reverse) yield coeff + "x^" + exp) mkString " + "
}
```

But if we now test it we see that additions are not calculated well:

```scala
val p1 = new Poly(Map(1 -> 2.0, 3 -> 4.0, 5 -> 6.2))
val p2 = new Poly(Map(0 -> 3.0, 3 -> 7.0))
p1 + p2
//> res0: Poly = 6.2x^5 + 7.0x^3 + 2.0x^1 + 3.0x^0
```

Coefficient for x^3 should be 11.0 instead of 7.0. So we obviously have to improve something:

```scala
class Poly(val terms: Map[Int, Double]) {
  def + (other: Poly) = new Poly(this.terms ++ (other.terms map adjust))
  def adjust(term: (Int, Double)): (Int, Double) = {
	val (exp, coeff) = term
	terms get exp match {
	  case Some(coeff1) => exp -> (coeff + coeff1)
	  case None => exp -> coeff
	}
  }
  override def toString =
	(for ((exp, coeff) <- terms.toList.sorted.reverse) yield coeff + "x^" + exp) mkString " + "
}
```

And now it works as expected:

```scala
p1 + p2
//> res0: Poly = 6.2x^5 + 11.0x^3 + 2.0x^1 + 3.0x^0
```

But it looks too much work and verbosity to compute just an addition. Remember we said that maps are actually functions. But they are "partial functions" in the sense that applying a map to a key could lead to an exception if that key isn't included in the map. To avoid this, there is a `withDefaultValue` operation that turns a map into a total function:

```scala
romanNumbers(6) // exception thrown
val rn = romanNumbers withDefaultValue "-"
rn(5)  //> res3: java.lang.String = V
rn(6)  //> res3: java.lang.String = -
```

We can apply this technique to our `Poly` class:

```scala
class Poly(val terms0: Map[Int, Double]) {
  val terms = terms0 withDefaultValue 0.0
  def + (other: Poly) = new Poly(this.terms ++ (other.terms map adjust))
  def adjust(term: (Int, Double)): (Int, Double) = {
	val (exp, coeff) = term
	exp -> (coeff + terms(exp))
  }
  override def toString =
	(for ((exp, coeff) <- terms.toList.sorted.reverse) yield coeff + "x^" + exp) mkString " + "
}
```

Finally, we want to improve the way we create our polynomials, so instead of the first syntax we can use the second:

```scala
val p1 = new Poly(Map(1 -> 2.0, 3 -> 4.0, 5 -> 6.2))
val p1 = new Poly(1 -> 2.0, 3 -> 4.0, 5 -> 6.2)
```

This is challenging if we take into account that the number of parameters (in this example, three) could be any number. To achieve this, we need an auxiliary constructor in our class:

```scala
def this(bindings: (Int, Double)*) = this(bindings.toMap)
```

Where the `*` notation means that this is a repeated parameter.

### Example: Translating phone numbers into sentences

Just to recapitulate concepts seen in previous sessions, we are going to work in a more complex example. 

Let's imagine this assignation between numbers in a phone (i.e. an iPhone) and letters:

```scala
val mnemonics = Map('2' -> "ABC", '3' -> "DEF", '4' -> "GHI", 
                    '5' -> "JKL", '6' -> "MNO", '7' -> "PQRS", 
                    '8' -> "TUV", '9' -> "WXYZ")
```

And let's assume we have a dictionary with a list of words. 

Our goal is to design a method `translate` such as this call produces this result (and others):

```scala
translate("7225247386")
// Scala is fun
```

Please note that "S" is included in phone key for number 7, "c-a" in key for number 2, "l" in key for number 5, "a" in key for number 2, and so on until "u" and "n" that are in keys for number 8 and 6. So, for each number we have some different letters that we can use to create existing words in our dictionary of words.

### Final consideration on collections

Now that we've seen some Scala immutable collections it's a good time to sumarize some of their characteristics:

* easy to use: few steps to do the job,
* concise: one word replaces a whole loop,
* safe: type checker is really good at catching errors,
* fast: collection operations are tuned, can be parallelized,
* universal: one vocabulary to work on all kinds of collections.

This makes them a very attractive tool for software development.

## Week 7: Lazy evaluation

### Streams

Streams are like lists, but their tail is evaluated only on demand. In previous sessions we've seen a number of immutable collections that provide powerful operations, in particular for combinatorial search, for instance to get the second prime number bigger than 1000:

```scala
((1000 to 10000) filter isPrime)(1)
```

But this is quite inefficient, as it constructs all prime numbers in the range just to take the second.

It's in these situations that we can use `Stream`. Streams are similar to lists, but their tail is evaluated only on demand (when it's needed for the evaluation result, if ever).

There are three basic ways to create streams:

```scala
val xs = Stream.cons(1, Stream.cons(2, Stream.empty())
val ys = Stream(1, 2, 3)
val zs = (1 to 1000).toStream
```

As an example, these two expressions do the same:

```scala
def streamRange1(lo: Int, hi: Int): Stream[Int] = 
  (lo until hi).toStream

def streamRange2(lo: Int, hi: Int): Stream[Int] = 
  if (lo >= hi) Stream.empty
  else Stream.cons(lo, streamRange2(lo + 1, hi))
```

Nothing special, the same thing we've seen so far in all the previous sessions, but with the important difference that when we use those functions, just the first element is actually created. The rest will be created only when required. So the object knows how to do that. If we request then the tail part, the second element will be created and the rest will wait until required itself.

Streams support almost all methods of `List`. So our first example can be rewritten in this much more efficient way:

```scala
((1000 to 10000).toStream filter isPrime)(1)
```

But there is an exception: the `::` operator. It always produces a list, never a stream. To do the same for streams, we have to use `#::` instead (which can be used in expressions as well as patterns).

In a stream, the tail part is always a call by name parameter, while in lists it's a call by value parameter. That's why tail is only evaluated when required.

### Lazy evaluation

We can understand lazyness as doing things as late as possible and never doing them twice.

When we work with streams there is a serious potential performance problem: if `tail` is called several times, the corresponding stream will be recomputed each time. This problem can be avoided by storing the result of the first evaluation of `tail` and re-using the stored result instead of recomputing `tail` next times it's requested.

We call this scheme *lazy evaluation* (as opposed to *by-name evaluation* in the case where everything is recomputed, and *strict evaluation* for normal parameters and val definitions).

Some functional programming languages use lazy evaluation by default (i.e. Haskell) while others use strict evaluation (i.e. Scala). However, in Scala we can ask for a lazy evaluation as follows:

```scala
lazy val x = expr
```

So, the following three expressions are similar, but with an important difference: `expr` is evaluated every time `x` is used (1), or is evaluated only when the code reaches that line and then reuses the value (2), or is evaluated only first time it's going to be used (3):

```scala
def x = expr
val x = expr
lazy val x = expr
```

### Computing with infinite sequences

Lazy evaluation allows us to work with infinite sequences, as any part will only be computed when required. For instance, here is the stream of all integers starting from a given number:

```scala
def from(n: Int): Stream[Int] = n #:: from(n+1)
```

A definition like this would usually be non terminating, but as long as the second part is only evaluated when required, it's a valid construction in Scala.

Using `from` function above we can create a stream with all natural numbers, another stream with all natural numbers multiple of 4 or a list with the first ten multiples of 4:

```scala
val nats = from(0)
val m4s = nats map (_ * 4)
(m4s take 10).toList
```

### Example: the sieve of Eratosthenes

In mathematics, the sieve of Eratosthenes is a simple, ancient algorithm for finding all prime numbers up to any given limit. It does so by iteratively marking as composite (i.e. not prime) the multiples of each prime, starting with the multiples of 2. The multiples of a given prime are generated starting from that prime, as a sequence of numbers with the same difference, equal to that prime, between consecutive numbers. This is the sieve's key distinction from using trial division to sequentially test each candidate number for divisibility by each prime. The sieve of Eratosthenes is one of the most efficient ways to find all of the smaller primes (below 10 million or so).

More information on the sieve of Eratosthenes can be found [on the Wikipedia][71].

The algorithm is as follows: we start with all integers from 2, the first prime number, and eliminate all its multiples; we take the first element of the resulting list, 3, as a prime number and eliminate all its multiples again; we then take the first element of the resulting list, which will be a prime number, and eliminate all its multiples. And so on.

In Scala:

```scala
def sieve(s: Stream[Int]): Stream[Int] =
  s.head #:: sieve(s.tail filter (_ % s.head != 0))

(sieve(from(2)) take 10).toList
//> res3: List[Int] = List(2, 3, 5, 7, 11, 13, 17, 19, 23, 29)
```

### Back to square roots

In week 1, we got a function to calculate square roots, but we needed a `isGoodEnough` function to tell when to terminate the iteration. With streams we can now express the concept of a converging sequence without having to worry about when to terminate it:

```scala
def sqrtStream(x: Double): Stream[Double] = {
  def improve(guess: Double) = (guess + x / guess) / 2
  lazy val guesses: Stream[Double] = 1 #:: (guesses map improve)
  guesses
}
```

And now we can calculate square roots as follows:

```scala
sqrtStream(4).take(10).toList
```

Of course we still can use a `isGoodEnough` function if we want, but while at the beginning we needed it to find a good moment to stop iterating, we now don't need to worry about that.

### Case study: the water pouring problem

As a final example for these articles, we're going to work on the water pouring problem. It's a well known example solved many times, but this time we're going to solve it with a pure functional approach.

Put it short, we assume we have a big tank full of water and two recipients of different sizes (i.e. 5 and 3 liters) and want to get a concrete volume of water (i.e. 2 liters). In this obvious example, it'd be enough to fill the 5 liters recipient from the tank, then fill the 3 liters recipient using the 5 liters one, and when it's full, we'd have 2 liters of water in the 5 liters recipient. If, with the same recipients, we'd want to get 4 liters it'd be a little more complicated.

We're going to solve the general case: an arbitrary number of glasses with arbitrary capacities to get an arbitrary target capacity in one of the glasses.

For that, we will represent our problem this way:

```scala
Glass: Int
State: Vector[Int] (one entry per glass)
Three possible moves:
	Empty(glass)
	Fill(glass)
	Pour(from, to)
```

And the idea is to represent all possible states until one of them shows in any glass our target capacity. To do so, we begin with a `(0,0)` state and create all possible states using available moves. From each one, we do the same again. And keep doing until we reach a `(x,6)` state. We'll then have a path to get that state. Of course we have to check all the time if we are in a loop (which will mean there is no solution).

### Epilogue

In these 7 weeks we've seen some important functional programming concepts:

* higher order functions,
* case classes and pattern matching,
* immutable collections,
* absence of mutable state,
* flexible evaluation strategies: strict / lazy / by name.

Of course there is much more in Scala. Here are some good resources to keep learning:

[Twitter Scala School][72]  
[Typesafe][73]  
[This week in Scala][74]

[01]: https://www.coursera.org/course/progfun
[11]: http://en.wikipedia.org/wiki/Newton's_method#Square_root_of_a_number
[21]: http://en.wikipedia.org/wiki/Euclidean_algorithm
[22]: http://en.wikipedia.org/wiki/Currying
[31]: scala-class-hierarchy.png
[41]: http://en.wikipedia.org/wiki/Peano_axioms
[51]: http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List
[52]: http://en.wikipedia.org/wiki/Merge_sort
[53]: http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List
[61]: http://www.scala-lang.org/api/current/index.html#scala.collection.Seq
[71]: http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes
[72]: http://twitter.github.com/scala_school/
[73]: http://typesafe.com/
[74]: http://www.cakesolutions.net/teamblogs/topic/scala