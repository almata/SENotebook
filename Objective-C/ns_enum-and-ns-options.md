# NS\_ENUM and NS\_OPTIONS

This is the standard way to define an enumeration in C:

```objc
enum AMGMonth {
    AMGMonthJanuary = 1, // optional to make it start at 1, otherwise it starts at 0
    AMGMonthFebruary,
    ...
    AMGMonthDecember
};
```	

Or when some specific values are required (i.e. for options to be combined using the bitwise OR operator):

```objc
enum AMGResizing {
    AMGResizingNone            = 0,      // 00000000
    AMGResizingFlexibleWidth   = 1 << 0, // 00000001
    AMGResizingFlexibleHeight  = 1 << 1, // 00000010
    AMGResizingFlexibleUnicorn = 1 << 2  // 00000100
};
```

Then the new types can be used this way:

```objc
enum AMGMonth month = AMGMonthFebruary;
enum AMGResizing option = AMGResizingFlexibleWidth | AMGResizingFlexibleHeight;
```

This syntax is usually simplified using `typedef`:

```objc
enum AMGMonth {
    AMGMonthJanuary = 1,
    AMGMonthFebruary,
    ...
    AMGMonthDecember
};
typedef enum AMGMonth AMGMonth;

AMGMonth month = AMGMonthFebruary;
```

And it's also possible to set the type to be used (in this example, `NSInteger`):

```objc
typedef enum {
    AMGResizingNone            = 0,
    AMGResizingFlexibleWidth   = 1 << 0,
    AMGResizingFlexibleHeight  = 1 << 1,
    AMGResizingFlexibleUnicorn = 1 << 2
};
typedef NSInteger AMGResizing;
```

But instead of all this, introduced with iOS 6 there are two new constructions to do the same:

```objc
typedef NS_ENUM(NSUInteger, AMGMonth) {
    AMGMonthJanuary = 1, 
    AMGMonthFebruary,
    ...
    AMGMonthDecember
};

typedef NS_OPTIONS(NSUInteger, AMGResizing) {
    AMGResizingNone            = 0,
    AMGResizingFlexibleWidth   = 1 << 0,
    AMGResizingFlexibleHeight  = 1 << 1,
    AMGResizingFlexibleUnicorn = 1 << 2
};

AMGMonth month = AMGMonthFebruary;
AMGResizing option = AMGResizingFlexibleWidth | AMGResizingFlexibleHeight;
```
