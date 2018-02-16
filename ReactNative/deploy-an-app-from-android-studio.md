# Deploy an app from Android Studio

I've spent two hours fighting with Android Studio and React Native in order to deploy an app. It was working from console using `react-native run-android`, but not from Android Studio. These are the things that have finally worked for me:

1. Obviously, follow step by step the instructions from [Getting Started > Building Projects with Native Code > macOS + Android](https://facebook.github.io/react-native/docs/getting-started.html).

2. In Android Studio, under __File > Project Structure > SDK Location__ check the __Use embedded JDK__ option.

3. In Android Studio, under __Android Studio > Preferences > Build, Execution, Deployment > Instant Run__ totally disable Instant Run. Strangely enough, this seems to be a problem only in some cases -not always-, but worth to keep it in mind.

4. In order to create an `.apk` file that does not rely on any React Packager running so that it can be distributed to betatesters, follow the instructions from [Generating Signed APK](https://facebook.github.io/react-native/docs/signed-apk-android.html).
