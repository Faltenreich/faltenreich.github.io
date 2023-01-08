---
layout: post
title: Migrating to Kotlin Multiplatform Mobile
date: 2022-11-13
categories:
public: false
---

Write once, deploy everywhere. This promise has been made time and time again for many years but there is still so much potential left before the final answer.

Microsoft introduced its Universal Windows Platform in 2015 which supported many Windows platforms and was used by the author with great enthusiasm until Windows Mobile became a thing of the past. The acquisition of Xamarin in 2016 marked the next stepping stone in going multiplatform and will soon be superseded by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their platform, but they are far from leaving the field. 

Apple does not live under the same rock it started iOS with by going open-source with Swift and making iOS apps compatible with macOS. This new-found openness ends at the borders of its own ecosystem though.

Google on the other hand does not only feature its own platform but introduced one of the leading multiplatform frameworks as well. Flutter supports mobile on both Android and iOS, desktop and web, has a steep learning curve and is completely open-source. While this framework is one of the best options to implement once and deploy everywhere in 2023, it also introduces an additional platform with its own programming language and layer of complexity.

> Disclaimer: This blogpost is not meant as a rating of or comparison between multiplatform frameworks. They all have their right to exist and the choice depends on numerous factors, some of them even beyond the developers' control. Examples for tools, frameworks or libaries are not representative and may be switched out for other options. They shall simply illustrate a potential migration path when moving to KMM.

Entering Kotlin Multiplatform Mobile.

