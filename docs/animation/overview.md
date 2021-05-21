Jetpack Compose 提供了强大的、可扩展的 API，使得在你的应用程序的用户界面上实现各种动画变得容易。本文描述了如何使用这些 API，以及根据你的动画场景使用哪个 API。


## 1. 概述

为了实现流畅和可理解的用户体验，动画在现代移动应用中是必不可少的。许多 Jetpack Compose 动画 API 就像布局和其他 UI 元素一样，可以作为 ***Composable*** 函数，它们由用 Kotlin coroutine suspend 函数构建的低级API 支持。本指南从在许多实际场景中很有用的高级 API 开始，接着解释给你进一步控制和定制的低级 API。

下面这个图表可以帮助你决定使用什么 API 来实现你的动画。

<img src = "../../../assets/animation/overview/demo.svg">


| API | 功能|
| ----| ----|
| **[AnimationVisibility](animationvisibility.md)** | **进入/退出的过渡动画** |
| **Modifier.contentSize** | **内容大小的变化过渡动画** |
| **Crossfade** | **暂时还没探索** 
| **rememberInfiniteTransition** | **暂时还没探索** |
| **updateTransition** | **暂时还没探索** |
| **animate*AsState** | **指定类型的数据变化动画** |