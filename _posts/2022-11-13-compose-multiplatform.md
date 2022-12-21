---
layout: post
title: Migrating to Kotlin Multiplatform Mobile
date: 2022-11-13
categories:
public: false
---

Write once, deploy everywhere. 

This promise has been made time and time again for many years but there is still so much potential left before the final answer.

Microsoft introduced its Universal Windows Platform in 2015 which supported many Windows platforms and was used by the author with great enthusiasm until Windows Mobile became a thing of the past. The acquisition of Xamarin in 2016 marked the next stepping stone in going multiplatform and will soon be superseded by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their platform, but they are far from leaving the field. 

Apple does not live under the same rock it started iOS with and opened itself up a little bit by going open-source with Swift and making iOS apps compatible with macOS powered by an Apple Silicon chip. This new-found openness ends at the borders of its own ecosystem though.

Google on the other hand does not only feature its own platform but introduced one of the leading multiplatform frameworks as well. Flutter supports mobile, desktop and web, has a steep learning curve and is completely open-source, but comes with its fair share of challenges. Instead of bringing native developers together, it introduces an additional platform with its own programming language and layer of complexity.

Entering Kotlin Multiplatform Mobile.

> Disclaimer: This blogpost is not meant as a rating of or comparison between multiplatform frameworks. They all have their right to exist and the choice depends on numerous factors, some of them even beyond the developers' control.

# Kotlin Multiplatform Mobile

