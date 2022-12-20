---
layout: post
title: Kotlin Multiplatform Mobile with Compose Multiplatform
date: 2022-11-13
categories:
public: false
---

Write once, deploy everywhere. This promise has been made time and time again but has yet to be fulfilled.

Microsoft introduced its Universal Windows Platform in 2015 which supported many Windows platforms and was used by the author with great enthusiasm until Windows 10 Mobile became a thing of the past. Xamarin has been published even earlier and will soon be superseded by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their platform, but they are far from leaving the field. 

Google on the other hand does not only feature its own mobile platform but also introduced one of the most interesting multiplatform frameworks today named Flutter. The drawbacks of Flutter on the other hand are big ones: Instead of bringing native developers together, it introduces a new platform with all the expected nooks and crannies. Flutter uses Dart, a programming language that has yet to set foot outside mobile development. Its toolchain is built on top of the existing ones and creates one additional layer of complexity in addition to what we already have to work with.

The same applies to frameworks like Adobe Cordova, Capacitor or React Native which utilize the power of the web but naturally are a far cry from a native experience most are striving for.

Diclaimer: This blogpost is not meant as a rating of or comparison between Kotlin Multiplatform Mobile, Flutter and other multiplatform frameworks. Most of these frameworks have their right to exist and the choice depends on multiple factors, such or strategic requirements like developers and skills available. Kotlin Multiplatform Mobile is currently in Beta and Compose Multiplatform has only recently started to gain traction on iOS.

# Kotlin Multiplatform Mobile

I want to focus on the transition from native to multiplatform development including the changes to the toolchain, framework and libraries. This blogpost is not a guide about how to setup a multiplatform project as this has already been covered by multiple awesome articles out there.

## Toolchain

### Programming language

Kotlin

### Integrated development environment

Google decided to outsource the development of an integrated development environment (IDE) for their platform and forked JetBrains' IntelliJ to create [Android Studio](https://developer.android.com/studio). Apple on the other hand decided to develop their own IDE with [Xcode](https://developer.apple.com/xcode) which leaves many features to be desired but can be accompanied by yet another fork of IntelliJ called [AppCode](https://www.jetbrains.com/de-de/objc). This situation might change as JetBrains just announced the [sunset of AppCode in December 2022](https://blog.jetbrains.com/appcode/2022/12/appcode-2022-3-release-and-end-of-sales-and-support).

Coming from this toolchain the learning curve for transitioning developers has been kept as minimal as possible. KMM uses Android Studio in tandem with Xcode as did Flutter before. This means only seasoned Xcode users have to learn a new IDE in order to create multiplatform code. The native iOS part of KMM still uses Xcode.

Conclusion: Android Studio 

### Build automation

Gradle is used as build automation tool which feels like home for native Android developers. iOS developers on the other hand have to learn yet another tool after Carthage, Cocoapods and Swift Package Manager while keeping one of them for the native side of the build process.

Conclusion: Gradle and is absolutely production-ready.

## Quality assurance

### Dependency Injection

Forget your state-of-the-art dependency injection framework as it is platform-dependent. This refers to [Hilt](https://developer.android.com/training/dependency-injection/hilt-android) on Android, which relies on the JVM, and to [Resolver](https://github.com/hmlongco/Resolver), [Swinject](https://github.com/Swinject/Swinject), [needle](https://github.com/uber/needle) and any other popular framework for iOS, which are written in native Swift.

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

There is no multiplatform way yet to test user interfaces created with Compose Multiplatform. This can be compensated by writing the UI tests natively with Espresso or XCTest.

### Mocking

Android embraces mocking due to its relation to the JVM which makes it compatible to multiple well-established mocking frameworks like Mockito. iOS on the other hand does not have this luxury since Swift is [The Only Modern Language without Mocking Frameworks](https://blog.pragmaticengineer.com/swift-the-only-modern-language-with-no-mocking-framework) due to its restrictive nature and read-only reflection. Therefore there are only a handful options to mock in KMM and they are heavily limited. [Mockative](https://github.com/mockative/mockative) and [MocKMP](https://github.com/kosi-libs/MocKMP) for example only support mocking interfaces.

## User interface

### Compose Multiplatform



## Architecture: MMVM

- DataSource?, e.g. Api
- Repository: Responsible for converting data, e.g. JSON to DTO, and creating Flows
- Use Cases?
- ViewModel: via expect/actual from Android and iOS
- View