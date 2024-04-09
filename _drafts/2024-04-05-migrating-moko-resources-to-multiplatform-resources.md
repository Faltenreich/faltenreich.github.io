---
layout: post
image: 
title: Shared resources with Kotlin Multiplatform
description: One single source of truth for multiplatform strings and other resources
tags: kmp multiplatform compose
---

JetBrains 

This article describes my transition from MOKO resources to Compose Resources. Both solutions provide a good way of creating one single source of truth for resources but are far from stable as of April 2024 and come with their own specialties and caveats.

> Disclaimer: This blogpost continues a previous one about [Migrating to Kotlin Multiplatform](/_posts/2023-01-18-migrating-to-kotlin-multiplatform-mobile.md#resources)

---

##### [Table of Contents](#table-of-contents)

---

## Multiplatform resources

If Kotlin Multiplatform was a series, and Compose Multiplatform one season, then the next episode would be multiplatform resources. As of April 2024 there is no official and stable solution that covers resource handling like we are used to from native Android or iOS development. Luckily there are already multiple solutions that support multiplatform resources to a varying degree. We will dive into two of them.

### MOKO resources

MOKO resources started development in 2019 and became one of the largest third-party solutions for multiplatform resources. It is one of many components by [IceRock Development](https://moko.icerock.dev) targeting Kotlin Multiplatform. The project is well-documented, has more than thirty contributors and almost 1k stars. 

Unfortunately it has become somewhat stale, as issues are responded to sparsely and the last minor update `0.23.0` was almost a year ago. 2024 started promising with weekly alpha versions but that trend ended two months later. Personally I experienced one crash after upgrading the Android Gradle Plugin to 8.3.0 (see [#652](https://github.com/icerockdev/moko-resources/issues/652)) and the issue tracker is approaching a point where open issues outnumber closed ones.

### Compose resources

JetBrains added multiplatform resources to its [roadmap](https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/#compose-multiplatform) in November 2023. In February 2024 they released a first experimental version of `compose.components.resources` with [Compose Multiplatform 1.6.0](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.0). One month later "Resources" occupy a good chunk of the changelog for [Compose Multiplatform 1.6.1](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.6.1) which might be a good sign for this library to slowly gain track.

### Comparison

Currently MOKO is leagues ahead of JetBrains' solution: more features, more customization, less bugs. MOKO supports multi-module projects,  - features JetBrains has yet to implement. MOKO has all of those, even though they are far from reaching the same level as their native counterparts.
MOKO supports splitting up resources of the same type into multiple files. This feature is a little bit limited (e.g. string files require a `strings`-prefix) and not very well documented, but it is working.

Following matrix compares the feature-set of both solutions:

| Feature | MOKO | JetBrains |
| ------- | ---- | ------- |
| Strings | ✅ | ✅ (no plurals) |
| Rasterized images | ✅ (.png, .jpg) | ✅ (.png, .jpg, .bmp, .webp) |
| Vector images | ✅ (.svg) | ✅ ([.xml](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources)) |
| Fonts | ✅ (.ttf, .otf) |  |
| Files | ✅ (raw, assets) | ✅ |
| Colors | ✅ |  |
| Modularization | ✅ | ❌ |

Both solutions support rasterized and vectorized images, but the format of the latter differs from each other: MOKO uses SVG while JetBrains reuses the XML format we know from native Android [Vector drawables](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources).

### Conclusion

As of April 2024 neither of both solutions is production-ready. If one has to decide for a single source of truth, MOKO resources should be the best bet. That might change in the near future because if both projects keep their current pace, I expect JetBrains to surpass MOKO in a few months and become stable during 2025.

## Howto migrate from MOKO resources to Compose Resources

Next I will describe my transition from MOKO resources to Compose Resources. MOKO resources provides a more mature solution. I decided to migrate to JetBrains' solution nonetheless as I anticipate tighter release cycles and therefore a more full-fledged solution in the near future.

For this example we use a namespace of `com.author.app`.

Prequisite: Plugins for both solutions have been applied and the project is single-module.

### Move resources

- Move resources from `src/commonMain/moko-resources` (0.24.+) or `src/commonMain/resources/MR` (0.23.0 or older) to `src/commonMain/composeResources`
- Move string resources from `base` to `values` and `<locale>` to `values-<locale>`
- Rename folder for images from `images` to `drawable`

### Convert resources

- Convert string templates from implicitly formatted (`%s` or `%d`) to explicitly formatted (`%1$s` or `%1$d`)
- Convert vector drawables from `.svg` to `.xml` via Android Studio: `Tools/Resource Manager/Drawable/+/Import Drawables and move them to src/commonMain/composeResources/drawable`

### Find and replace

Next we will make excessive use of `Edit/Find/Replace in Files...` to avoid touching every file dependant on resources.

Prequisite: Disable `Add unambiguous imports on the fly` and `Optimize imports on the fly` for `Kotlin` in `File/Settings/Editor/General/Auto Import`. This will keep Android Studio from scrambling imports up in the process and can be re-enabled after this chapter.

Core element of the . Android developers know them as `R.java` which will be generated at `TODO`. MOKO calls it `MR` and places it at `com.author.app.MR`. JetBrains calls it `Res` and places it at `app.shared.generated.resources.Res`. 

By default the import path changes from `<namespace>.MR` to `<rootProject>.shared.generated.resources.*`. That wildcard speeds up the transition of imports which would have otherwise be declared individually for every resource. If you want to avoid wildcards, optimize your imports afterwards.


### Migrate function calls

MOKO and Compose share a similar API which makes it easy to migrate from one to the other. `PainterResource` and `StringResource` are called the same and only differ by import.

- `import com.faltenreich.diaguard.MR` becomes `import diaguard.shared.generated.resources.*`
- `import dev.icerock.moko.resources.compose.stringResource` becomes `import org.jetbrains.compose.resources.stringResource`
- `import dev.icerock.moko.resources.compose.painterResource` becomes `import org.jetbrains.compose.resources.painterResource`
- `import dev.icerock.moko.resources.ImageResource` becomes `import org.jetbrains.compose.resources.DrawableResource`

| MOKO | Compose |
| ---- | ------- |
| com.faltenreich.diaguard.MR | 