Kotlin Multiplatform Mobile (KMM) supports both Android and iOS. Since its introduction in 2020 many companies have been joining the platform, such as Netflix, VMWare and Philips. In October 2022 [it finally went Beta](https://blog.jetbrains.com/kotlin/2022/10/kmm-beta) and has been declared production-ready which was accompanied by Google's announcement of an [experimental preview of libraries for this platform](https://android-developers.googleblog.com/2022/10/announcing-experimental-preview-of-jetpack-multiplatform-libraries.html). Therefore it is safe to say that KMM has left its early days and is finally starting to gain track.

This blogpost focusses on the transition from native- to multiplatform development with KMM. Readers will get a quick overview of the tools (not) available in 2023 including toolchain, frameworks and libraries. This is not a guide about how to setup and run a multiplatform project as this has already been covered by numerous articles out there.

## Toolchain

Behind every complex application there is a toolchain that supports continuous development. KMM makes no difference and stands on the shoulders of well-established tools forged mostly by Czech development company JetBrains and multiple open-source consortiums.

### Integrated development environment

Google outsourced the development of an integrated development environment (IDE) for their platform and forked JetBrains' IntelliJ to create [Android Studio](https://developer.android.com/studio). Apple on the other hand developed their own IDE with [Xcode](https://developer.apple.com/xcode) which leaves many features to be desired but can be accompanied by yet another fork of IntelliJ called [AppCode](https://www.jetbrains.com/de-de/objc). This situation might change as JetBrains just announced the [sunset of AppCode in December 2022](https://blog.jetbrains.com/appcode/2022/12/appcode-2022-3-release-and-end-of-sales-and-support).

**Android Studio** is being used for KMM in tandem with **Xcode**, similar as Flutter did it before. This means a well-known environment for native Android-, and even Flutter developers, and only iOS developers without AppCode need to learn a new IDE. Xcode is being used for the native iOS part anyway as its build tools are needed to compile, sign and archive apps for Apple's ecosystem. This also means that macOS is required to deploy an iOS app, even though some constellations may use Linux or Windows when focussing on Android while leaving iOS for the build server.

### Programming language

Android and iOS both started with an established programming language before switching to a modern, interoperable alternative. Kotlin and Swift have been created in a similar period of time and therefore share many paradigms like object-orientation, static type checking, suspending functions, optionals and some sort of garbage collection. Both languages put a heavy focus on extensibility which is why they are quickly adopting modern techniques like functional, reactive or declarative programming and share similar mechanisms for things like asynchronicity or user interfaces.

**Kotlin** is the one and only programming language for KMM, as the name implies. This means no change for Android developers using Kotlin which should be the majority by now. iOS developers will have to learn a new programming language but should be able to quickly gain a foothold by transferring existing knowledge from Swift to Kotlin.

### Build automation



**Gradle** is used as build automation tool which feels like home for native Android developers. iOS developers on the other hand have to learn yet another tool after Carthage, Cocoapods and Swift Package Manager while keeping one of them for the native side of the build process.

## Quality assurance

### Dependency Injection

Forget your state-of-the-art dependency injection framework as it is platform-dependent. This refers to [Hilt](https://developer.android.com/training/dependency-injection/hilt-android) on Android, which relies on the JVM, and to [Resolver](https://github.com/hmlongco/Resolver), [Swinject](https://github.com/Swinject/Swinject), [needle](https://github.com/uber/needle) and any other popular framework for iOS, which all are written in native Swift.

Fortunately Kotlin is being ported to more and more platforms and therefore some of its dependency inection frameworks have already made the transition to Kotlin/Native which makes them compatible with KMM. [Koin](https://insert-koin.io) and [Kodein](https://kodein.net/oss.html) are two heavily used, extensively documented and thoroughly tested examples.

## Testing

Unit tests can be implemented using [kotlin.test](https://kotlinlang.org/api/latest/kotlin.test) which shares many parts of its API with JUnit, a testing framework familiar to Android- or Java developers. 

```
import kotlin.test.Test
import kotlin.test.assertEquals

internal class SampleTest {

    @Test
    fun testSum() {
        assertEquals(42, 40 + 2)
    }
}
```

There is no way yet to test user interfaces created with Compose Multiplatform. This can be compensated by writing the UI tests natively with Espresso or XCTest.

### Mocking

Android embraces mocking due to its relaxed reflection and its tight relation to the JVM which makes it compatible to multiple well-established mocking frameworks like Mockito. iOS on the other hand does not have this luxury since Swift is "[The Only Modern Language without Mocking Frameworks](https://blog.pragmaticengineer.com/swift-the-only-modern-language-with-no-mocking-framework)" due to its restrictive nature and read-only reflection. Therefore there are only a handful options to mock in KMM and they are heavily limited. [Mockative](https://github.com/mockative/mockative) and [MocKMP](https://github.com/kosi-libs/MocKMP) for example only support mocking interfaces.

# Libraries

## Date

[kotlinx.datetime](https://github.com/Kotlin/kotlinx-datetime)
[Klock](https://github.com/korlibs/klock)

## Persistence

SQLDelight
Realm
Kodein-DB

## Backend-as-a-Service

[Firebase](https://firebaseopensource.com/projects/gitliveapp/firebase-kotlin-sdk) (Third party)

## Asynchronicity

[Coroutines](https://kotlinlang.org/docs/coroutines-overview.html)

## Serialization

[kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization)

## Networking

[Ktor](https://ktor.io)

## Image loading

Korim

# User interface

Up to this point, we have moved under the hood. Up to this point, KMM allows us to reuse business logic but requires separate implementations regarding the user interface for each platform. What if we could reuse this implementation as well?

Entering Compose Multiplatform.

## Compose Multiplatform

[Compose Multiplatform](https://www.jetbrains.com/de-de/lp/compose-mpp) elevates [Jetpack Compose](https://developer.android.com/jetpack/compose) and makes this declarative design framework compatible with KMM. This marks the next step in programming with KMM as not only the business layer can be shared between platforms but the user interface itself can be. The only native components left are one Android Activity and one iOS view respectively that each embed the multiplatform composables in their native app.

```
// shared
@Composable
fun MainView() {
    Text("Hello, world")
}

// androidApp
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent { MainView() }
    }
}

// TODO: Verify
// iosApp
@main
struct iOSApp: App {
    var body: some Scene {
        WindowGroup {
            MainView()
        }
    }
}
```

Compose Multiplatform is working fine on Android and technically also on iOS. Support for the latter has been introduced with version 1.2.0-alpha1 early 2022 and is still in alpha state. This becomes apparent as the experimental support for Apple's UIKit has to be enabled via opt-in and features like the preview do not work yet. There might be another year around the corner until Compose Multiplatform becomes production-ready for Apple's platform but the foundation has been laid out and shows big potential.

## Material 3

Google's design language received its latest update with Material 3 including Material You. This dependency is experimental but works for KMM as well as natively. The only difference is the amount of supported components since every ui element must be supported on both platforms. If there is no pendant on iOS, then Android will not be supported either.

## Resources

[moko-resources](https://github.com/icerockdev/moko-resources)