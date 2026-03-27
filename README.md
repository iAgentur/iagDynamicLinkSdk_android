# DeeplinkSDK for Android

**DeeplinkSDK** is a lightweight library for handling dynamic links and deep linking in Android applications — a drop-in replacement for the deprecated Firebase Dynamic Links.

---

## Requirements

- Android API 24+
- Kotlin 2.0+

---

## Installation

Add the GitHub-hosted Maven repository and the dependency to your app's `build.gradle.kts`:

```kotlin
repositories {
    maven {
        url = uri("https://raw.githubusercontent.com/iAgentur/iagDynamicLinkSdk_android/main/")
    }
}

dependencies {
    implementation("iagenturandroidlibs:deeplinks-android:1.0.1")
}
```

Or Groovy `build.gradle`:

```groovy
repositories {
    maven { url 'https://raw.githubusercontent.com/iAgentur/iagDynamicLinkSdk_android/main/' }
}

dependencies {
    implementation 'iagenturandroidlibs:deeplinks-android:1.0.1'
}
```

---

## Setup

### 1. Add Host String

Define a non-translatable string in `res/values/strings.xml`:

```xml
<string name="appshort_host" translatable="false">yourapp.appshort.link</string>
```

### 2. Add Intent Filter

In `AndroidManifest.xml`, add to your launcher `<activity>`:

```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
    <data android:host="@string/appshort_host" />
</intent-filter>
```

### 3. Initialize the SDK

In your `Application` class `onCreate()`:

```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        DeeplinkSdk.init(
            host = "https://${getString(R.string.appshort_host)}",
            apiKey = "yourApiKey"
        )
    }
}
```

### 4. Handle Deep Links

Handle both cold starts and cases where the app is already running:

```kotlin
// Called on cold start
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    handleIntent(intent)
}

// Called when app is already running
override fun onNewIntent(intent: Intent) {
    super.onNewIntent(intent)
    handleIntent(intent)
}

private fun handleIntent(intent: Intent) {
    DeeplinkSdk.getInstance()
        .getDynamicLink(this, intent)
        .addOnSuccessListener { data ->
            // data.url         — resolved link
            // data.fallbackUrl — Android fallback URL
            // data.desktopUrl  — desktop fallback URL
        }
        .addOnFailureListener { throwable ->
            // handle error
        }
}
```
