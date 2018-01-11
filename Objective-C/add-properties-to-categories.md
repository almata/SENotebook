# Add properties to categories

Not a big fan of this kind of hacks, but having the option to add a property to a category can be extremely handful sometimes. This is probably one of the cleanest ways to do it:

```objc
@interface UIView (MyCategory)

@property (nonatomic, copy) NSString *myProperty;

@end
```

```objc
#import <objc/runtime.h> // additional required import

@implementation UIView (MyCategory)

- (void)setMyProperty:(NSString *)myProperty {
    objc_setAssociatedObject(self, @selector(myProperty), myProperty, OBJC_ASSOCIATION_COPY_NONATOMIC);
}

- (NSString *)myProperty {
    return objc_getAssociatedObject(self, @selector(myProperty));
}

@end
```

The second argument for both methods requires a `void *`. This is usually done by using the memory address of a `static const char` or similar, but using `@selector(myProperty)` removes the need for that extra constant.
