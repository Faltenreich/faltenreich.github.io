---
layout: post
title: Compose Multiplatform with shared resources
description: One single source of truth for multiplatform resources
tags: kmp multiplatform compose
---

> This blogpost continues [Migrating to Kotlin Multiplatform](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md) and its part about [resources](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md#resources)

Kotlin Multiplatform, together with Compose Multiplatform, is a serious competition for multiplatform frameworks like Flutter or React Native. Besides business logic and user interfaces, one missing piece of the puzzle are multiplatform resources.

---

##### [Table of Contents](#table-of-contents)

1. [Plugins](#plugins)
    1. [Compose Multiplatform resources](#compose-multiplatform-resources)
    2. [MOKO resources](#moko-resources)
    3. [Comparison](#comparison)
    4. [Conclusion](#conclusion)
2. [Tutorial](#tutorial)
    1. [Migration from MOKO resources to Compose Multiplatform resources](#showcase)

---

## Plugins

As of April 2024 there is no official and stable solution that covers resource handling like we are used to from native Android or iOS development. Luckily there are solutions available that support multiplatform resources to a varying degree. We will dive into the most promising two.

### Compose Multiplatform resources

JetBrains added multiplatform resources to its [roadmap](https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/#compose-multiplatform) in November 2023. In February 2024 they released a first experimental version of `compose.components.resources` with [Compose Multiplatform 1.6.0](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.0). One month later "Resources" occupy a good chunk of the changelog for [Compose Multiplatform 1.6.1](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.1). The documentation can be found at [jetbrains.com](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-images-resources.html).

Compose Multiplatform resources works in a similar manner to Android's native [App resources](https://developer.android.com/guide/topics/resources/providing-resources). Resources accessors are generated during compilation (for everything within `composeResources`) and available through one single source of truth (`Res.kt`).

```
shared
    build/generated/compose/resourceGenerator/kotlin/<rootProject>/shared/generated/resources
        Res.kt
    src/commonMain/composeResources
        drawable
        files
        font
        values
```

`Res.kt` itself is an `object` providing accessors for every resource type.

```kotlin
internal object Res {

  public object drawable

  public suspend fun readBytes(path: String): ByteArray = readResourceBytes(path)

  public object font

  public object string
}
```

Compose Multiplatform resources provides representations for supported resource types, like `StringResource` or `DrawableResource`. Additional helper methods simplify access from within a Composable context, e.g.

```kotlin
@Composable
fun stringResource(resource: StringResource, vararg formatArgs: Any): String

@Composable
fun painterResource(resource: DrawableResource): Painter
```

### MOKO resources

[MOKO resources](https://github.com/icerockdev/moko-resources) works similarly to Compose Multiplatform resources but started development much earlier in 2019. It is one of many components by [IceRock Development](https://moko.icerock.dev) targeting Kotlin Multiplatform. Therefore its name: **MO**bile **KO**tlin project. This project is well-documented, has more than thirty contributors and almost 1k stars on GitHub. 

Unfortunately it has become somewhat stale, as issues are responded to sparsely and the last minor update `0.23.0` was almost a year ago. 2024 started promising with weekly alpha versions but that trend ended two months later. Personally I experienced one crash after upgrading the Android Gradle Plugin to 8.3.0 (see [#652](https://github.com/icerockdev/moko-resources/issues/652)) and the issue tracker is approaching a point where open issues outnumber closed ones.

### Comparison

Currently Compose Multiplatform resources is leagues behind MOKO resources. While the former does not support multi-module projects or splitting up resources into multiple files yet, the former's featureset is extensive and its configuration customizable. 

Both solutions support the usual resource types and common restrictions boil down to the lowest common denominator of every supported platform. Some platforms may not support certain types, like `.dimens` or `.mipmap`, or use proprietary ones, like `.xml` or `.mlmodel`. Colors on the other hand can be covered completely by the app's theme and therefor their single source of truth shifts from resources to the Composable realm.

| Resource type | Compose Multiplatform resources | MOKO resources | 
| ------------- | ------------------------------- | -------------- |
| String | ✅ (except plurals) | ✅ |
| Image (Rasterized) | ✅ (.png, .jpg, .bmp, .webp) | ✅ (.png, .jpg) |
| Image (Vectorized) | ✅ ([.xml](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources)) | ✅ (.svg) |
| Font | ✅ (.ttf, .otf) | ✅ (.ttf, .otf) |
| File | ✅ (raw) | ✅ (raw, assets) |
| Color | - | ✅ |

### Conclusion

As of April 2024 neither of the two solutions is production-ready. If one has to decide for a single source of truth, MOKO resources should be the way to go. But if both projects keep their current pace, I expect Compose Multiplatform resources to surpass MOKO resources in the near future.

## Tutorial

This tutorial documents challenges I faced during the rewrite of [Diaguard](https://github.com/Faltenreich/Diaguard). In April 2023 I decided to replace the then ten-years-old native Android app written in Java with Kotlin Multiplatform.

### Migration from MOKO resources to Compose Multiplatform resources

This tutorial describes the migration path from MOKO resources 0.24.0-alpha-5 to Compose Multiplatform resources 1.6.1. I tried to abstract everything domain-specific, so this can be applied to similar projects. I did not use any custom fonts but migrating those and other types should work similarly to strings and images.

**Prerequisites**

Dependencies for both MOKO resources and Compose Multiplatform resources have been implemented and are working. The project is single-module.

**Move resources**

Move resource folders:

```diff
- src/commonMain/moko-resources
- src/commonMain/resources/MR
+ src/commonMain/composeResources
```

Rename folders for resource types:

```diff
- base
+ values

- <locale>
+ values-<locale>

- images
+ drawable
```

**Convert resources**

Convert string templates from implicitly- to explicitly formatted, e.g. for strings placeholders:

```diff
- %s Hello, World %d
+ %1$s Hello, World %2$d
```

Convert vector drawables from `.svg` to `.xml` via Android Studio's `Resource Manager`

1. Resource Manager -> Drawable -> + -> Import Drawables
2. Select everything `.svg` that should be converted to `.xml`
4. Move the generated `.xml` to `src/commonMain/composeResources/drawable`

**Migrate imports**

Next we need to migrate all imports for the resource linkers that are bridging Kotlin to XML. Android developers know this bridge as `R.java` which will be generated during compilation in the build folder. MOKO resource calls it `MR` (by default) and places it at `<namespace>.MR`. Compose Multiplatform resources calls it `Res` and places it at `app.shared.generated.resources.Res`. 

This migration steps makes excessive use of `Edit/Find/Replace in Files…` to avoid touching every file that uses resources.

> Tip: Disable `Add unambiguous imports on the fly` and `Optimize imports on the fly` for `Kotlin` in `File/Settings/Editor/General/Auto Import`. This will keep Android Studio from scrambling imports up in the process and can be re-enabled after this chapter.

```diff
- import <namespace>.MR
+ import <rootProject>.shared.generated.resources.*

- import dev.icerock.moko.resources.compose.stringResource
+ import org.jetbrains.compose.resources.stringResource

- import dev.icerock.moko.resources.compose.painterResource
+ import org.jetbrains.compose.resources.painterResource

- import dev.icerock.moko.resources.ImageResource
+ import org.jetbrains.compose.resources.DrawableResource
```

The wildcard speeds up the transition of imports which would have otherwise be declared individually for every resource. If you want to avoid wildcards, optimize your imports afterwards via Android Studio's `Code / Optimize imports`.

**Migrate function calls**

Strings and images can be migrated project-wide, since MOKO resources and Compose Multiplatform resources share a similar API:

```diff
- MR.strings
+ Res.string

- MR.images
+ Res.drawable
```

Files do not yet support resource linking, so every call-site requires manual migration:

```diff
- MR.files.<filename>.readTextAsState()
+ runBlocking { Res.readBytes("files/<filename>.<filetype>").decodeToString() }
```

Let me break down those changes:

- Files are read asynchronously, so we use `runBlocking` for call-sites that operate synchronously
- Files are referenced by file name including -type and by their relative path, from `src/commonMain/composeResources`
- Files are returned as `ByteArray`, so we decode them, e.g. to `String` via `ByteArray.decodeToString(): String`

### Encapsulation

Encapsulatin helps a lot when working with moving targets that require repeated adjustment of the toolchain.

 Kotlin Multiplatform and especially Compose Multiplatform are still moving targets, I tried to encapsulate as much as possible in order to speed up migration paths. This helped a lot when swapping out functions resource file accress or other resources which would have been tedious if scattered across the project.

Encapsulation may have helped during the migration process, but since resources are such an essential part of every user-facing application, effort and return must be weighed up. In my case, I applied all described steps to migrate my small app within a few hours, thanks to a handful of functions provided by Android Studio. Then I tried to encapsulate everything redundant, like file access, but leaving access to the resource linker scattered across the project. This will be my sweet spot between simplifying another potential migration process and hoping that Compose Multiplatform resources is here to stay. Time will tell.