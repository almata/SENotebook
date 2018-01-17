# FunProg week 6: Collections

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Vectors

We have seen that lists are linear: access to the first element is much faster than access to the middle or end of a list. Vectors, on the other hand, have more evenly balanced access patterns.

A vector is constructed using an array of maximum 32 elements. If it needs to contain more than 32 elements, it becomes an array of 32 pointers to 32 arrays of 32 elements. And so on -adding levels- until there is space for all the elements. So a depth of six can handle around a billion elements.

Access to random elements in a vector is related to its depth, so obviously it's very efficient. Lists are a better choice when we use a pattern where we use a list's head and a tail. Otherwise vectors are generally a better choice.

We create vectors in a similar way to lists:

```scala
val nums = Vector(1, 2, 3, -4)
val people = Vector("Bob", "James", "Peter")
```

And they support the same operations as lists, with the exception of `::` (but we can use `x +: xs` or `xs :+ x` instead to create a new vector with a new element at the beginning or the end of previous vector). Please note that vectors are immutable, so when we say we modify them we are actually creating a new one. 

## Ranges

Ranges are another simple kind of sequence that represent a sequence of evenly spaced integers using `to` (inclusive), `until` (exclusive) and `by` (to determine step value). Ranges are represented as single objects with three fields: lower bound, upper bound and step value.

```scala
val r: Range = 1 until 5 // 1, 2, 3, 4
val s: Range = 1 to 5    // 1, 2, 3, 4, 5
1 to 10 by 3             // 1, 4, 7, 10
6 to 1 by -2             // 6, 4, 2
```

## Operations on sequences

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

## Some examples

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

## Collection hierarchy

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

## Combinatorial search

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

## For expressions

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

## Sets

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

## N queens problem

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

## Queries with for

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

## For expressions and higher order functions

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

## Maps

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

## The Option type

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

## OrderBy and GroupBy

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

## Map example: Polynom

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

## Example: Translating phone numbers into sentences

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

## Final consideration on collections

Now that we've seen some Scala immutable collections it's a good time to sumarize some of their characteristics:

* easy to use: few steps to do the job,
* concise: one word replaces a whole loop,
* safe: type checker is really good at catching errors,
* fast: collection operations are tuned, can be parallelized,
* universal: one vocabulary to work on all kinds of collections.

This makes them a very attractive tool for software development.

[01]: https://www.coursera.org/course/progfun
[61]: http://www.scala-lang.org/api/current/index.html#scala.collection.Seq
