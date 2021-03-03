# Property List Files

## Handling version numbers

### Updating version number in Info.plist

```sh
plutil -replace 'CFBundleVersion' -string "${VERSION}" "Info.plist"
plutil -replace 'CFBundleShortVersionString' -string "${VERSION_SHORT}" "Info.plist"
```

### Updating version number in the settings bundle

More complext plist workflows can be performed with `PlistBuddy`:

```sh
/usr/libexec/PlistBuddy -c "Set :PreferenceSpecifiers:0:DefaultValue ${VERSION}" "Settings.bundle/Root.plist"
```

## Creating a new plist file

The easiest way to create a new plist file is via the heredoc syntax:

```sh
cat > "ExportOptiopns.plist" <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>destination</key>
    <string>${DESTINATION}</string>
    <key>method</key>
    <string>${METHOD}</string>
    <key>signingStyle</key>
    <string>${SIGNING_STYLE}</string>
</dict>
</plist>
EOF
```
