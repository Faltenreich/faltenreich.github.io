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

While concepts were adopted and both platforms influenced each other, frameworks and libraries for handling data have largely been developed in isolation.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Date and time | [Date/Time](https://www.baeldung.com/java-8-date-time-intro) | [Date](https://developer.apple.com/documentation/foundation/date) | [kotlinx.datetime](https://github.com/Kotlin/kotlinx-datetime) | 
| Serialization | [Moshi](https://github.com/square/moshi) | [Codable](https://developer.apple.com/documentation/foundation/archives_and_serialization) | [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) |
| Persistence | [Room](https://developer.android.com/training/data-storage/room) | [Core Data](https://developer.apple.com/documentation/coredata) | [SQLDelight](https://cashapp.github.io/sqldelight/1.5.4/multiplatform_sqlite) |
| Networking | [Retrofit](https://square.github.io/retrofit) | [AFNetworking](https://github.com/AFNetworking/AFNetworking) | [Ktor](https://ktor.io) |
| Concurrency | [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-overview.html) | [async/await](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html) | [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-overview.html) |
| Streaming | [Kotlin Flow](https://kotlinlang.org/docs/flow.html) | [Combine](https://developer.apple.com/documentation/combine) | [Kotlin Flow](https://kotlinlang.org/docs/flow.html) |

Technologies that are marketed cross-platform on the other hand, do support multiple platforms but tend to being implemented natively and separatedly. [ObjectBox](https://objectbox.io) for example is still [looking for help](https://github.com/objectbox/objectbox-java/issues/601) to migrate to Kotlin/Native while [Realm](https://realm.io) has percieved it as a separate platform and therefore introduced a new project for [Realm Kotlin](https://github.com/realm/realm-kotlin) (including an extensive [migration guide](https://www.mongodb.com/docs/realm/sdk/kotlin/migrate-from-java-sdk-to-kotlin-sdk)).

### Backend-as-a-Service

Firebase is one of the most important tools when it comes to mobile-Backend-as-a-Service (mBaaS). Unfortunately there is no official support for KMM yet, but there are multiple options available to implement Firebase into a KMM project. [Firebase Kotlin SDK](https://github.com/gitliveapp/firebase-kotlin-sdk) is a Kotlin-first SDK for Firebase that wraps the respective official Firebase SDK for each supported platform and provides a common API, even though its coverage is far from complete and new releases are a long time in coming. Touchlab introduced a [Firestore SDK for Kotlin Multiplatform](https://github.com/touchlab-lab/FirestoreKMP) in 2019 without ongoing support but at that time already showed how multiplatform wrappers for KMM may look like. 

[Parse Server](https://parseplatform.org) has one closed issue regarding Kotlin/Native with one developer summarizing: "I'm not sure this is a direction / strategy that we'll pursue in any case. If this is something you want to pursue (build a kotlin SDK) feel free to get started." (via [github.com](https://github.com/parse-community/Parse-SDK-Android/issues/728)). The [AWS Amplify](https://aws.amazon.com/de/amplify/) team has stated that "it's unlikely that we we'll ever prioritize a full multi-platform build. But if we see enough customer demand, we could potentially add more explicit support for Multiplatform Mobile." (via [stackoverflow](https://stackoverflow.com/a/66234604/3269827)) and this seems to be the gist for most mBaaS: They have only recently gone to great lengths to support Flutter, so KMM may not be a top priority in the near future.

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

Compose Multiplatform is working on Android and technically also on iOS. Support for the latter has been introduced with version 1.2.0-alpha1 early 2022 and is still in alpha state. This becomes apparent as the experimental support for Apple's UIKit has to be enabled via opt-in and features like the preview or JUnit 4 extensions do not work yet.

### Resources

Since Compose Multiplatform allows multiplatform user interfaces, it would be nice to have a single source of truth for resources as well. Currently there is no integrated way to share resources, so they have to be declared natively on each platform. 

**[moko-resources](https://github.com/icerockdev/moko-resources)** offers a solution for this missing link and supports the multiplatform declaration of resources. It seems to be heavily inspired by Android which uses code generation to create a Java file at build time that contains references to all resources declared in XML files (see [App resources overview](https://developer.android.com/guide/topics/resources/providing-resources)). moko-resources works similarly.

```
import <package>.MR

@Composable
fun ExampleView(localization: Localization) {
    Text(localization.getString(MR.strings.example))
}
```

With the help of moko-resources, resources can be declared in the shared module and accessed via built-in methods. These methods are platform-dependant but can be encapsulated furthermore using expected and actual declarations in order to offer a platform-independent way to access resources.

```
// shared
expect class Localization constructor(context: Context) {
    override fun getString(resource: StringResource): String
}

// androidMain
actual class Localization actual constructor(private val context: Context) {
    actual override fun getString(resource: StringResource): String {
        return StringDesc.Resource(resource).toString(context)
    }
}

// iosMain
actual class Localization actual constructor(context: Context) {
    actual override fun getString(resource: StringResource): String {
        return StringDesc.Resource(resource).localized()
    }
}
```

Since Android relies on its context to load resources according to its configuration, we need to pass it to the localization mechanism using expected and actual declarations once more. iOS does not use a context for localizing resources, so we implement its actual class as Never in order to fail fast and early if we mistakingly decide to use the context anyway.

```
// shared
expect abstract class Context

// androidMain
actual typealias Context = android.content.Context

// iosMain
actual class Context: Never
```

### Material

Google's design components are available for Compose Multiplatform but offer a different set of components compared to Android since KMM requires support for every platform. If there is no pendant on iOS, then Android will not be supported either. One example of this is the DropdownMenu which has no counterpart on iOS and is therefore not available. Material is currently transitioning to version 3 which is still experimental and has to be enabled via opt-in.

| Requirement | Android | iOS | KMM |
| ----------- | ------- | --- | --- |
| Material | [Compose Material](https://developer.android.com/jetpack/androidx/releases/compose-material) | [Material Components for iOS](https://github.com/material-components/material-components-ios) | [Compose Material Components](https://mvnrepository.com/artifact/org.jetbrains.compose.material/material) |
| Material3 | [Compose Material 3](https://developer.android.com/jetpack/androidx/releases/compose-material3) | *unsupported* | [Compose Material3 Components](https://mvnrepository.com/artifact/org.jetbrains.compose.material3/material3) |

## Addendum

In my opinion there might be at least another year around the corner until Compose Multiplatform becomes production-ready for mobile, but the foundation has been laid out and shows big potential.

Firebase shows. There may not be a SDK for every yet, but 