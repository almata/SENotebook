# Class clusters to group a number of private concrete subclasses under a public abstract superclass

According to Apple:

> Class clusters are a design pattern that the Foundation framework makes extensive use of. Class clusters group a number of private concrete subclasses under a public abstract superclass. The grouping of classes in this way simplifies the publicly visible architecture of an object-oriented framework without reducing its functional richness. Class clusters are based on the [Abstract Factory][1] design pattern.

Say we want a public abstract superclass `AMGContainer` that internally can be implemented using a [linked list][2], a [doubly linked list][3] or an [AVL tree][4], but we don't want the particularities of each one to be public.

Our public interface should be something like this:

```objc
typedef NS_ENUM(NSUInteger, AMGContainerType) { 
    AMGContainerTypeLinkedList, 
    AMGContainerTypeDoublyLinkedList, 
    AMGContainerTypeAvlTree,
};

@interface AMGContainer : NSObject

@property (nonatomic, readonly) NSUInteger count;

// Helper for creating AMGContainer objects
+ (AMGContainer *)containerWithType:(AMGContainerType)type;

// Some methods that all containers will have
- (void)addElement:(id)element;
- (void)removeElement:(id)element;

@end
```

```objc
@implementation AMGContainer

+ (AMGContainer *)containerWithType:(AMGContainerType)type {
    switch (type) {
        case AMGContainerTypeLinkedList:
            return [AMGContainerTypeLinkedList new];
        case AMGContainerTypeDoublyLinkedList:
            return [AMGContainerTypeDoublyLinkedList new];
        case AMGContainerTypeAvlTree:
            return [AMGContainerTypeAvlTree new];
    }
    return nil;
}

- (void)addElement:(id)element { /* subclasses implement this */ }
- (void)removeElement:(id)element { /* subclasses implement this */ }

@end
```

And each of the private subclasses will have to implement the data structure and the public methods:

```objc
@interface AMGContainerLinkedList : AMGContainer
@end
```

```objc
@implementation AMGContainerLinkedList

- (id)init { /* whatever is needed, at least creating the data structure itself */ }
- (void)addElement:(id)element {...}
- (void)removeElement:(id)element {...}

@end
```

[1]: http://en.wikipedia.org/wiki/Abstract_factory_pattern
[2]: http://en.wikipedia.org/wiki/Linked_list
[3]: http://en.wikipedia.org/wiki/Doubly_linked_list
[4]: http://en.wikipedia.org/wiki/AVL_tree
