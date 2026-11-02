
# RaptorQA Android SDK

## Installation Guide

RaptorQA is an Android SDK that enables QA reporting directly from an Android application.

The SDK is distributed as a compiled Android Archive (`.aar`) file. You do not need access to the RaptorQA source code or a Maven Central account to install it.

### Requirements

- Android Studio
- An Android application using Gradle
- Android 8.0 (API 26) or later
- RaptorQA account and application credentials

### Step 1 — Download the SDK

1. Visit the [RaptorQA Android SDK Releases](https://github.com/BConnectSoftware/RaptorQAKotlin_Library) page.
2. Select the latest release.
3. Under **Assets**, download `RaptorQA-release.aar` (or the versioned AAR filename provided with the release).

### Step 2 — Add the AAR to your Android project

Create a `libs` directory inside your application's module if one does not already exist.

Copy the downloaded AAR into that directory.

Example project structure:

```text
MyAndroidProject/
├── app/
│   ├── libs/
│   │   └── RaptorQA-release.aar
│   ├── src/
│   └── build.gradle.kts
├── gradle/
└── settings.gradle.kts
```

### Step 3 — Add the Gradle dependency

Open your application's `app/build.gradle.kts` and add the following dependency:

```kotlin
dependencies {
    implementation(files("libs/RaptorQA-release.aar"))
}
```

Because the SDK is distributed as a standalone AAR, Gradle does not automatically resolve its external dependencies.

For an application that does not already include these libraries, add the required AndroidX and Jetpack Compose dependencies:

```kotlin
dependencies {
    implementation(files("libs/RaptorQA-release.aar"))

    implementation("androidx.appcompat:appcompat:1.7.1")
    implementation("androidx.core:core-ktx:1.17.0")
    implementation("com.google.android.material:material:1.13.0")

    implementation(platform("androidx.compose:compose-bom:2025.10.01"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.material3:material3")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material:material-icons-extended")

    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.9.4")
    implementation("androidx.activity:activity-compose:1.11.0")
    implementation("androidx.window:window:1.5.1")
}
```

These are example compatible dependency versions. If your application already uses AndroidX or Compose, use versions compatible with your existing project. The release notes may specify additional requirements.

### Step 4 — Initialize RaptorQA

Configure the SDK during application startup, using your RaptorQA account ID and application ID.

```kotlin
import android.app.Application
import com.bconnect.raptorqa.RaptorQAContext

class MyApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        RaptorQAContext.configure(
            application = this,
            accountId = "YOUR_ACCOUNT_ID",
            appId = "YOUR_APPLICATION_ID"
        )
    }
}
```

Register the application class if you dont have one in your `AndroidManifest.xml`:

```xml
<application
    android:name=".MyApplication"
    ... >
</application>
```

Replace the example IDs with the values provided by RaptorQA.

### Step 5 — Build and run

Sync your Gradle project, then build and launch your application.

When correctly configured, the RaptorQA SDK provides its QA reporting interface within your application.

### Updating the SDK

To upgrade to a newer version:

1. Download the updated AAR from GitHub Releases.
2. Replace the existing file in `app/libs`.
3. Sync Gradle.
4. Rebuild and test your application.

### Support

For assistance, contact **support@raptorqa.com**.

Website: [https://raptorqa.com](https://raptorqa.com)

### License

Copyright © BConnect Software LLC. All rights reserved.

See [LICENSE.md](https://github.com/BConnectSoftware/RaptorQAKotlin_Library/blob/main/LICENSE.md) for licensing terms.
