# FunProg week 1: Functions & evaluations

_In September 2012, I enrolled in the course [Functional Programming Principles in Scala][01] given by Martin Odersky at Coursera. In that course I learnt about functional programming and how it can be effectively combined with object-oriented programming. These are all my compiled notes from that course._

## Programming Paradigms

In general terms, we can distinguish three different programming paradigms:

* imperative programming (i.e. C, Java)
* functional programming (i.e. Scala or Haskell)
* logic programming (i.e. Prolog)

In imperative programming, we are used to modify mutable variables, use assignments and use control structures as if-then-else, loops, break, continue and return. In functional programming, nonetheless, we avoid mutations and totally concentrate on functions, which is why we say that in functional programming languages functions are first-class citizens. That means, for instance, that functions can be defined anywhere, including inside other functions, and that they can be passed to other functions as parameters or returned as results. 

One of the important benefits of functional programming is that it's specially good for exploiting parallelism and concurrency for multicore and cloud computing.

## Evaluation of expressions: call-by-value and call-by-name

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

## Conditional expressions

We have conditional expressions if-else in Scala, but it's important to note that they are used for expressions, not for statements. So an if-else expression in Scala always returns an expression:

```scala
def abs(x: Int) = if (x >= 0) else -x
```

The part `x >= 0` is called a predicate and needs to be of type `Boolean`. It can use the standard operators `!`, `&&`, `||`, `<=`, `==`, `!=`, etc.

## def vs val

Both `def` and `val` are used to set definitions. But while `def` makes the right hand side be evaluated on each use, `val` only evaluates once, at the point of the definition itself. Let's see that with an example:

```scala
val x = 2 // x will always be 2
def y = square(x)
val z = square(x) // z will always be 4
```

From now on, every time we use `y` it will evaluate `square(x)` to get a value. On the other side, every time we use `z` it will directly refer to 4, without evaluating anything.

In functional programming we don't use variables the same way we do in imperative programming. Variables have to be understood simply as aliases for expressions (so we don't have to type everything on one line when writing a long expression). But these variables can't be modified, so in some sense they can be considered as final or constants.

## Example: Newton's method to calculate square roots

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

## Implicit or explicit return type

In the previous example there are some functions (like `isGoodEnough` that have an explicit return type, while others (like `squareRoot` just have it implicitly). This is because some functions are recursive (they call themselves) while others are not. Recursive functions always need to explicitly declare the return type. Non-recursive functions can declare it as well (and it's a good idea as it works as source code documentation), but it's not a requirement.

## Nested functions

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

## Blocks

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

## Functional programming and unit testing

In functional programming, no function can ever cause side effects. That means a function can never modify a value outside of its scope and so the only effect of evaluating a function is its return value, and the only things that affect that return value are the function's arguments.

As a consequence of this, we can test every function in a program only worrying about its arguments. We don't need any `setUp` or `tearDown`. All we need to do is pass arguments that represent edge cases and check the results. If every function in a program passes all unit tests we can be a lot more confident about quality of our software than doing the same when using an imperative language.

[01]: https://www.coursera.org/course/progfun
[11]: http://en.wikipedia.org/wiki/Newton's_method#Square_root_of_a_number
