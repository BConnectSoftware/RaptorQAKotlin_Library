# RaptorQA Android SDK

RaptorQA lets Android applications capture and submit QA reports. The SDK is distributed through **GitHub Packages (Maven)** and supports applications written in **Java or Kotlin**.

## Requirements

- Android application with **minSdk 26** (Android 8.0) or later
- Gradle with access to Google's Maven repository and Maven Central
- A GitHub account and a **personal access token (classic)** with `read:packages` permission
- Your RaptorQA account ID and application ID

## 1. Configure GitHub Packages credentials

GitHub requires authentication when Gradle downloads Maven packages from GitHub Packages, including public packages. **Use your own GitHub account and token; do not use the SDK publisher's token.**

1. In GitHub, open **Settings → Developer settings → Personal access tokens → Tokens (classic)**.
2. Generate a token with **`read:packages`** permission.
3. Add these properties to your *user-level* Gradle file, `~/.gradle/gradle.properties` (not the project file):

```properties
gpr.user=YOUR_GITHUB_USERNAME
gpr.key=YOUR_READ_PACKAGES_TOKEN
```

Keep the token secret. Do not commit it to source control. On macOS/Linux, you can restrict access with `chmod 600 ~/.gradle/gradle.properties`.

For CI/CD, supply the same Gradle properties through your pipeline's secret management instead of checking them into the repository. The CI identity must have permission to read the package.

## 2. Add the package repository

In the **project's `settings.gradle.kts`**, add the GitHub Packages repository inside the existing `dependencyResolutionManagement.repositories` block. Keep the other repositories and settings your project already uses:

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://maven.pkg.github.com/BConnectSoftware/RaptorQAKotlin_Library")
            credentials {
                username = providers.gradleProperty("gpr.user").orNull
                password = providers.gradleProperty("gpr.key").orNull
            }
            content {
                includeGroup("com.bconnectsoftware")
            }
        }
    }
}
```

If your project uses Groovy (`settings.gradle`) instead:

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri('https://maven.pkg.github.com/BConnectSoftware/RaptorQAKotlin_Library')
            credentials {
                username = providers.gradleProperty('gpr.user').orNull
                password = providers.gradleProperty('gpr.key').orNull
            }
            content {
                includeGroup('com.bconnectsoftware')
            }
        }
    }
}
```

**Note:** If your project already has a `dependencyResolutionManagement` block, edit it rather than adding a second block. Keep your existing `repositoriesMode` if it differs.

## 3. Add the SDK dependency

In your **app module's** `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.bconnectsoftware:raptorqa:1.0.0")
}
```

Or in Groovy `app/build.gradle`:

```groovy
dependencies {
    implementation 'com.bconnectsoftware:raptorqa:1.0.0'
}
```

Gradle downloads the AAR and its declared AndroidX/Jetpack Compose dependencies automatically. **Do not also include a local `RaptorQA-release.aar`** in the same app.

## 4. Initialize the SDK

Initialize RaptorQA once during application startup, in your `Application` subclass.

**Java:**

```java
import android.app.Application;
import com.bconnect.raptorqa.RaptorQAContext;

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        RaptorQAContext.configure(this, "YOUR_ACCOUNT_ID", "YOUR_APPLICATION_ID");
    }
}
```

**Kotlin:**

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

Register your subclass in `app/src/main/AndroidManifest.xml` by adding `android:name` to your **existing** `<application>` element:

```xml
<application
    android:name=".MyApplication"
    ... >
</application>
```

Also add internet permission inside `<manifest>` and **outside** `<application>`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Replace the placeholder IDs with your RaptorQA credentials. Preserve your existing manifest attributes and activity declarations.

## 5. Sync, build, and test

Sync Gradle in Android Studio and launch your app. Verify that the RaptorQA reporting interface opens and that a test report submits successfully.

You can inspect dependency resolution with:

```bash
./gradlew :app:dependencies --configuration debugRuntimeClasspath
```

## Troubleshooting

- **401 Unauthorized:** Check the GitHub username and token, confirm `read:packages`, and check that the token/account can access the package.
- **Could not find `com.bconnectsoftware:raptorqa`:** Confirm the repository URL, version `1.0.0`, credentials, and that the repository is configured in `dependencyResolutionManagement`.
- **Duplicate classes or conflicting dependencies:** Remove any older manually installed RaptorQA AAR. Inspect the resolved dependency graph; existing applications can still impose incompatible dependency versions.
- **SDK cannot submit reports:** Check internet permission and the RaptorQA account/application IDs.

## Support and license

- Website: https://raptorqa.com
- Support: support@raptorqa.com
- License: https://github.com/BConnectSoftware/RaptorQAKotlin_Library/blob/main/LICENSE.md

Copyright © BConnect Software LLC. All rights reserved.
