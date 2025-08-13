# Deeplinks SDK

Deeplinks SDK is a lightweight library for handling “short” links, providing functionality similar to the deprecated Firebase Dynamic Links.

---

## Getting Started

### 1️⃣ Add Host String

Define a non-translatable string in `res/values/strings.xml` to represent your link host:

```xml
<string name="appshort_host" translatable="false">yourapp.appshort.link</string>
```

---

### 2️⃣ Add Intent Filter

Add the following intent filter to your `AndroidManifest.xml` inside the relevant `<activity>` (usually your launcher Activity):

```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data android:scheme="https" />
    <data android:host="@string/appshort_host" />
</intent-filter>
```

This allows your app to open links matching your host.

---

### 3️⃣ Initialize SDK

Initialize the SDK in your `Application` class, typically in `onCreate()`:

```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        DeeplinkSdk.init(
            host = "https://${getString(R.string.appshort_host)}",
            apiKey = "{yourApiKey}"
        )
    }
}
```

---

### 4️⃣ Handle Deeplinks in Activity

Retrieve and handle incoming short links in your Activity:

```kotlin
DeeplinkSdk.getInstance()
    .getDynamicLink(this, intent)
    .addOnSuccessListener { data ->
        // data.url         -> Link
        // data.fallbackUrl -> Fallback for Android
        // data.desktopUrl  -> Desktop version
    }
    .addOnFailureListener { throwable ->
        // Handle errors
    }
```

- `data.url` — resolved link  
- `data.fallbackUrl` — optional mobile fallback URL  
- `data.desktopUrl` — optional desktop fallback URL

---

### Notes

- Ensure your Activity handles both cold starts and already-running instances (consider `onNewIntent()` for the latter).

