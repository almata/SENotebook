# Filter arrays using blocks instead of predicates

According to the Wikipedia:

> In functional programming, filter is a higher-order function that processes a data structure (typically a list) in some order to produce a new data structure containing exactly those elements of the original data structure for which a given predicate returns the boolean value true.

There is a `filteredArrayUsingPredicate:` method in `NSArray` and a `filterUsingPredicate:` method in `NSMutableArray` (and similar methods in other collection classes). These methods use `NSPredicate` objects to filter the elements in the collection. But in some cases the cross between kind-of-SQL and kind-of-regular-expressions language used for `NSPredicate` can make creating a complex filter function difficult. However, it's possible to create a predicate using its `predicateWithBlock:` method (and so it's possible to filter a collection using a block):

```objc
NSArray *array = @[@1, @2, @3, @7, @8, @11, @14, @19, @20];

NSPredicate *evenNumbers = [NSPredicate predicateWithBlock:^BOOL(id evaluatedObject, NSDictionary *bindings) {
    NSNumber *number = (NSNumber *)evaluatedObject;
    return [number integerValue] % 2 == 0;
}];

NSArray *filteredArray = [array filteredArrayUsingPredicate:evenNumbers];
NSLog(@"%@", filteredArray); // (2, 8, 14, 20)
```

Actually the block definition can be changed to use `NSNumber *` so no additional cast operation is required:

```objc
NSArray *array = @[@1, @2, @3, @7, @8, @11, @14, @19, @20];

NSPredicate *evenNumbers = [NSPredicate predicateWithBlock:^BOOL(NSNumber *number, NSDictionary *bindings) {
    return [number integerValue] % 2 == 0;
}];

NSArray *filteredArray = [array filteredArrayUsingPredicate:evenNumbers];
NSLog(@"%@", filteredArray); // (2, 8, 14, 20)
```
