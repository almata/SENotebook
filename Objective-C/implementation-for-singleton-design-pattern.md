# Implementation for Singleton design pattern

According to [Apple](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Singleton.html):

> A singleton class returns the same instance no matter how many times an application requests it. A typical class permits callers to create as many instances of the class as they want, whereas with a singleton class, there can be only one instance of the class per process. A singleton object provides a global point of access to the resources of its class. Singletons are used in situations where this single point of control is desirable, such as with classes that offer some general service or resource.
>
> You obtain the global instance from a singleton class through a factory method. The class lazily creates its sole instance the first time it is requested and thereafter ensures that no other instance can be created. A singleton class also prevents callers from copying, retaining, or releasing the instance.

Apparently, for some people and for some time now singletons are the very devil. However, I still think they are an excellent design pattern to use in the appropriate situations. And this seems to be the best possible implementation:

```objc
+ (instancetype)sharedInstance {
    static id sharedInstance = nil; 
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[self alloc] init];
    });
    return sharedInstance; 
}
```

This code ensures that the block is executed once and only once (the first time the method is called) and is entirely thread safe. Moreover, `dispatch_once()` is more efficient than other heavyweight synchronization mechanisms like `@synchronized`, which need to acquire a lock every time the code runs.

In principle, the block shouldn't be able to assign the value for `sharedInstance` (it should require a `__block` qualifier). But it can, because `sharedInstance` has been declared as `static`, which has the same effect but even stronger: `__block` promotes the lifetime of its variable to live as long as the block, `static` promotes the lifetime of its variable to live as long as the program itself.