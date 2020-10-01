# Google Play

The first version of the app must be uploaded manually, so it's possible these examples will fail to publish at first. If this happens, open the build in Codemagic, download the APK and upload it manually to Google Play. Future builds should then publish.

Make sure you update the following:

- `<WORKFLOW_DISPLAY_NAME>`
- `<YOUR_PACKAGE_NAME>`
- `<EMAIL_ADDRESS>`

### Code Signing

To build your app, Codemagic must be able to code sign. You need to produce a keystore file. Assuming you have Android studio, you should have access to the `Java Keytool` utility. Here are 2 useful links that show you how to produce the keystore file:

- [Codemagic documents](https://docs.codemagic.io/code-signing/android-code-signing/)
- [Blog article](https://blog.codemagic.io/distributing-module-yaml/)

The values produced go into the following section. The `CM_KEYSTORE` is the encrypted file (not the content)

```yaml
environment:
  vars:
    CM_KEYSTORE: >-
      Encrypted(<KEYSTORE_FILE>)
    CM_KEYSTORE_PASSWORD: >-
      Encrypted(<KEYSTORE_PASSWORD>)
    CM_KEY_ALIAS_PASSWORD: >-
      Encrypted(<KEYSTORE_ALIAS>)
    CM_KEY_ALIAS_USERNAME: >-
      Encrypted(<KEYSTORE_ALIAS_USERNAME>)
```

### Build Numbers

Inside your `android/app/build.gradle` file, you will need to access the keyfile variables, increment build number and create a release. Here is an example of a few things you will need to add.

```java
apply plugin: 'com.android.application'

def keystoreProperties = new Properties()
def keystorePropertiesFile = rootProject.file('key.properties')
if (keystorePropertiesFile).exists() {
    keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
}

android {
    def appVersionCode = Integer.valueOf(System.env.BUILD_NUMBER ?: 1)
    compileSdkVersion rootProject.ext.compileSdkVersion
    defaultConfig {
        ...
        ...
        ...
        versionCode appVersionCode
        versionName "1.0"
    }

    if (System.getenv()["CI"] && System.getenv()["CONTINUOUS_INTEGRATION"]) {
        signingConfigs {
            file(rootProject.file('key.properties')).with { propFile ->
                if (propFile.canRead()) {
                    release {
                        storeFile file(keystoreProperties['storeFile'])
                        storePassword keystoreProperties['storePassword']
                        keyAlias keystoreProperties['keyAlias']
                        keyPassword keystoreProperties['keyPassword']
                    }
                }
            }
        }

    } else {

    }

    buildTypes {
        if (System.getenv()["CI"] && System.getenv()["CONTINUOUS_INTEGRATION"]) {
            release {
                ...
                ...
                signingConfig signingConfigs.release
            }
        }
    }
}
```

### Credentials

To provide Codemagic permission to deploy to your Google Play account, you must [create a service account](https://docs.codemagic.io/publishing-yaml/distribution/) and download the JSON file. **You also need to grant access**

Encrypt the JSON file (not the file content), and place it under the publishing section:

```yaml
publishing:
  google_play:
    credentials: >-
      Encrypted(<SERVICE_ACCOUNT_CREDENTIAL_FILE>)
```
