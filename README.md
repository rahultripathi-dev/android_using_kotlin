## 🤖 Android Native Concepts for React Native Developers (Kotlin)

When writing [Native Modules](https://reactnative.dev) for Android in React Native, JavaScript/TypeScript concepts map to Kotlin core architectures.

### 📦 Quick Reference Table


| JavaScript / TypeScript | Kotlin Equivalent | Key Difference |
| :--- | :--- | :--- |
| `const` | `val` | Immutable reference; evaluated at runtime. |
| `let` / `var` | `var` | Mutable variable; requires explicit or inferred typing. |
| `Optional Chaining (?.)` | `Safe Call (?.)` | Enforced at compile-time to prevent Null Pointer Exceptions. |
| `interface` / `type` | `data class` | Retains full type structure and automatic helper methods at runtime. |
| `Promises` / `async-await` | `Coroutines` | Lightweight threads managed by the Kotlin runtime for async operations. |
| `package.json` | `build.gradle.kts` | Handles native dependencies, Android SDK versions, and build variants. |

---

### 🔑 Core Language Essentials

#### 1. Null Safety
Kotlin separates nullable and non-nullable types at compile time to eliminate runtime crashes.
```kotlin
var nonNullUser: String = "John"  // Cannot be null
var nullableUser: String? = null // Explicitly allowed to be null

// Safe call (Matches JS optional chaining)
val length = nullableUser?.length
```

#### 2. Data Classes
Unlike TypeScript interfaces which disappear after compilation, Kotlin `data class` models exist at runtime and automatically provide `copy()` and utility methods.
```kotlin
data class DeviceConfig(val deviceId: String, val isEnabled: Boolean)
```

#### 3. Asynchronous Execution (Coroutines)
Native network requests, database operations, or heavy calculations must run off the main UI thread using Coroutines.
```kotlin
import kotlinx.coroutines.*

// Launch a background task
CoroutineScope(Dispatchers.IO).launch {
    val result = fetchNativeData()
    withContext(Dispatchers.Main) {
        // Update UI or send event to React Native here
    }
}
```

---

### 🌉 React Native Bridge Architecture

To expose Kotlin code to the JavaScript layer, the implementation relies on three native pillars:

#### 1. ReactContextBaseJavaModule
Defines the module name used in JavaScript and houses the native methods.
```kotlin
class MyNativeModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {
    override fun getName() = "MyNativeModule"

    @ReactMethod
    fun performAction(data: String, promise: Promise) {
        // Native code execution
        promise.resolve("Success from Kotlin")
    }
}
```

#### 2. ReactPackage
Registers your module within the Android application runtime.
```kotlin
class MyNativePackage : ReactPackage {
    override fun createNativeModules(reactContext: ReactApplicationContext) = 
        listOf(MyNativeModule(reactContext))

    override fun createViewManagers(reactContext: ReactApplicationContext) = 
        emptyList<ViewManager<*, *>>()
}
```

#### 3. Type Mapping Reference
The React Native bridge automatically translates types between environments:
* `String` ↔ `string`
* `Double` / `Int` ↔ `number`
* `Boolean` ↔ `boolean`
* `ReadableMap` ↔ `object`
* `ReadableArray` ↔ `array`
