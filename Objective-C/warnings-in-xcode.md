# Warnings in Xcode

_This information is valid as of May 2014. Things may have changed since then._

A quick way to enable all possible warnings in Xcode consists in going to Build Settings > Other Warning Flags and adding one of this options:

* `-Wall` to add a quite complete subset of warnings "that the developers [of the compiler] have high confidence in both their value and a low false-positive rate."

* `-Wextra` to add some extra "warnings that are believed to be valuable and sound (i.e., they aren't buggy), but they may have high false-positive rates or common philosophical objections."

* `-Weverything` to add "an insane group that literally enables every warning in Clang. Don't use this on your code. It is intended strictly for Clang developers or for exploring what warnings exist."

All the quoted parts are by Chandler Carruth. Personally, I think best option is adding `-Wall` and `-Wextra` and then maybe adding or removing some specific warnings on a one by one basis using `-Wunused-variable` (to add) or `-Wno-unused-variable` (to remove).

If we then want to disable a warning temporarily we can use `#pragma` directives:

```objc
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wunused-variable"

// this code will ignore unused variable warnings

#pragma clang diagnostic pop
```

Finally, some programmers like to treat warnings as errors. This can be achieved with these flags:

* `-Werror` to turn warnings into errors.

* `-Werror=foo` to turn a specific warning into an error. 

* `-Wno-error=foo` to turn a specific warning into a warning again (in case `-Werror` has been specified).
