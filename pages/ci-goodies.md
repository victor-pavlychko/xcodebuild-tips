# CI Goodies

## Instaling missing tools on the first run

Ideally, build machines should be provisioned with all required software to minimize build time.
Here is and an example script to check the presence of the `figlet` tool and install it if needed:

```sh
if ! which figlet >/dev/null; then
    brew install figlet
fi
```

## Storing binary data in CI variables

CI systems provide storage for secrets to be used during the build process. The values are usually accessible
via environment variables -- an approach suitable for usernames, passwords, and tokens but a bit tricky for binary data like certificate files.
One possible solution is storing file contents as base64-encoded values.

### Copy encoded file data to the macOS pasteboard

```sh
base64 "certificates.p12" | pbcopy
```

### Extract the data on the build machine

```sh
echo "${VARIABLE}" | base64 --decode > "certificates.p12"
```

## Parsing version number from the tag name

Sometimes you need to parse a version number from a commit tag.
Assuming the version is included in the form `...-1.2.3.4-...`, here is a command to extract that value:

```sh
VERSION="$(echo "${CI_COMMIT_TAG}" | sed -n 's/^\(.*-\)*\(\([0-9]\{1,\}\.\)\{3\}[0-9]\{1,\}\)\(-.*\)*$/\2/p')"
```
