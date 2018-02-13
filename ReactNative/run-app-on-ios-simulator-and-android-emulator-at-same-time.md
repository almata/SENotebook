# Run app on iOS simulator and Android emulator at same time

Just as personal documentation, these are the steps I've needed to follow to run an Android emulator and iOS simulator, side by side, both with live reload enabled.

1. I've needed to install Watchman with `brew install watchman`.
2. I've also needed to install the React Native CLI with `npm install -g react-native-cli`.
3. Pretty obvious, I already had both Xcode and Android Studio installed on my Mac. 

__iOS simulator__

4. With `react-native run-ios` I launch the React Packager. Same thing can be done with `npm run ios` provided `package.json` contains a `scripts` section like this:

```javascript
"scripts": {
  "start": "react-native start",
  "android": "react-native run-android",
  "ios": "react-native run-ios",
  "test": "node node_modules/jest/bin/jest.js"
}
```

5. At this point, the iOS simulator gets opened automatically and loads the app. I can reload it every time after saving changes in the code using ⌘R in the simulator. Or I can show the developer menu with Hardware > Shake Gesture and from there enable the live reload option.

__Android emulator__

6. First of all, I need to __manually__ open an Android emulator using `~/Library/Android/sdk/tools/emulator -avd Nexus_4_API_25` (more details about this command at [Run AVD emulator without running Android Studio](https://github.com/almata/SENotebook/blob/master/Android/run-avd-emulator-without-running-android-studio.md)).
7. With `react-native run-android` I launch the React Packager. Same thing can be done with `npm run android` (again, provided `package.json` contains a `scripts` section as stated above).
8. At this point, the Android emulator loads the app. I can access the developer menu with ⌘M and enable live reload from there.
