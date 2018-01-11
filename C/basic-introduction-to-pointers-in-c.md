# Basic introduction to pointers in C

For most Computer Science students, pointers in C is usually a complex subject to fully understand, when it really shouldn't be as long as it's quite straightforward when it's well explained. So that's what this article tries to do.

## Introduction

Put it short, a pointer is nothing else than a memory address. So, any time we talk about pointers we are actually talking about specific memory addresses where there are some contents we are interested in.

Let's start with a very basic code where we just declare an integer variable `i`, assign a value to it and print some result in our standard output:

```c
int main(void) {
    int i = 42;
    printf("Our variable i has value %i\n", i);
    return 0;
}

$ Our variable i has value 42
```

This variable is, of course, living in some specific place in our computer's memory. That specific place is a memory address (so, a pointer) that we can actually find out using the `&` operator:

```c
int main(void) {
    int i = 42;
    printf("Our variable i has value %i\n", i);
    printf("Our variable i lives in memory address %p\n", &i);
    return 0;
}

$ Our variable i has value 42
$ Our variable i lives in memory address 0x7fff55f18ba4
```

Please note that while `%i` works to format an integer in `printf` function, `%p` is required to format a pointer.

Which type is that memory address value? Well, it's a pointer, and more specifically it's a pointer to an integer, because we know an integer is what's being stored in that memory address. Although this explanation takes an integer example, it's of course the same whatever data type we consider.

When we want to say "a pointer to something" we use a special notation `something*`. So, in our code we can write this:

```c
int main(void) {
    int i = 42;
    int* pi = &i;
    printf("Our variable i has value %i\n", i);
    printf("Our variable i lives in memory address %p\n", &i);
    printf("Our pointer to integer pi has value %p\n", pi);
    return 0;
}

$ Our variable i has value 42
$ Our variable i lives in memory address 0x7fff5f4b8ba4
$ Our pointer to integer pi has value 0x7fff5f4b8ba4
```

The expression `int* pi` can also be written as `int * pi` or as `int *pi`. It really doesn't matter at all if the space is after the asterisk, before it or after and before. In all cases the type is `int*` (pointer to an integer).

## Usage of pointers

At this point we have a little space in memory where there is a value (42) and we also know the address of that little space in memory (0x7fff5f4b8ba4). Knowing this address will be helpful when we want to tell some other piece of code "hey, you probably want to set a value for an integer for me, just use this memory address for that and I'll check the content later". 

Let's see an example for this. Imagine we have an odd function that tells if a given number is even or not (returns 1 when it's even, 0 otherwise) but at the same time it adds 10 to that given number when it's even.

We could try to do it this way:

```c
int isEven(int i) {
    if (i % 2 == 0) {
        i += 10;
        return 1;
    } else {
        return 0;
    }
}

int main(void) {
    int i = 42;
    printf("Initial value of i: %i\n", i);
    int result = isEven(i);
    printf("Is i even (1 = yes, 0 = no): %i\n", result);
    printf("Final value of i: %i\n", i);
    return 0;
}

$ Initial value of i: 42
$ Is i even (1 = yes, 0 = no): 1
$ Final value of i: 42
```

But it obviously doesn't work because `isEven` function increments the value of a `i` variable whose scope is just that `isEven` function. It's not changing the `i` variable in `main`. These two variables are actually in different memory addresses, as we could check easily:

```c
int isEven(int i) {
    printf("Memory addres of i in isEven: %p\n", &i);
    if (i % 2 == 0) {
        i += 10;
        return 1;
    } else {
        return 0;
    }
}

int main(void) {
    int i = 42;
    printf("Memory addres of i in main: %p\n", &i);
    printf("Initial value of i: %i\n", i);
    int result = isEven(i);
    printf("Is i even (1 = yes, 0 = no): %i\n", result);
    printf("Final value of i: %i\n", i);
    return 0;
}

$ Memory addres of i in main: 0x7fff53e7dba4
$ Initial value of i: 42
$ Memory addres of i in isEven: 0x7fff53e7db8c
$ Is i even (1 = yes, 0 = no): 1
$ Final value of i: 42
```

So, as we said, in this situation we can use a pointer to an integer and tell `isEven` "hey, I know you maybe want to change the value for the integer I'm sending to you, if so put it in this memory address so that I can check it later". In code:

