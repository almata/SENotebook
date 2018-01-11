# Equality and isEqual: in NSObject subclasses

Three main things are required when implementing custom `isEqual:` for any subclass of NSObject:

1. Implementing a new `isEqualTo__ClassName__:` method that performs the meaningful value comparison.
2. Overriding `isEqual:` to firstly check objects identity, then classes equality and finally objects equality.
3. Overriding `hash` method, usually by XOR'ing the hash values of some or all critical properties.

In code:

```objc
@interface AMGBook

@property (nonatomic, copy) NSString *title;
@property (nonatomic, copy) NSString *author;
@property (nonatomic, strong) NSDate *publicationDate;

- (BOOL)isEqualToBook:(AMGBook *)book;

@end
```

```objc
@implementation AMGBook

- (BOOL)isEqualToBook:(AMGBook *)book {
    if (!book) {
        return NO;
    }
    return [self.title isEqualToString:book.title]
        && [self.author isEqualToString:book.author]
        && [self.publicationDate isEqualToDate:book.publicationDate];
}

- (BOOL)isEqual:(id)object {
    if (self == object) {
        return YES;
    }
    if (![object isKindOfClass:[AMGBook class]]) {
        return NO; 
    }
    return [self isEqualToBook:(AMGBook *)object];
}

- (NSUInteger)hash {
    return [self.title hash] ^ [self.author hash] ^ [self.publicationDate hash];
}

@end
```
