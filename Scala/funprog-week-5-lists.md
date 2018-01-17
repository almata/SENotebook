# FunProg week 5: Lists

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Lists

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

## Right associativity of operators ending in ":"

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

## List patterns

We can use the pattern matching technique we saw in previous chapters with lists. A pattern `Nil` will match an empty list. A pattern `p :: ps` will match a list with a `head` matching `p` and a `tail` matching `ps`. For example:

```scala
1 :: 2 :: xs  // list starting with 1 and then 2
x :: Nil      // list of length 1
List(x)       // list of length 1
List()        // empty List
Nil           // empty list
List(2 :: xs) // list with only one element, which is another list that starts with 2
```

## Example: sorting lists

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

## Some list methods

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

## Pairs and tuples

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

## Generics and type parameters

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

## Parameterization with Ordering

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

## Implicit parameters

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

## Higher order list functions

In previous chapters we talked about higher order functions and said that "in functional programming, functions can be passed as a parameter to another function and returned as a result as well, which provides a very flexible way to compose programs. The functions that take other functions as parameters or that return functions as results are called higher order functions (as opposite to first order functions, which are the standard functions that just work on simple data types). So, functions that operate on other functions (accept them as arguments) or return other functions are called higher order functions. The idea behind this is that when we see that a particular piece of code is repeated in different functions, we can break it out into a new function and then write higher order functions that use it. Or, seen from a different point of view, when a piece of logic within our function needs to behave differently in different situations, we can break it out into a higher order function that uses different functions in each situation. In fact,  we get a higher degree of granularity with less code."

Now that we are working with lists we can see several recurring patterns, like:

* transforming each element in a list in a certain way,
* retrieving a list of all elements satisfying a criterion,
* combining the elements of a list using an operator.

## Transformation of lists

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

## Getting sublists

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

## Reduction of lists

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

## foldRight and reduceRight

As seen, `foldLeft` and `reduceLeft` unfold on trees that lean to the left. This means that the operands are taken beginning from the left. Analogously, we have `foldRight` and `reduceRight` that do the opposite: produce trees which lean to the right, or take the operands beginning from the right.

With sums and products both versions return the same result, as they are associative and commutative, but with other operations they do not. For instance:

```scala
val myList = List(10.0, 5.0, 2.0)
myList reduceLeft (_ / _)  // 1.0
myList reduceRight (_ / _) // 4.0
```

[01]: https://www.coursera.org/course/progfun
[51]: http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List
[52]: http://en.wikipedia.org/wiki/Merge_sort
[53]: http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List