```c
int isEven(int* pi) {
    printf("Memory addres of i in isEven: %p\n", pi);
    if (*pi % 2 == 0) {
        *pi += 10;
        return 1;
    } else {
        return 0;
    }
}

int main(void) {
    int i = 42;
    printf("Memory addres of i in main: %p\n", &i);
    printf("Initial value of i: %i\n", i);
    int result = isEven(&i);
    printf("Is i even (1 = yes, 0 = no): %i\n", result);
    printf("Final value of i: %i\n", i);
    return 0;
}

$ Memory addres of i in main: 0x7fff50dc6ba4
$ Initial value of i: 42
$ Memory addres of i in isEven: 0x7fff50dc6ba4
$ Is i even (1 = yes, 0 = no): 1
$ Final value of i: 52
```

If we look at the output, we can see that `isEven` still returns the right value (a number 1, as 42 is even) but now it also changes the value for variable `i` as both `main` and `isEven` work with the value at the same memory address.

But, hey, we have a new syntax there. What does that asterisk in `*pi` mean inside the function? In some sense it's the opposite to the `&` operator, and we call it dereferencing. With `&`, we get the memory address where a real value is stored. With `*`, we get access to the real value behind a memory address.

We actually could create a bizarre expression like this `*(&(*(&i)))` to say "get the real value behind the memory address of the real value behind the memory address where `i` lives". And, of course, we'd get exactly the same as just writing `i`. 

And we could also define something like `int ***pppi` which would read as "a pointer to a pointer to a pointer to an integer". That would be a memory address where we store another memory address where we store another memory address where we store an integer value.

## Pointers and arrays

There is a strong relationship between arrays and pointers. When we declare an array of N elements, we are taking a space in memory where N elements are to be stored contiguously. That is, in memory addresses one next to the other. For instance:

```c
int main(void) {
    int arr[] = {4, 8, 15, 16, 23, 42};
    int i;
    for (i = 0; i < 6; i++) {
        printf("Memory address for element %i: %p\n", i, &arr[i]);
    }
    return 0;
}

$ Memory address for element 0: 0x7fff55b4cb90
$ Memory address for element 1: 0x7fff55b4cb94
$ Memory address for element 2: 0x7fff55b4cb98
$ Memory address for element 3: 0x7fff55b4cb9c
$ Memory address for element 4: 0x7fff55b4cba0
$ Memory address for element 5: 0x7fff55b4cba4
```

We can see that ~90, ~94, ~98, ~9c, ~a0 and ~a4 are contiguous memory addresses separated by 4 (because each integer in my machine is represented using 4 bytes). The memory address for the first element (`arr[0]`) can also be accessed simply as `arr` instead of `&arr[0]`.  

That said, we could set a pointer to an integer to store the memory address for the first element in the array (or, said in an easier way, just a pointer to the array):

```c
int main(void) {
    int arr[] = {4, 8, 15, 16, 23, 42};
    int* parr = arr;
    printf("Memory address for array's element 0: %p\n", &arr[0]);
    printf("Memory address for pointer to array: %p\n", parr);
    return 0;
} 

$ Memory address for array's element 0: 0x7fff5e84db90
$ Memory address for pointer to array: 0x7fff5e84db90
```

What's interesting now is that as `parr` is a pointer to integers and the compiler knows that each integer is stored using 4 bytes, it's possible to use it to access the elements of the array. For example we could get the memory address for the forth element in the array (so index 3, as arrays are zero based) just saying `parr + 3`. And we could change that value using the pointer:

```c
int main(void) {
    int arr[] = {4, 8, 15, 16, 23, 42};
    int* parr = arr;
    printf("Memory address for array's element 0: %p\n", &arr[0]);
    printf("Memory address for pointer to array: %p\n", parr);
    *(parr + 3) = 1616; // it was 16
    printf("Memory address for array's element 3: %p\n", &arr[3]);
    printf("Memory address for pointer to array's element 3: %p\n", parr + 3);
    printf("Real value for array's element 3 (using array): %i\n", arr[3]);
    printf("Real value for array's element 3 (using pointer): %i\n", *(parr + 3));  
    return 0;
}

$ Memory address for array's element 0: 0x7fff5078fb90
$ Memory address for pointer to array: 0x7fff5078fb90
$ Memory address for array's element 3: 0x7fff5078fb9c
$ Memory address for pointer to array's element 3: 0x7fff5078fb9c
$ Real value for array's element 3 (using array): 1616
$ Real value for array's element 3 (using pointer): 1616
```

