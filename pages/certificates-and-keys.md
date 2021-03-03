# Certificates and Keys

## SSH keys

Some build workflows may require installing specific SSH keys.
One example is checking out dependencies from a private repository using Carthage or SPM.

**Important:** make sure that you are not overwriting SSH keys owned by the CI environment.

### [Store the SSH keys in the CI variables](/pages/ci-goodies/#passing-binary-data-with-ci-variables) and install them

```sh
echo "${SSH_KEY_PUBLIC}" | base64 --decode > "~/.ssh/id_rsa.pub"
echo "${SSH_KEY_PRIVATE}" | base64 --decode > "~/.ssh/id_rsa"
```

### Make sure to adjust access the permissions, so SSH accepts them

```sh
chmod 644 "~/.ssh/id_rsa.pub"
chmod 600 "~/.ssh/id_rsa"
```

### Clean up the keys in the  `after_build` script

```sh
rm "~/.ssh/id_rsa.pub"
rm "~/.ssh/id_rsa"
```

## Code signing certificates

Xcode build tools and, specifically, the `codesign` utility relies on the keychain system to store code signing keys and certificates.
To make specific keys available during the build process, we can use a temporary keychain.

### Define keychain name and generate a password for it

```sh
BUILD_KEYCHAIN_NAME="build.keychain"
BUILD_KEYCHAIN_PASSWORD="$(uuidgen)"
```

### Create a new keychain, set it as default, and unlock it

```sh
security create-keychain -p "${BUILD_KEYCHAIN_PASSWORD}" "${BUILD_KEYCHAIN_NAME}"
security default-keychain -s "${BUILD_KEYCHAIN_NAME}"
security unlock-keychain -p "${BUILD_KEYCHAIN_PASSWORD}" "${BUILD_KEYCHAIN_NAME}"
```

### Add a certificate/key to the keychain and grant codesign utility required permissions

```sh
security import "${BUILD_CERTIFICATE_FILE}" -k "${BUILD_KEYCHAIN_NAME}" -P "${BUILD_CERTIFICATE_PASSWORD}" -T "/usr/bin/codesign"
security set-key-partition-list -S apple-tool:,apple:,codesign: -s -k "${BUILD_KEYCHAIN_PASSWORD}" "${BUILD_KEYCHAIN_NAME}"
```

### Revert defaults and delete the temporary keychain in the `after_build` script

```sh
security default-keychain -s "login.keychain"
security delete-keychain "${BUILD_KEYCHAIN_NAME}"
```

Please refer to the [CI Goodies](/pages/ci-goodies/#passing-binary-data-with-ci-variables) page for details on storing binary data in the CI variables.
