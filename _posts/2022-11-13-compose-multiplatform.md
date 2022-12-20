---
layout: post
title: Migrating to Kotlin Multiplatform Mobile in 2023
date: 2022-11-13
categories:
public: false
---

Write once, deploy everywhere. 

This promise has been made time and time again for many years but there is still so much potential left before the final answer.

Microsoft introduced its Universal Windows Platform in 2015 which supported many Windows platforms and was used by the author with great enthusiasm until Windows 10 Mobile became a thing of the past. Xamarin has been published even earlier and will soon be superseded by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their platform, but they are far from leaving the field. 

Google on the other hand does not only feature its own mobile platform but also introduced one of the most interesting multiplatform frameworks today named Flutter. The drawbacks of Flutter on the other hand are big ones: Instead of bringing native developers together, it introduces a new platform with all the expected nooks and crannies. Flutter uses Dart, a programming language that has yet to set foot outside mobile development. Its toolchain is built on top of the existing ones and creates one additional layer of complexity in addition to what we already have to work with.

The same applies to frameworks like Adobe Cordova, Capacitor or React Native which utilize the power of the web but naturally are a far cry from a native experience most are striving for.

> This blogpost is not meant as a rating of or comparison between multiplatform frameworks. They all have their right to exist and the choice depends on numerous factors, some of them even beyond the developers' control.

Entering Kotlin Multiplatform Mobile.

# Kotlin Multiplatform Mobile

Kotlin Multiplatform Mobile (KMM) supports both Android and iOS. Since its introduction in 2020 many companies have been joining the platform, such as Netflix, VMWare and Philips. In October 2022 [it finally went Beta](https://blog.jetbrains.com/kotlin/2022/10/kmm-beta) and has been declared production-ready which was accompanied by Google's announcement of an [experimental preview of libraries for this platform](https://android-developers.googleblog.com/2022/10/announcing-experimental-preview-of-jetpack-multiplatform-libraries.html). Therefore it is safe to say that KMM has left its early days 

I want to focus on the transition from native to multiplatform development including the changes to the toolchain, framework and libraries. This blogpost is not a guide about how to setup a multiplatform project as this has already been covered by multiple awesome articles out there.

Instead it should give seasoned native developers a quick migration guide to tools, frameworks and libraries that they use in most of their projects.

This blogpost will give a quick overview of the tools (not) available for KMM in 2023 and marks the start of a series of blogposts related to professional development using KMM.

## Toolchain

### Integrated development environment

Google decided to outsource the development of an integrated development environment (IDE) for their platform and forked JetBrains' IntelliJ to create [Android Studio](https://developer.android.com/studio). Apple on the other hand decided to develop their own IDE with [Xcode](https://developer.apple.com/xcode) which leaves many features to be desired but can be accompanied by yet another fork of IntelliJ called [AppCode](https://www.jetbrains.com/de-de/objc). This situation might change as JetBrains just announced the [sunset of AppCode in December 2022](https://blog.jetbrains.com/appcode/2022/12/appcode-2022-3-release-and-end-of-sales-and-support).

**Android Studio** is being used for KMM in tandem with Xcode, similar as Flutter did it before. This means a well-known environment for native Android-, and even Flutter developers, and only iOS developers without AppCode need to learn a new IDE. Xcode is being used for the native iOS part anyway as its build tools are needed to compile, sign and archive apps for Apple's ecosystem. This also means that macOS is required to deploy an iOS app, even though some constellations may use Linux or Windows when focussing on Android while leaving iOS for the build server.

### Programming language

**Kotlin** is the one and only programming language for KMM. This means no change for Android developers using Kotlin which should be the majority by now. iOS developers will have to learn a new programming language but should be able to transfer knowledge from Swift to Kotlin since both share many paradigms like object-orientation, static type checking, suspending functions, optionals and some sort of garbage collection. Both languages have been created in a similar period of time and put a heavy focus on extensibility which is why they are quickly adopting modern techniques like functional, reactive or declarative programming and share similar mechanisms for things like asynchronicity or user interfaces.

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

Android embraces mocking due to its relation to the JVM which makes it compatible to multiple well-established mocking frameworks like Mockito. iOS on the other hand does not have this luxury since Swift is [The Only Modern Language without Mocking Frameworks](https://blog.pragmaticengineer.com/swift-the-only-modern-language-with-no-mocking-framework) due to its restrictive nature and read-only reflection. Therefore there are only a handful options to mock in KMM and they are heavily limited. [Mockative](https://github.com/mockative/mockative) and [MocKMP](https://github.com/kosi-libs/MocKMP) for example only support mocking interfaces.

# User interface

Up to this point, we have moved under the hood. Up to this point, KMM allows us to reuse business logic but requires separate implementations regarding the user interface for each platform. What if we could reuse this implementation as well?

Entering Compose Multiplatform.

## Compose Multiplatform

Mobile development has embraced declarative programming in the last years. After Flutter came iOS with SwiftUI and shortly after followed Android with Jetpack Compose. Compose Multiplatform elevates Jetpack Compose and combines this declarative framework with KMM in order to make Compose available for multiple platforms. This marks the next step in programming with KMM as not only the business layer can be shared between platforms but the user interface itself. The only native components left are one Android Activity and one iOS view respectively that each embed the multiplatform user interface in their native app.

```
// gradle.properties
org.jetbrains.compose.experimental.uikit.enabled=true

// settings.gradle
pluginManagement {
    repositories {
        maven("https://maven.pkg.jetbrains.space/public/p/compose/dev")
    }
}

// build.gradle.kts
plugins {
    id("org.jetbrains.compose") version "<version>" apply false
}

// shared/build.gradle.kts
plugins {
    id("org.jetbrains.compose")
}
kotlin {
    sourceSets {
        val commonMain by getting {
            implementation(compose.ui)
            implementation(compose.foundation)
            implementation(compose.runtime)
            @OptIn(ExperimentalComposeLibrary::class)
            implementation(compose.material3)
            // Not yet supported on iOS
            // implementation(compose.preview)
        }
    }
}
```

Once this setup has been completed, calling composables built with Compose Multiplatform from native Android code is as easy as integrating Jetpack Compose:

```
// build.gradle.kts
dependencies { 
    implementation("androidx.activity:activity-compose:<version>")
}

// MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent { MainView() }
    }
}
```

// TODO: Verify
Calling Compose Multiplatform from native iOS code is almost as easy as integrating SwiftUI in a legacy UIKit application:

```
// iOSApp.swift
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



## Architecture: MMVM

- DataSource?, e.g. Api
- Repository: Responsible for converting data, e.g. JSON to DTO, and creating Flows
- Use Cases?
- ViewModel: via expect/actual from Android and iOS
- View