This is possible only because `parr` is defined as `int*` and so when we say something like `parr + 1` the compiler knows it has to add 4 to `parr`'s memory address. 

## Pointers to functions

The code for a function is also stored in some place in the memory (where else could it be?). So we actually could get the exact memory address where a function's code starts. And we then could make use of that to pass a function (using that memory address, so that pointer) to another function that requires a function with the characteristics of the former. All this sounds worse than it actually is, so let's see that with an example.

Imagine we have a function that gets an integer and firstly makes a lot of checks on it, secondly does some stuff with it (i.e. doubles it, or multiplies by itself, or negates its value, or whatever else) and finally returns if the final result is even or odd. The pseudocode could be as follows:

```c
int isEven(int i) {
    // lots of checks on i
    i = do_stuff(i);
    return (i % 2 == 0);
}
```

Of course this function could (and should) be organized differently, but let's go with it for the sake of this example. 

The problem now is that we actually don't have a `do_stuff` function. What we have are some functions (`doubles`, `squares`, `negates` and so on) and we want that `isEven` uses the one we tell it to use and considers that as the stuff to do with the integer. Something like this:

```c
int isEven(int i, function concrete_stuff_to_do) {
    // lots of checks on i
    i = concrete_stuff_to_do(i);
    return (i % 2 == 0);
}
```

So it's obvious that we need to pass a function as an argument to another function. That's where function pointers are really useful. Let's see how this works in real code:

```c
int doubles(int i) {
    return i * 2; // assuming it's still an int
}

int squares(int i) {
    return i * i; // assuming it's still an int
}

int negates(int i) {
    return -i; // assuming it's still an int
}

int isEven(int i, int(*stuff_to_do)(int)) {
    // lots of checks on i
    i = stuff_to_do(i);
    return (i % 2 == 0);
}

int main(void) {
    int i = 3;
    int result;

    // doubles(3) isEven?
    int (*doubles_ptr)(int); // declaring the function pointer
    doubles_ptr = &doubles;  // referencing to doubles function (the & is optional)
    result = isEven(i, doubles_ptr);
    printf("doubles(3) isEven? (1 = yes, 0 = no): %i\n", result);   

    // squares(3) isEven?
    int (*squares_ptr)(int); // declaring the function pointer
    squares_ptr = &squares;  // referencing to squares function (the & is optional)
    result = isEven(i, squares_ptr);
    printf("squares(3) isEven? (1 = yes, 0 = no): %i\n", result);   

    // negates(3) isEven?
    int (*negates_ptr)(int); // declaring the function pointer
    negates_ptr = &negates;  // referencing to negates function (the & is optional)
    result = isEven(i, negates_ptr);
    printf("negates(3) isEven? (1 = yes, 0 = no): %i\n", result);       

    return 0;
}

$ doubles(3) isEven? (1 = yes, 0 = no): 1
$ squares(3) isEven? (1 = yes, 0 = no): 0
$ negates(3) isEven? (1 = yes, 0 = no): 0
```

There are some important things to notice. One of them is how we express a function pointer. This could be a very simple first example where we declare a pointer (named `foo`) to a function that takes an integer and returns nothing:

```c
void (*foo)(int);
```

And this is another example where we declare a pointer (named `foo` again) to a function that takes a pointer to an integer and returns a pointer to a float:

```c
float *(*foo)(int *);
```

That's what we've done in the previous code for example at this point:

```c
int (*doubles_ptr)(int); // declaring the function pointer
doubles_ptr = &doubles;  // referencing to doubles function (the & is optional)
```

The second thing important in this code is how we tell a function to get another function as a parameter. For instance, in this example we have a function called `foo` that receives some standard parameters and a final parameter which is a pointer (called `bar`) to a function that takes a float and an integer and returns an integer:

```c
void foo(<# some standard parameters #>, int(*bar)(float, int));
```

In the implementation of this `foo` function we could use the `bar` function the way we need, as long as we pass a float and integer to it (and we'll get an integer as a result). That's very similar to what we've done in our `isEven` function.

Of course function pointers have more interesting and useful uses, as setting up listener or callback functions to be invoked when a particular event happens. This example just tried to show how it works, not a real scenario where it should be used. As always, any feedback via email or Twitter will be highly appreciated.