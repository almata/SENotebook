# Assign a value to a variable with a block of code

Dominik Wagner recently wrote [this post][01] showing how he uses blocks of code (not to be mistaken for [usual meaning of blocks in Objective-C][02]) to get a value that he assigns to a variable. 

[01]: http://cocoa-dom.tumblr.com/post/56517731293/new-thing-i-do-in-code
[02]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Blocks/Articles/00_Introduction.html

What Dominik Wagner suggests is to stop using these constructions:

```objc
// self.myView
CGRectframe = self.myView.frame;
frame.size.width += 100.0;
frame.origin.x += 50.0;
frame.origin.y += 25.0;
self.myView.frame = frame;

// self.myButton
UIButton *button = [[UIButton alloc] initWithFrame:CGRectMake(100.0,300.0,50.0,50.0)];
button.backgroundColor = [UIColor orangeColor];
[button addTarget:self action:@selector(myAction:) forControlEvents:UIControlEventTouchUpInside];
[self.view addSubview:button];
self.myButton = button;
```

And use these instead:

```objc
// self.myView
self.myView.frame = ({
    CGRect frame = self.myView.frame;
    frame.size.width += 100.0;
    frame.origin.x += 50.0;
    frame.origin.y += 25.0;
    frame;
});

// self.myButton
self.myButton = ({
    UIButton *button = [[UIButton alloc] initWithFrame:CGRectMake(100.0, 300.0, 50.0, 50.0)];
    button.backgroundColor = [UIColor orangeColor];
    [button addTarget:self action:@selector(myAction:) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:button];
    button;
});
```

The major benefits from this approach seem to be:

* The assignment happens at the first line and not at the end and this clearly shows what that block of code does. Probably true, but if I sometimes forget to do the last assignment `self.myView.frame = frame;` I think I'd forget even more that last `frame;` line. 
* The stack variables (`frame`, `button`, etc.) don't mess with other code in the same method, so it's possible to reuse same names again and again. Totally true, that's the part I like the most, I often need to use `frameView`, `frameButton` and so on (or similar with number suffixes or whatever).
* It kinds of encapsulate some standalone functionality in a single block of code. That's also great actually.

To be fair I haven't decided yet if I'm going to make use of this construction, but maybe I'll give it a try.
