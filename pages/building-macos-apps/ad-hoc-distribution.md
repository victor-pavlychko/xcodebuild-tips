---
parent: Building macOS apps
---
# Ad-Hoc Distribution

## Export signed app

This section covers exporting using automatic provisioning.

### Prepare export options plist

```sh
cat > "${BUILD_DIR}/ExportOptiopns.plist" <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>destination</key>
    <string>export</string>
    <key>method</key>
    <string>developer-id</string>
    <key>signingStyle</key>
    <string>automatic</string>
</dict>
</plist>
EOF
```

### Perform actual export

```sh
xcodebuild \
    -exportArchive \
    -archivePath "${BUILD_DIR}/MyApp.xcarchive" \
    -exportOptionsPlist "${BUILD_DIR}/ExportOptiopns.plist" \
    -exportPath "${BUILD_DIR}/AdHoc"
```

## Notarize the build

[Customizing the Notarization Workflow](https://developer.apple.com/documentation/xcode/notarizing_macos_software_before_distribution/customizing_the_notarization_workflow)

**Important:** some steps require zipping application bundles.
Make sure that zip archive correctly stores symbolic links as this is crucial for app to work properly.

### Create ZIP archive for notarization

```sh
ditto \
    -c -k --sequesterRsrc --keepParent \
    "${BUILD_DIR}/AdHoc/MyApp.app" \
    "${BUILD_DIR}/AdHoc/MyApp-for-notarization.zip"
```

### Upload the archive for notarization

```sh
xcrun altool --notarize-app \
    --primary-bundle-id "com.mycompany.MyApp" \
    --username "${APPSTORE_LOGIN}" \
    --password "${APPSTORE_PASSWORD}" \
    --file "${BUILD_DIR}/AdHoc/MyApp-for-notarization.zip"
```

### Wait for notarization to complete and staple the build

```sh
until xcrun stapler staple "${BUILD_DIR}/AdHoc/MyApp.app"; do
    echo "wait 1 min..."
    sleep 60
done
```
