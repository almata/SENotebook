# Quotes from _Effective Objective-C 2.0_

I've just finished reading the book _Effective Objective-C 2.0_ by Matt Galloway (Addison-Wesley, 2013). In my opinion, one of the best Objective-C books I've ever read. As its subtitle exposes, the book is a collection of 52 specific ways to improve our iOS skills. These 52 items can be read independently to learn a thing or two about a topic, although it surely can be read in standard linear order as well (which is what I've done).

More information: [http://www.informit.com/store/effective-objective-c-2.0-52-specific-ways-to-improve-9780133386967][1]

[1]: http://www.informit.com/store/effective-objective-c-2.0-52-specific-ways-to-improve-9780133386967

> Another reason for using a marker for private methods arises when considering changing a method name or signature. If the method is public, a change such as this should be thought about more carefully, since it might not be ideal to change the public API to a class. Thus, consumers of the class would have to update their code as well. However, if the method is an internal one, only the class's own code need change, with no effect on the public-facing API. Using a marker to indicate private methods means that this distinction is easy to see when making such a change.
>
> The prefix to use depends on your personal preference. Good choices of characters to include in a prefix are underscores and the letter p. My preference is for the prefix to be p_, since the p indicates "private," and the underscore gives a visual gap before the start of the method name.
>
> [...]
>
> Apple tends to use a single underscore to prefix its private methods. So you might think that it would be a good idea to follow Apple's cue and also use an underscore. However, this would be a potentially disastrous decision; if you did this in a subclass of an Apple-provided class, you could inadvertently override one of its methods. For this reason, Apple has documented that you should avoid using an underscore as the prefix.

The author seems to be right. This is what Apple says about naming private methods in its Coding Guidelines for Cocoa:

> In most cases, private method names generally follow the same rules as public method names. However, a common convention is to give private methods a prefix so it is easy to distinguish them from public methods. Even with this convention, the names given to private methods can cause a peculiar type of problem. When you design a subclass of a Cocoa framework class, you cannot know if your private methods unintentionally override private framework methods that are identically named.
>
> Names of most private methods in the Cocoa frameworks have an underscore prefix (for example, _fooData) to mark them as private. From this fact follow two recommendations.
>
> 1. Don't use the underscore character as a prefix for your private methods. Apple reserves this convention.
> 2. If you are subclassing a large Cocoa framework class (such as NSView or UIView) and you want to be absolutely sure that your private methods have names different from those in the superclass, you can add your own prefix to your private methods. The prefix should be as unique as possible, perhaps one based on your company or project and of the form "XX_". So if your project is called Byte Flogger, the prefix might be BF_addObject:
>
> Although the advice to give private method names a prefix might seem to contradict the earlier claim that methods exist in the namespace of their class, the intent here is different: to prevent unintentional overriding of superclass private methods.

> Remembering that exceptions are to be used for fatal errors only, an example of a scenario in which you should consider throwing an exception in your own classes is when creating an abstract base class that should be subclassed before being used. Objective-C has no language construct to say that a class is abstract, unlike some other languages. So the best way to achieve a similar effect is to throw an exception in any method that must be overridden in subclasses. Anyone who then tries to create an instance of the abstract base class and use it will get an exception thrown.

> Another thing to be aware of and avoid in dealloc methods is calling other methods. It is not ideal to have to call any other methods at all, because the object being deallocated is in a winding-down state. If the other method happens to perform work asynchronously or calls methods that themselves do, the object being deallocated could be completely dead by the time those methods finish doing their work. This can cause all sorts of problems and often results in an application crash because it calls back to tell the object that it has finished. If the object is dead, this call will fail.
>
> Also, the dealloc method is called on the thread in which the final release that caused the retain count to zero occurred. Some methods are required to be run in a certain thread, such as the main thread. If these methods are called from dealloc, there is no safe way to ensure that it is run on the correct thread. Any usual code to force it to be run on the correct thread is not at all safe, because the object is in a deallocating state, and the runtime has already started altering its internal data structures to indicate this.
>
> The avoidance of method calls in dealloc should also go for property accessors, which can be overridden and therefore themselves try to perform work that is unsafe to do during deallocation. Alternatively, the property may be being observed through Key-Value Observation (KVO), and the observer may try to do some work, such as attempting to retain the object, using the object that is being deallocated. Doing so would cause the runtime to get in a completely inconsistent state, and strange crashes would likely result.

> A common problem encountered when developing a Mac OS X or an iOS application that downloads images from the Internet is deciding what to do about caching them. A good first approach is to use a dictionary to store in memory images that have been downloaded, such that they don't need to be downloaded again if they are requested later. A naïve developer will simply use an NSDictionary (or rather a mutable one) because that's a commonly used class. However, an even better class, called NSCache, is also part of the Foundation framework and has been designed exactly for this task.
>
> The benefit of NSCache over an NSDictionary is that as system memory becomes full, the cache is automatically pruned. When using a dictionary, you often end up having to write pruning code yourself by hooking into system notifications for low memory. However, NSCache offers this automatically; because it is part of the Foundation framework, it will be able to hook in deeper to the system than you could yourself. An NSCache will also prune the least recently used objects first. Writing the code to support this yourself with a dictionary would be quite complex.
