# Writing an app for Android and iOS with one codebase for business logic and UI using Kotlin Multiplatform Mobile and Compose Multiplatform

Write once, deploy everywhere. This promise has been made time and time again but has yet to be fulfilled.

Microsoft introduced its Universal Windows Platform in 2015 which supported many Windows platforms and was used by the author with great enthusiasm until Windows 10 Mobile became a thing of the past. Xamarin has been published even earlier and will soon be superseded by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their platform, but they are far from leaving the field. 

Google on the other hand does not only feature its own mobile platform but also introduced one of the most interesting multiplatform frameworks today named Flutter. The drawbacks of Flutter on the other hand are big ones: Instead of bringing native developers together it introduces a new platform with all the nooks and crannies one could expect from it. Flutter uses Dart, a programming language that has yet to set foot outside mobile development. The toolchain builds on the existing ones and creates one additional layer of complexity in addition to what we already have to work with.

The same applies to frameworks like Adobe Cordova, Capacitor or React Native which utilize the power of the web but naturally are a far cry from a native experience most are striving for.

Diclaimer: This blogpost is not meant as a rating of or comparison between Kotlin Multiplatform Mobile, Flutter and other multiplatform frameworks. Most of these frameworks have their right to exist and the choice depends on multiple factors, such or strategic requirements like developers and skills available. Kotlin Multiplatform Mobile is currently in Beta and Compose Multiplatform has only recently started to gain traction on iOS.

# Kotlin Multiplatform Mobile



## Dependency Injection

- Koin does not work out of the box with Compose Multiplatform, only with Jetpack Compose
- Cokoin works as a standalone extension to support this
- Kodeon does work with Compose Multiplatform

## Testing
- kotlin.test works almost like JUnit
- Mocking: mockk works only on JVM, MocKMP/Mockative work only with interfaces

## Architecture: MMVM

- DataSource?, e.g. Api
- Repository: Responsible for converting data, e.g. JSON to DTO, and creating Flows
- Use Cases?
- ViewModel: via expect/actual from Android and iOS
- View