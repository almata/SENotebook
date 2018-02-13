# Run AVD emulator without running Android Studio

It is possible to start an AVD emulator without having to first open Android Studio. Provided, of course, we've already created the emulator itself.

First, we need to get a list of all available AVD emulators in our machine:

```bash
$ ~/Library/Android/sdk/tools/bin/avdmanager list avd
```

This will return a big bunch of text, but we are only interested in the last part, so I recommend this instead:

```bash
$ ~/Library/Android/sdk/tools/bin/avdmanager list avd | grep -v -o Parsing.*
```

And the output will be something like this:

```
    Name: Nexus_4_API_25
  Device: Nexus 4 (Google)
    Path: /Users/albertmata/.android/avd/Nexus_4_API_25.avd
  Target: Google APIs (Google Inc.)
          Based on: Android 7.1.1 (Nougat) Tag/ABI: google_apis/x86_64
    Skin: nexus_4
  Sdcard: 100M
---------
    Name: Nexus_5X_API_27
  Device: Nexus 5X (Google)
    Path: /Users/albertmata/.android/avd/Nexus_5X_API_27.avd
  Target: Google Play (Google Inc.)
          Based on: Android API 27 Tag/ABI: google_apis_playstore/x86
    Skin: nexus_5x
  Sdcard: 100M
```

All we need now is the name of the emulator we want to run in order to execute this command:

```bash
$ ~/Library/Android/sdk/tools/emulator -avd Nexus_4_API_25
```

Please note the exact path to the `tools` directory can be slightly different based on your original Android Studio installation.