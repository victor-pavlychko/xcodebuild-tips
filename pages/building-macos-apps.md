---
has_children: true
---
# Building macOS apps

## Codesign [Sparkle](https://sparkle-project.org) binaries

Sparkle automatic update system comes with a utility app which must be properly signed
with a hardened runtime before building the host application. Here is the command to enable the hardened runtime
for Sparkle installed using Carthage:

```sh
for file in Carthage/Build/Mac/Sparkle.framework/Resources/Autoupdate.app/Contents/MacOS/*; do
    codesign \
        --force -o runtime \
        -s "Developer ID Application" \
        "$file"
done
```

## Build the workspace

```sh
xcodebuild \
    -workspace "MyApp.xcworkspace" \
    -scheme "MyApp" \
    -configuration "Release" \
    -archivePath "${BUILD_DIR}/MyApp.xcarchive" \
    archive
```
