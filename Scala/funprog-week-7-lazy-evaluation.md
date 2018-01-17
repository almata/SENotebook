# FunProg week 7: Lazy evaluation

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Streams

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

## Lazy evaluation

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

## Computing with infinite sequences

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

## Example: the sieve of Eratosthenes

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

## Back to square roots

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

## Case study: the water pouring problem

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

## Epilogue

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
[71]: http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes
[72]: http://twitter.github.com/scala_school/
[73]: http://typesafe.com/
[74]: http://www.cakesolutions.net/teamblogs/topic/scala