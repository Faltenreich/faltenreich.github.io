# Writing an app for Android and iOS with one codebase for business logic and UI using Kotlin Multiplatform Mobile and Compose Multiplatform

Writing once and deploying to multiple platforms. 

Cordova, Ionic, ... utilized the power of the web by taking TBD to the next level. 

Microsoft introduced its Universal Windows Platform in 2015 which supported multiple Windows platforms and was used by the author with great enthusiasm until Windows 10 Mobile became a thing of the past. Xamarin has been published even earlier and is one of the longest-living cross-platform frameworks still available and soon to be by the [.NET Multi-Platform App UI](https://learn.microsoft.com/de-de/dotnet/maui) (MAUI). Microsoft may have lost their team, but they are far from leaving the field. 

Google on the other hands does not only feature its own platform that , but with Flutter one of the most interesting multiplatform frameworks today. The drawbacks of Flutter on the other hand are big ones: Instead of bringing native developers together it introduces a new platform with all the nooks and crannies one could expect from it. Flutter uses Dart, a programming language that has yet to set foot outside mobile development. The toolchain builds on the existing ones and creates one additional layer of complexity in addition to what we already have to work with.

Diclaimer: This blogpost is not meant as a rating of or comparison between Kotlin Multiplatform Mobile, Flutter and other multiplatform frameworks. Most of these frameworks have their right to exist and the choice depends on multiple factors, such or strategic requirements like developers and skills available. Kotlin Multiplatform Mobile is currently in Beta and Compose Multiplatform has only recently started to gain traction on iOS.

Introdu

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