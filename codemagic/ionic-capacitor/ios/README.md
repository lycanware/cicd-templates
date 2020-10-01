# Codemagic

## Apple App Store

Before publishing will work, you must setup the App within your iTunes Connect account.

### Code Signing

To build your app, Codemagic must be able to code sign. This example uses automatic code signing. You must setup the following:

- [API Key and Private Key](https://docs.codemagic.io/code-signing-yaml/signing/)

The values produced go into the following section.

```yaml
environment:
  vars:
    APP_STORE_CONNECT_PRIVATE_KEY: >-
      Encrypted(<CONNECT_PRIVATE_KEY_FILE>)
    APP_STORE_CONNECT_KEY_IDENTIFIER: >-
      Encrypted(<KEY_IDENTIFIER>)
    APP_STORE_CONNECT_ISSUER_ID: >-
      Encrypted(<ISSUER_ID>)
    CERTIFICATE_PRIVATE_KEY: >-
      Encrypted(<CERTIFICATE_PRIVATE_KEY>)
```

### Version and Build Numbers

To auto increment version numbers, the following section is used. This should be modified as needed.

```shell
agvtool new-marketing-version 1.0.$((BUILD_NUMBER + 1))
avgtool new-version -all 3
```

In this example, version numbers are in this format: `Major.Minor.Patch` - the Patch number increments on every Codemagic build, and will create a new version in Test Flight.

The second line creates a single `build` in Test Flight labelled `3`. This can be extended to produce a `dev`, `staging` and `live` version for QA. I recommend `dev = 3`, `staging = 2`, `live = 1` as Test Flight users automatically get the latest version number, so live testing must be explicitly selected in Test Flight.

### Credentials

To provide Codemagic permission to deploy to your App Store account, you must [setup an App Specific Password](https://support.apple.com/en-gb/HT204397)

The App Specific Password goes into this section.

```yaml
publishing:
  app_store_connect:
    apple_id: <YOUR_APPLE_DEVELOPER_EMAIL>
    password: Encrypted(<YOUR_APP_SPECIFIC_PASSWORD>)
```