Kotlin Multiplatform Mobile (KMM) supports a similar number of platforms as Flutter, including Android and iOS. Since its introduction in 2020 many companies have been joining the platform, such as Netflix, VMWare and Philips. In October 2022 [it went Beta](https://blog.jetbrains.com/kotlin/2022/10/kmm-beta) and has been declared production-ready which was accompanied by Google's announcement of an [experimental preview of libraries for this platform](https://android-developers.googleblog.com/2022/10/announcing-experimental-preview-of-jetpack-multiplatform-libraries.html). Therefore it is safe to say that KMM has left its early days and is finally starting to gain track.

This blogpost focuses on the transition from native- to multiplatform development using KMM. Readers will get an overview of the tools (not) available in 2023 regarding toolchain, frameworks and libraries. This is not a guide about how to set up and run a multiplatform project as this has already been covered by numerous articles out there.

## Toolchain

**Android Studio** is being used as an integrated development environment (IDE). This means a well-known environment for native Android- and Flutter developers and a potential return to an IntelliJ-based IDE since the [sunset of AppCode](https://blog.jetbrains.com/appcode/2022/12/appcode-2022-3-release-and-end-of-sales-and-support). Xcode is still being used for the native iOS part as its build tools are needed to compile, sign and archive apps for Apple's ecosystem. This also means that macOS is required to deploy an iOS app, even though some constellations may use Linux or Windows when focussing on Android while leaving iOS for others like the build server.

**Kotlin** is the one and only programming language for KMM, as the name implies. This means no change for Android developers already using Kotlin - at least on the language-level, since there is no JVM anymore to rely on. iOS developers will have to learn a new programming language but should be able to quickly gain a foothold by transferring existing knowledge from Swift to Kotlin. Both share many paradigms, since they have been created in a similar time frame, and both put a heavy focus on extensibility which is why they are quickly adopting modern techniques like functional, reactive or declarative programming.

**Gradle** is used as build automation tool which feels like home for native Android developers. iOS developers on the other hand have to learn yet another tool after Carthage, Cocoapods and Swift Package Manager while keeping one of them for the native side of the build process.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| IDE | [Android Studio](https://developer.android.com/studio) | [Xcode](https://developer.apple.com/xcode) (RIP AppCode) | [Android Studio](https://developer.android.com/studio) |
| Programming language | [Kotlin/JVM](https://developer.android.com/kotlin) | [Swift](https://developer.apple.com/swift) | [Kotlin/Native](https://kotlinlang.org/docs/native-overview.html) |
| Build automation | [Gradle](https://gradle.org) | [Swift Package Manager](https://www.swift.org/package-manager) | [Gradle](https://gradle.org) |

## Frameworks and libraries

Frameworks and libraries are not mandatory and the same for every project but they amplify the development process enormously. Over the years, standards came and go and with KMM many will leave the stage once again. 

In short: most dependencies only available on iOS or strongly dependant on the JVM may not (yet) be available for KMM and and many compatible with Kotlin/Native already are. Technically native dependencies of one platform could be made compatible via the [expect/actual](https://kotlinlang.org/docs/multiplatform-connect-to-apis.html) mechanism but this would imply an implementation for the other platform that is unfamiliar with the corresponding APIs and therefore debatable.

### Data handling

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Date and time | [Date/Time](https://www.baeldung.com/java-8-date-time-intro) | [Date](https://developer.apple.com/documentation/foundation/date) | [kotlinx.datetime](https://github.com/Kotlin/kotlinx-datetime) | 
| Serialization | [Moshi](https://github.com/square/moshi) | [Codable](https://developer.apple.com/documentation/foundation/archives_and_serialization) | [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) |
| Persistence | [Room](https://developer.android.com/training/data-storage/room) | [Core Data](https://developer.apple.com/documentation/coredata) | [SQLDelight](https://cashapp.github.io/sqldelight/1.5.4/multiplatform_sqlite) |
| Networking | [Retrofit](https://square.github.io/retrofit) | [AFNetworking](https://github.com/AFNetworking/AFNetworking) | [Ktor](https://ktor.io) |
| Concurrency | [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-overview.html) | [async/await](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html) | [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-overview.html) |
| Streaming | [Kotlin Flow](https://kotlinlang.org/docs/flow.html) | [Combine](https://developer.apple.com/documentation/combine) | [Kotlin Flow](https://kotlinlang.org/docs/flow.html) |

### Hardware

Early Flutter adopters remember that time and history repeats itself regarding low-level frameworks for KMM. Dependencies are far and few between, mostly under heavy development, and therefore not yet suited for production. Currently the best alternative is to branch manually via [expect/actual](https://kotlinlang.org/docs/multiplatform-connect-to-apis.html) or omit KMM yet if the whole application revolves around hardware features.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Biometry | [androidx.biometrix](https://developer.android.com/training/sign-in/biometric-auth) | [Touch ID, Face ID](https://developer.apple.com/documentation/localauthentication/logging_a_user_into_your_app_with_face_id_or_touch_id) | [moko-biometry](https://github.com/icerockdev/moko-biometry)
| Bluetooth | [android.bluetooth](https://developer.android.com/guide/topics/connectivity/bluetooth) | [Core Bluetooth](https://developer.apple.com/documentation/corebluetooth) | [Kable](https://github.com/juullabs/kable) |
| Camera | [CameraX](https://developer.android.com/training/camerax) | [AVFoundation Capture](https://developer.apple.com/documentation/avfoundation/capture_setup) | *none yet* |
| GPS | [android.location](https://developer.android.com/training/location) | [Core Location](https://developer.apple.com/documentation/corelocation) | [moko-geo](https://github.com/icerockdev/moko-geo) |
| NFC | [android.nfc](https://developer.android.com/guide/topics/connectivity/nfc) | [Core NFC](https://developer.apple.com/documentation/corenfc) | *none yet* |

### Quality assurance

Forget your state-of-the-art dependency injection framework as it is platform-dependent. Forget your mocking framework as it relies on reflection which is read-only in Swift, "[The Only Modern Language without Mocking Frameworks](https://blog.pragmaticengineer.com/swift-the-only-modern-language-with-no-mocking-framework)". Therefore there are only a handful options to mock in KMM and they are heavily limited. [Mockative](https://github.com/mockative/mockative) and [MocKMP](https://github.com/kosi-libs/MocKMP) for example only support mocking interfaces. Testing on the other hand is rather simple and heavily influenced by JUnit, a framework older than the platforms we are talking about.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Dependency injection | [Hilt](https://developer.android.com/training/dependency-injection/hilt-android) | [Swinject](https://github.com/Swinject/Swinject) | [Koin](https://insert-koin.io) |
| Mocking | [MockK](https://mockk.io/) | *none* | *heavily limited* |
| Testing | [JUnit](https://junit.org) | [XCTest](https://developer.apple.com/documentation/xctest) | [kotlin.test](https://kotlinlang.org/api/latest/kotlin.test) |

### Backend-as-a-service

Realm
https://github.com/objectbox/objectbox-java/issues/601
[Firebase](https://firebaseopensource.com/projects/gitliveapp/firebase-kotlin-sdk) (Third party)

## User interface

### Compose Multiplatform

[Compose Multiplatform](https://www.jetbrains.com/de-de/lp/compose-mpp) elevates [Jetpack Compose](https://developer.android.com/jetpack/compose) and makes this declarative design framework compatible with KMM. This marks the next step in programming with KMM as not only the business layer can be shared between platforms but the user interface itself. 

The code for Compose Multiplatform behaves exactly like for Jetpack Compose.

```
// shared/MainView.kt
@Composable
fun MainView() {
    Text("Hello, world")
}
```

The only native components left are one Android Activity and one iOS ViewController respectively that each embed the multiplatform composables in their native app.

For Android we need an Activity to include Jetpack Compose by using the [Interoperability APIs](https://developer.android.com/jetpack/compose/interop/interop-apis) and then its content as entry point from our shared Compose Multiplatform code.

```
// androidApp/MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Bridging Jetpack Compose with Compose Multiplatform
        setContent { MainView() }
    }
}
```

For iOS we need an AppDelegate to set its root UIViewController which is being created using androidx.compose.ui.window.Application in our shared module. SwiftUI is currently not supported.

```
// iosApp/AppDelegate.swift
@UIApplicationMain
struct AppDelegate: UIResponder, UIApplicationDelegate {
    func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        // Bridging Swift with Kotlin/Native
        let mainViewController = MainViewControllerKt.MainViewController()
        window?.rootViewController = mainViewController
        window?.makeKeyAndVisible()
        return true
    }
}

// shared/iOSMain/MainViewController.kt
fun MainViewController(): UIViewController {
    return Application("App name") {
        // Bridging UIKit with Compose Multiplatform
        MainView()
    }
}
```

Compose Multiplatform is working on Android and technically also on iOS. Support for the latter has been introduced with version 1.2.0-alpha1 early 2022 and is still in alpha state. This becomes apparent as the experimental support for Apple's UIKit has to be enabled via opt-in and features like the preview or JUnit 4 extensions do not work yet. In my opinion there might be at least another year around the corner until Compose Multiplatform becomes production-ready for mobile, but the foundation has been laid out and shows big potential.

### Material

Google's design components are available for Compose Multiplatform but offer a different set of components compared to Android since KMM requires support for every platform. If there is no pendant on iOS, then Android will not be supported either. One example of this is the DropdownMenu which has no counterpart on iOS and is therefore not available. Material is currently transitioning to version 3 which is still experimental and has to be enabled via opt-in.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Material | [Compose Material](https://developer.android.com/jetpack/androidx/releases/compose-material) | [Material Components for iOS](https://github.com/material-components/material-components-ios) | [Compose Material Components](https://mvnrepository.com/artifact/org.jetbrains.compose.material/material) |
| Material3 | [Compose Material 3](https://developer.android.com/jetpack/androidx/releases/compose-material3) | *unsupported* | [Compose Material3 Components](https://mvnrepository.com/artifact/org.jetbrains.compose.material3/material3) |

### Resources



So sharing user interfaces is possible, but what about resources like strings, colors or images? 

Currently there is no integrated way to share resources, so they have to be declared natively for each platform and then be collected via [expect/actual](https://kotlinlang.org/docs/multiplatform-connect-to-apis.html). [moko-resources](https://github.com/icerockdev/moko-resources) offers a missing link for sharing resources by placing them in the shared module. This tool seems to be heavily inspired by the mechanism Android uses which generates a Java file at the build time that contains references to all resources declared in XML (see [App resources overview](https://developer.android.com/guide/topics/resources/providing-resources)). moko-resources uses code generation similarly 

```
// shared
expect class Localization constructor(context: Context) {
    override fun getString(resource: StringResource): String
}
```

```
// androidMain
actual class Localization actual constructor(private val context: Context) {
    actual override fun getString(resource: StringResource): String {
        return StringDesc.Resource(resource).toString(context)
    }
}
```

```
// iosMain
actual class Localization actual constructor(context: Context) {
    actual override fun getString(resource: StringResource): String {
        return StringDesc.Resource(resource).localized()
    }
}
```



```
// shared
expect abstract class Context
```

```
// androidMain
actual typealias Context = android.content.Context
```

iOS does not use a context for localizing resources, so its implementation does not matter. We implement the actual class as Never in order to let the program fail fast and early if we mistakingly decide to use the Context on iOS.

```
// iosMain
actual class Context: Never
```