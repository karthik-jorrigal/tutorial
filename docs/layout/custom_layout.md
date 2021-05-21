## 概述

Compose 已经内置了许多组件，诸如 Column，Row，Box 等。开发者可以通过这些组合这些已有的组件来定制自己的专属组件。

就像在传统View系统中，当 LinearLayout 等基础布局无法满足你的需求时，你可以通过重写 measure 与 layout 来达成你的期望。Compose 沿用了这一理念，在一些场景下如果 Compose 内置组件可能无法满足你的需求，可以尝试通过定制测量与布局过程来完成需求。事实上，Compose 内置组件也是通过定制 Layout 来达成的，只是一个更高层次的封装。

在学习如何定制 Layout 之前，我们需要先了解下 Compose 的布局原理。

## Compose 布局原理

composable 被调用时会将自身包含的UI元素添加到UI树中并在屏幕上被渲染出来。每个 UI 元素都有一个父元素，可能会包含零至多个子元素。每个元素都有一个相对其父元素的内部位置和尺寸。

每个元素都会被要求根据父元素的约束来进行自我测量(类似传统 View 中的 MeasureSpec )，约束中包含了父元素允许子元素的最大宽度与高度和最小宽度与高度，当父元素想要强制子元素宽高为固定值时，其对应的最大值与最小值就是相同的。

对于一些包含多个子元素的UI元素，需要测量每一个子元素从而确定当前UI元素自身的大小。并且在每个子元素自我测量后，当前UI元素可以根据其所需要的宽度与高度进行在自己内部进行放置

**Compose UI 不允许多次测量**，当前UI元素的每一个子元素均不能被重复进行测量，换句话说就是**每个子元素只允许被测量一次**。这样做的好处是什么？这样做的好处是为了提高性能。在传统 View 系统中一个 UI 元素允许多次测量子元素，我们假设对子元素测量两次，而该子元素可能又对其子元素又测量了两次，总体上当前 UI 元素重新测量一次，则孙子元素就需要测量四次，测量次数会随着深度而指数级上升。以此类推，那么一次布局整颗 UI 树都需要做大量的工作，很难保持应用程序的良好性能。 为避免传统 View 系统测量布局的性能陷阱，Compose 限制了每个子元素的测量次数，可以高效处理深度比较大的 UI 树(极端情况是退化成链表的树形结构)。但是在有些场景下，多次测量子元素是有意义的，我们是需要获取到子元素多次测量的信息的。对于这些情况，有办法做到这一点，我们将在后面讨论。

<iframe  width="780" height="615" src="//player.bilibili.com/player.html?aid=459420051&bvid=BV1ZA41137gr&cid=305252461&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

<br/>

## 使用 Layout Modifier

使用 <code>Modifier.layout()</code> 手动控制元素的测量和布局。通常 layout 修饰符的使用方法像下面这样。

```kotlin
fun Modifier.customLayoutModifier(...) = Modifier.layout { measurable, constraints ->
  ...
})
```

当使用 layout 修饰符时，你传入的回调 lambda 需要包含两个参数：measurable、constraints

measurable：子元素的测量句柄，通过提供的api完成测量与布局过程

constraints: 子元素的测量约束，包括宽度与高度的最大值与最小值。

### Layout Modifier 使用示例

有时你想在屏幕上展示一段文本信息，通常你会使用到 Compose 内置的 Text 组件。单单显示文本是不够的，你希望指定 Text 顶部到文本基线的高度，让文本看的更自然一些。使用内置的 padding 修饰符是无法满足你的需求的，他只能指定 Text 顶部到文本顶部的高度，此时你就需要使用到 layout 修饰符了。

<img src = "../../assets/layout/custom_layout/demo1.png" width = "50%" height = "50%">

我们首先创建一个  <code>firstBaselineToTop</code> 修饰符

```kotlin
fun Modifier.firstBaselineToTop(
  firstBaselineToTop: Dp
) = Modifier.layout { measurable, constraints ->
  ...
}
```

正如我们在 Compose 布局原理中所提到的，**每个子元素只允许被测量一次**。

通过使用 <code>measurable.measure(constraints)</code> 完成子元素的测量，如果将 lambda 的 constraints 直接传入则意味着你将父元素给当前元素的限制直接提供了当前元素的子元素，自身没有增加任何额外的限制。子元素测量的结果被包装在一个 <code>Placeable</code> 实例中，可通过该<code>Placeable</code> 实例获取子元素测量结果。

在我们的示例中当前 `Text` 元素也不对子元素进行额外限制。

```kotlin
fun Modifier.firstBaselineToTop(
  firstBaselineToTop: Dp
) = Modifier.layout { measurable, constraints ->
  val placeable = measurable.measure(constraints)
  ...
}
```

现在子元素已经完成了测量流程，你需要计算当前元素的打算并通过 <code>layout(width, height)</code> 方法对当前元素的宽度与高度进行指定。并将子元素的布局流程写入在 <code>layout(width, height)</code> 的 lambda 参数中。

在我们的示例中当前 `Text` 元素的宽度则是文本宽度，而高度则是我们指定的 `Text` 顶部到文本基线高度与文本基线到 `Text` 底部的高度之和。

