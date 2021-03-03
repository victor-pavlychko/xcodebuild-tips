---
parent: Building macOS apps
---
# Packaging

## Package as ZIP

**Important:** some steps require zipping application bundles.
Make sure that zip archive correctly stores symbolic links as this is crucial for app to work properly.

```sh
ditto \
    -c -k --sequesterRsrc --keepParent \
    "${BUILD_DIR}/AdHoc/MyApp.app" \
    "${BUILD_DIR}/AdHoc/MyApp.zip"
```
