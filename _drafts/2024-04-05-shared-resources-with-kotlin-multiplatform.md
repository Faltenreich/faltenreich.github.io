---
layout: post
image: 
title: Shared resources with Kotlin Multiplatform
description: One single source of truth for multiplatform resources
tags: kmp multiplatform compose
---

Kotlin Multiplatform, together with Compose Multiplatform, is a serious competition for multiplatform frameworks like Flutter or React Native. Besides business logic and user interfaces, one missing piece of the puzzle are multiplatform resources.

> This blogpost continues [Migrating to Kotlin Multiplatform](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md) and its part about [resources](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md#resources)

---

##### [Table of Contents](#table-of-contents)

1. [MOKO resources](#moko-resources)
2. [JetBrains resources](#jetbrains-resources)
3. [Comparison](#comparison)
4. [Conclusion](#conclusion)
3. [Showcase: Migrating from MOKO resources to JetBrains resources](#showcase)

---

As of April 2024 there is no official and stable solution that covers resource handling like we are used to from native Android or iOS development. Luckily there are solutions available that support multiplatform resources to a varying degree. We will dive into two of them.

### MOKO resources

[MOKO resources](https://github.com/icerockdev/moko-resources) started development in 2019 and became one of the largest third-party solutions for multiplatform resources. It is one of many components by [IceRock Development](https://moko.icerock.dev) targeting Kotlin Multiplatform. Therefore its name: **MO**bile **KO**tlin project. This project is well-documented, has more than thirty contributors and almost 1k stars on GitHub. 

Unfortunately it has become somewhat stale, as issues are responded to sparsely and the last minor update `0.23.0` was almost a year ago. 2024 started promising with weekly alpha versions but that trend ended two months later. Personally I experienced one crash after upgrading the Android Gradle Plugin to 8.3.0 (see [#652](https://github.com/icerockdev/moko-resources/issues/652)) and the issue tracker is approaching a point where open issues outnumber closed ones.

### JetBrains resources

JetBrains added multiplatform resources to its [roadmap](https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/#compose-multiplatform) in November 2023. In February 2024 they released a first experimental version of `compose.components.resources` with [Compose Multiplatform 1.6.0](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.0). One month later "Resources" occupy a good chunk of the changelog for [Compose Multiplatform 1.6.1](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.1). The documentation can be found at [jetbrains.com](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-images-resources.html).

> We will continue to name this solution "JetBrains resources" in the following text, as there is no clear indication of whether or how JetBrains wants to identify it.

### Comparison

Currently the solution by IceRock Development is leagues ahead of JetBrains'. While the former's featureset is extensive and its configuration customizable, the latter misses many features and does not support multi-module projects or splitting up resources into multiple files yet, which might be dealbrakers.

Following types of resources are supported:

| Resource | MOKO | JetBrains |
| -------- | ---- | -------- |
| String | ✅ | ✅ (except plurals) |
| Image (Rasterized) | ✅ (.png, .jpg) | ✅ (.png, .jpg, .bmp, .webp) |
| Image (Vectorized) | ✅ (.svg) | ✅ ([.xml](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources)) |
| Font | ✅ (.ttf, .otf) | ✅ (.ttf, .otf) |
| File | ✅ (raw, assets) | ✅ (raw) |
| Color | ✅ | ❌ |

Compared to native solution this set seems limited and most likely boils down to the lowest common denominator of every supported platform. Some platforms may not support certain types, like `.dimens` or `.mipmap`, or use proprietary ones, like `.xml` or `.mlmodel`. Colors on the other hand can be covered completely by the app's theme and therefor their single source of truth shifts from resources to the Composable realm.

### Conclusion

As of April 2024 neither of the two solutions is production-ready. If one has to decide for a single source of truth, MOKO resources should be the best bet. That might change in the near future because if both projects keep their current pace, I expect JetBrains to surpass MOKO in the upcoming months and become stable late 2025 / early 2026.

## Showcase: Migrating from MOKO resources to JetBrains resources

This showcase describes the migration path from MOKO resources to JetBrains resources. I did this for [Diaguard](https://github.com/Faltenreich/Diaguard) which is currently being rewritten from native Android with Java to Kotlin Multiplatform using technologies mentioned in this blogpost and the [previous one](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md). I tried to abstract everything domain-specific, so this can be applied to similar projects. I did not use any custom fonts but migrating those and other types should work similarly to strings and images.

### Prerequisite

The project is single-module and dependencies for both MOKO resources and JetBrains resources have been implemented.

### Move resources

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

### Convert resources

Convert string templates from implicitly- to explicitly formatted, e.g. for strings placeholders:

```diff
- %s Hello, World %d
+ %1$s Hello, World %2$d
```

Convert vector drawables from `.svg` to `.xml` via Android Studio's `Resource Manager`

1. Resource Manager -> Drawable -> + -> Import Drawables
2. Select everything `.svg` that should be converted to `.xml`
4. Move the generated `.xml` to `src/commonMain/composeResources/drawable`

### Migrate imports

Next we need to migrate all imports for the resource linkers that are bridging Kotlin to XML. Android developers know this bridge as `R.java` which will be generated during compilation in the build folder. MOKO resource calls it `MR` (by default) and places it at `<namespace>.MR`. JetBrains resources calls it `Res` and places it at `app.shared.generated.resources.Res`. 

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

### Migrate function calls

Strings and images can be migrated project-wide, since MOKO resources and JetBrains resources share a similar API:

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

### Summary

As always, encapsulating access to the resource handling may have seemed a good idea to streamline the migration process. But since resources are such an essential part of every user-facing application, the effort might not be worth this cost. In my case, I applied all described steps to migrate my small app within a few hours, thanks to a handful of functions provided by Android Studio. Then I tried to encapsulate everything redundant, like file access, but leaving access to the resource linker scattered across the project. This will be my sweet spot between simplifying another potential migration process and hoping that JetBrains resources is here to stay. Time will tell.