```kotlin
fun Modifier.firstBaselineToTop(
  firstBaselineToTop: Dp
) = Modifier.layout { measurable, constraints ->
  val placeable = measurable.measure(constraints)
  check(placeable[FirstBaseline] != AlignmentLine.Unspecified)
  val firstBaseline = placeable[FirstBaseline]
  val placeableY = firstBaselineToTop.roundToPx() - firstBaseline
  val height = placeable.height + placeableY
  layout(placeable.width, height) {
    ...
  }
}
```

现在你可以通过使用 <code>placeable.placeRelative(x, y)</code> 来完成子元素的布局流程，这是必要的。<code>placeRelative</code> 会根据当前 <code>layoutDirection</code> 自动调整子元素的位置。

在我们的示例中，当前子元素的横向坐标相对当前元素为零，而纵向坐标则为 `Text` 组件顶部到文本顶部的距离。

```kotlin
fun Modifier.firstBaselineToTop(
  firstBaselineToTop: Dp
) = Modifier.layout { measurable, constraints ->
  ...
  val placeableY = firstBaselineToTop.roundToPx() - firstBaseline
  val height = placeable.height + placeableY
  layout(placeable.width, height) {
    placeable.placeRelative(0, placeableY)
  }
}
```

为预览布局结果，我们创建了两个预览视图。

```kotlin
@Preview
@Composable
fun TextWithPaddingToBaselinePreview() {
  LayoutsCodelabTheme {
    Text("Hi there!", Modifier.firstBaselineToTop(24.dp))
  }
}

@Preview
@Composable
fun TextWithNormalPaddingPreview() {
  LayoutsCodelabTheme {
    Text("Hi there!", Modifier.padding(top = 24.dp))
  }
}
```

预览效果

<img src = "../../assets/layout/custom_layout/demo2.png" width = "50%" height = "50%">

## 使用Layout Composable

Layout Modifier 会将当前元素的所有子元素视作为整体进行统一的测量与布局，多适用于统一处理的场景。然而我们有时是需要精细化测量布局每一个子组件，这需要我们进行完全的自定义 Layout。这类似于传统 View 系统中定制 View 与 ViewGroup 测量布局流程的区别。对于定制 “ViewGroup” 的场景，我们应该使用 Layout Composable 了。首先我们需要创建一个 Layout Composable。

```kotlin
@Composable
fun CustomLayout(
    modifier: Modifier = Modifier,
    // custom layout attributes 
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        // measure and position children given constraints logic here
    }
}
```

可以看到，Layout 需要填写三个参数：modifier，content，measurePolicy

**modifier**：由外部传入的修饰符，会决定该 UI 元素的 constraints

**content**：在 content 中声明所有子元素信息

**measurePolicy**：默认场景下只实现 measure 即可，上面示例中最后传入的 lambda 就是 measure 的实现。当你想要为你的 Layout Composable 适配 Intrinsics 时(官方中文翻译为固有特性测量)，则需要重写 <code>minIntrinsicWidth</code> 、<code>minIntrinsicHeight</code>、<code>maxIntrinsicWidth</code> 、<code>maxIntrinsicHeight</code> 方法，有关于固有特性测量的文章后续会更新，请持续关注。

### Layout Composable使用示例

我们可以通过 Layout Composable 定制一个自己专属的 Column，首先我们需要声明这个 Composable。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        // measure and position children given constraints logic here
    }
}
```

和 Layout Modifier 一样，我们需要对所有子组件进行一次测量。**切记，每个子元素只允许被测量一次**。

与 Layout Modifier 不同的是，这里的 measurables 是一个 List，而 Layout Modifier 则只是一个 measurable，因为他将所有子元素看作了一个整体。

在我们的示例中仍然不对子元素进行额外限制，最终将每次测量的结果保存到 placeables 这个 List 中。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        val placeables = measurables.map { measurable ->
            // Measure each child
            measurable.measure(constraints)
        }
    }
}
```

现在在将这些子元素布局之前，你需要计算当前定制 column 所应该占用的屏幕宽度与高度。这样为了出于简单考虑，选择将宽度与高度设置为其父元素所允许的最大高度与宽度。与 Layout Modifier 一样通过 <code>layout(width, height)</code> 方法对当前元素的宽度与高度进行指定。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        ...
        layout(constraints.maxWidth, constraints.maxHeight) {
            // Place children
        }
    }
}
```

具体子元素的布局也与 Layout Modifier 是相同的。作为 Column 是需要将子元素进行垂直排列的，所以我们仅需指定每一个子元素的顶部相对位置即可。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        val placeables = measurables.map { measurable ->
            measurable.measure(constraints)
        }
        var yPosition = 0
        layout(constraints.maxWidth, constraints.maxHeight) {
            placeables.forEach { placeable ->
                placeable.placeRelative(x = 0, y = yPosition)
                yPosition += placeable.height
            }
        }
    }
}
```

为预览布局结果，我们创建了预览视图，创建自己定制的 Column，并添加了一些子元素。

```kotlin
@Composable
fun BodyContent(modifier: Modifier = Modifier) {
    MyOwnColumn(modifier.padding(8.dp)) {
        Text("MyOwnColumn")
        Text("places items")
        Text("vertically.")
        Text("We've done it by hand!")
    }
}
```

预览效果

<img src = "../../assets/layout/custom_layout/demo3.png" width = "50%" height = "50%">

