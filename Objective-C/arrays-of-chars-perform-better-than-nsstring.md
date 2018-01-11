# Arrays of chars perform better than NSString

In an iOS project I developed some time ago, I needed to create lots of strings in one of my algorithms. And by lots of I mean some millions. Those strings were actually alive for a very short time, but just creating them was too much time-consuming, so I needed a way to improve that step.

The original way I was creating all those strings was with `NSString` and one of its standard class methods, as follows:

```objc
[NSString stringWithFormat:@"My number is %i", myNumber]
```

That works perfectly fine most of the time. But it can become a bottleneck when creating and destroying a huge number of strings. At this point I realized that, as long as Objective-C is a strict superset of C I could use strings in the standard way in C, that is using `char[]`, as shown here:

```objc
#define STRING_SIZE 22
char c[STRING_SIZE];
sprintf(c, "My number is %i", i);
```

The execution was certainly faster with this last approach. But faster is not accurate enough, so I created a little Objective-C project to test both options and compare how fast (or slow) they were. In this project there are two classes: `AMGStringInObjectiveC` and `AMGStringInC`, both containing only a property/variable of type `NSString` or `char[]`.

This is the code for the first (using `NSString`):

```objc
// AMGStringInObjectiveC.h

#import <Foundation/Foundation.h>

@interface AMGStringInObjectiveC : NSObject

- (id)initWithString:(NSString *)string;

@end

// AMGStringInObjectiveC.m

#import "AMGStringInObjectiveC.h"

@interface AMGStringInObjectiveC() 
@property (nonatomic, copy) NSString *myString; 
@end

@implementation AMGStringInObjectiveC

- (id)init {
    return [self initWithString:nil];
}

- (id)initWithString:(NSString *)string {
    self = [super init];
    if (self) {
        _myString = string;
    }
    return self;
}
@end
```

And this is the code for the second (using `char[]`):

```objc
// AMGStringInC.h

#import <Foundation/Foundation.h>

#define STRING_SIZE 22

@interface AMGStringInC : NSObject

- (id)initWithString:(char *)string; 

@end

// AMGStringInC.m

#import "AMGStringInC.h"

@interface AMGStringInC() {
    char myString[STRING_SIZE];
}
@end

@implementation AMGStringInC

- (id)init {
    return [self initWithString:NULL]; 
}

- (id)initWithString:(char *)string {
    self = [super init];
    if (self) {
        strcpy(myString, string);
    }
    return self;
} 

@end
```

And then I created a battery of tests, checking execution time for 100,000, 500,000, 1,000,000, 5,000,000 and 20,000,000 strings and running each test five times to get the arithmetic mean and the standard deviation. This was the code used:

```objc
// For the NSString option
NSMutableArray *array = [NSMutableArray new];
for (int i = 0; i < NUM_STRINGS; i++) {
    [array addObject:[[AMGStringInObjectiveC alloc] initWithString:[NSString stringWithFormat:@"My number is %i", i]]]; 
} 

// For the char[] option
NSMutableArray *array = [NSMutableArray new];
for (int i = 0; i < NUM_STRINGS; i++) {
    char c[STRING_SIZE];
    sprintf(c, "My number is %i", i);
    [array addObject:[[AMGStringInC alloc] initWithString:c]];
}
```

Of course this isn't a valid method to check execution times. We would need much deeper tests, but it's enough to get an idea. These were the results in milliseconds for the `NSString` option:

```
Elapsed time for 100000: 0.3437 
Elapsed time for 100000: 0.3448 
Elapsed time for 100000: 0.3404 
Elapsed time for 100000: 0.3602 
Elapsed time for 100000: 0.3424 
Mean execution time = 0.346 +/- 0.007

Elapsed time for 500000: 1.6950 
Elapsed time for 500000: 1.7129 
Elapsed time for 500000: 1.6920 
Elapsed time for 500000: 1.7163 
Elapsed time for 500000: 1.7265 
Mean execution time = 1.709 +/- 0.013

Elapsed time for 1000000: 3.4524 
Elapsed time for 1000000: 3.4038 
Elapsed time for 1000000: 3.3992 
Elapsed time for 1000000: 3.3813 
Elapsed time for 1000000: 3.4293 
Mean execution time = 3.413 +/- 0.025

Elapsed time for 5000000: 16.9836 
Elapsed time for 5000000: 16.9589 
Elapsed time for 5000000: 17.2482 
Elapsed time for 5000000: 17.2215 
Elapsed time for 5000000: 17.2685 
Mean execution time = 17.136 +/- 0.136

Elapsed time for 20000000: 68.1141 
Elapsed time for 20000000: 68.1765 
Elapsed time for 20000000: 67.9571 
Elapsed time for 20000000: 68.4114
Elapsed time for 20000000: 68.6366 
Mean execution time = 68.259 +/- 0.239
```

And these are the results for the `char[]` option:

```
Elapsed time for 100000: 0.1110 
Elapsed time for 100000: 0.0993 
Elapsed time for 100000: 0.0973 
Elapsed time for 100000: 0.1076 
Elapsed time for 100000: 0.0928 
Mean execution time = 0.102 +/- 0.007

Elapsed time for 500000: 0.4479 
Elapsed time for 500000: 0.6306 
Elapsed time for 500000: 0.5551 
Elapsed time for 500000: 0.6215 
Elapsed time for 500000: 0.6227 
Mean execution time = 0.576 +/- 0.069

Elapsed time for 1000000: 0.9002 
Elapsed time for 1000000: 1.1771 
Elapsed time for 1000000: 1.1597 
Elapsed time for 1000000: 1.1182 
Elapsed time for 1000000: 1.0287 
Mean execution time = 1.077 +/- 0.102

Elapsed time for 5000000: 4.5200 
Elapsed time for 5000000: 4.8681 
Elapsed time for 5000000: 4.7976 
Elapsed time for 5000000: 4.7932 
Elapsed time for 5000000: 4.7769 
Mean execution time = 4.751 +/- 0.120

Elapsed time for 20000000: 18.0116 
Elapsed time for 20000000: 18.7309 
Elapsed time for 20000000: 18.5369 
Elapsed time for 20000000: 18.2186 
Elapsed time for 20000000: 18.3700 
Mean execution time = 18.374 +/- 0.249
```

For 20 million strings the `NSString` approach takes 68 seconds for just 18 using `char[]` instead. Similar performance ratios with the other tests.

In fact using `char[]` strings turns out to be between a 66% and a 73% faster than using the standard `NSString`. Nonetheless I'm not saying it's a good idea to use `char[]` in general. Absolutely not. `NSString` is the right choice in the vast majority of cases. But it's good to bear in mind that at the end of the day Objective-C is a superset of C and so any code that works efficiently in C will also work in Objective-C and that can be used to our advantage.
