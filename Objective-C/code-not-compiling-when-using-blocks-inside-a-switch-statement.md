# Code not compiling when using blocks inside a switch statement

This is something I discovered some months ago related with blocks and `switch` statements. I had compiling problems with a quite simple code and drove me nuts for a while. I started with something trivial like this:

```objc
switch(i) {
    case 1:
        NSLog(@"Case 1");
        break;
    case 2:
        NSLog(@"Case 2");
        break;
    case 3:
        NSLog(@"Case 3");
        break;
    default:
        NSLog(@"Case whatever else"); 
        break;
}
```

This, of course, compiles and executes perfectly. But my code was just slightly more complex and use methods with blocks in some of the `switch` cases, like this:

```objc
switch(i) { 
    case 1:
        NSLog(@"Case 1");
        break;
    case 2:
        NSLog(@"Case 2");
        [UIView animateWithDuration:1.0
                         animations:^{
                             self.myView.frame = someFrame;
                         }];
        break;
    case 3:
        NSLog(@"Case 3");
        break; 
    default:
        NSLog(@"Case whatever else"); 
        break;
}
```

And this suddenly didn't compile. It shows an error message saying "Switch case is in protected scope". It seems the problem is the compiler doesn't know how to properly scope that block and its content. To solve this problem I only had to add some extra braces to explicitly scope the code inside the case:

```objc
switch(i) { 
    case 1:
        NSLog(@"Case 1");
        break;
    case 2: { // '{' to start scoped block
        NSLog(@"Case 2");
        [UIView animateWithDuration:1.0
                         animations:^{
                             self.myView.frame = someFrame;
                         }];
        } // '}' to end scoped block
        break;
    case 3:
        NSLog(@"Case 3");
        break; 
    default:
        NSLog(@"Case whatever else"); 
        break;
}
```

The brace closing the scoped block can be at the end of previous line, of course, I just didn't put it there in this example to make it clearer.
