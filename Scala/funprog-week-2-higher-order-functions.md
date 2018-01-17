# FunProg week 2: Higher order functions

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Tail recursion

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

## Higher order functions

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

## Anonymous functions

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

## Currying

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

[01]: https://www.coursera.org/course/progfun
[21]: http://en.wikipedia.org/wiki/Euclidean_algorithm
[22]: http://en.wikipedia.org/wiki/Currying
