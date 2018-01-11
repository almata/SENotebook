# Stack blocks vs heap blocks

Blocks, by default, are allocated on the stack and so they are valid only within the scope in which they are defined. That makes the following code pretty dangerous, as the block is defined in a scope that no longer exists when the block is called:

```objc
void (^block)();

if ( /* some condition */ ) {
    block = ^{ /* do something */ };
} else {
    block = ^{ /* do something else */ };
}

block();
```

A good option to solve this is to force blocks to be allocated on the heap. This way, they can be used outside the scope in which they are defined (as it no longer matters if the piece of stack for that scope has been reused or not). We can achieve this simply by sending the `copy` message to them. So, the right and safe way to write previous code would be as follows:

```objc
void (^block)();

if ( /* some condition */ ) {
    block = [^{ /* do something */ } copy];
} else {
    block = [^{ /* do something else */ } copy];
}

block();
```
