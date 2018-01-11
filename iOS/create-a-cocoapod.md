# Create a CocoaPod

_This information is valid as of July 2016. Things may have changed since then._

## The project

**Step 1** -- Create a new Xcode project of type **Cocoa Touch Framework** (e.g. MyFramework).

**Step 2** -- Add new files, create types and code everything as usual. Tests included. Save and close the project.

**Step 3** -- Create a new Xcode project, for instance of type **Single View Application**. Call it MyFramework\_Example.

**Step 4** -- Drag MyFramework.xcodeproj file from Finder and drop it at the root level of the MyFramework\_Example project in Xcode. From now on, any changes on MyFramework can be done either by opening MyFramework project directly or by opening MyFramework\_Example instead. This last option will probably be handier sometimes.

**Step 5** -- Make sure everything that needs to be accessible from outside of MyFramework is marked as public.

**Step 6** -- In Xcode, drag the MyFramework.framework from the MyFramework.xcodeproj project into the Embedded Binaries section in the MyFramework_Example project.

**Step 7** -- Use the framework from the MyFramework_Example and check everything works as expected. Remember to `import MyFramework` as required.

## The CocoaPods part -- some housekeeping

**Step 8** -- Copy CHANGELOG.md, LICENSE and MyFramework.podspec files from any other project. *Yes, copy & paste FTW.*

**Step 9** -- Change the content in CHANGELOG.md and LICENSE files (only if required, although CHANGELOG.md probably will).

**Step 10** -- Totally review and update content in MyFramework.podspec.

**Step 11** -- Run `pod lib lint` to ensure MyFramework passes validation.

## The CocoaPods part -- checking the pod works

As the guys from CocoaPods say, "before releasing your new Pod to the world its best to test that you can install your pod successfully into an Xcode project".

**Step 12** -- Push MyFramework repository to GitHub.

**Step 13** -- Create a new Xcode project, for instance of type **Single View Application**. Call it MyFramework\_Pod.

**Step 14** -- Add a Podfile file into the MyFramework\_Pod folder and reference the GitHub repo with:

```
pod 'MyFramework', :git => 'https://github.com/almata/MyFramework.git'
```

**Step 15** -- Run `pod install`. That should download the pod and create the usual MyFramework\_Pod.xcworkspace. Everything should work as usual.

## The CocoaPods part -- making the pod available

**Step 16** -- "Once you have a release ready you'll need to make the corresponding tag. First run a quick `pod lib lint` then create your tag and push it."

**Step 17** -- Run `git add -A && git commit -m "Release 1.0"` to commit everything.

**Step 18** -- Run `git tag '1.0'` to add the tag.

**Step 19** -- Run `git push --tags` to push including tags.

**Step 20** -- "Once your tags are pushed you can use the command `pod trunk push MyFramework.podspec`."

Most of this information can be found at [CocoaPods](https://guides.cocoapods.org/making/making-a-cocoapod.html).

_PS. Both MyFramework\_Example and MyFramework\_Pod folders can be inside the main MyFramework folder to keep everything together in a single place. Just remember to add these folders in the .gitignore file._

