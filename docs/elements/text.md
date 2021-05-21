
<img src = "../../assets/elements/text/code.png" width = "50%" height = "50%">

`Text` 是 `Compose` 中最基本的布局组件，它可以显示文字


``` kotlin
@Composable
fun TextDemo() {
    Text("Hello World")
}
```

从 `res` 中加载文字

``` kotlin
@Composable
fun TextDemo() {
    Text(stringResource(id = R.string.content))
}

<resources>
    <string name="app_name">examples</string>
    <string name="content">桃之夭夭，灼灼其华。之子于归，宜其室家。</string>
</resources>
```


## 1. style 参数

当然，我们有时候也需要更换字体的大小

`Compose` 已经为我们准备了很多专门的字体大小, 从 `h1` 到 `overline`

``` kotlin
@Composable
fun TextDemo() {
    Column{
        Text(
            text = "你好呀陌生人，这是一个标题",
            style = MaterialTheme.typography.h6
        )
        Text(
            text ="你好呀陌生人，我是内容",
            style = MaterialTheme.typography.body2
        )
    }
}
```

![](../assets/elements/text/text.png)

### 文字间距

当然有的时候我们想自己自定义字体的间隔和大小，那我们可以将代码改为：

``` kotlin
@Composable
fun TextDemo() {
    Column(
        modifier = Modifier.fillMaxWidth(),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(
            text = "你好陌生人",
            style = TextStyle(
                fontWeight = FontWeight.W900, //设置字体粗细
                fontSize = 20.sp,
                letterSpacing = 7.sp
            )
        )
    }
}
```

它将会显示成

![](../assets/elements/text/text2.png)  

### 字体大小

!!! Tips
    如果只是想简单的修改字体大小而不考虑间隔之类的，可以直接使用 `fontSize = xx.sp` 来设置大小


## 2. maxLines 参数

使用 `maxLines` 参数可以帮助我们将文本限制在指定的行数之间，如果文本足够短则不会生效，如果文本超过 `maxLines` 所规定的行数，则会进行截断

``` kotlin
@Composable
fun TextDemo() {

    Column{
        Text(
            text = "你好呀陌生人，这是一个标题，不是很长，因为我想不出其他什么比较好的标题了",
            style = MaterialTheme.typography.h6,
            maxLines = 1,
        )
        Text(
            text ="你好呀陌生人，我是内容",
            style = MaterialTheme.typography.body2
        )
    }

}
```

![](../assets/elements/text/text7.png)

### overflow 处理溢出

使用 `overflow` 参数可以帮助我们处理溢出的视觉效果

``` kotlin
@Composable
fun TextDemo() {

    Column{
        Text(
            text = "你好呀陌生人，这是一个标题，不是很长，因为我想不出其他什么比较好的标题了",
            style = MaterialTheme.typography.h6,
            maxLines = 1,
            overflow = TextOverflow.Ellipsis
        )
        Text(
            text ="你好呀陌生人，我是内容",
            style = MaterialTheme.typography.body2
        )
    }

}
```

![](../assets/elements/text/text8.png)  

## 3. textAlign 参数

当我们在 `Text` 中设置了 `fillMaxWidth()` 之后，我们可以指定 `Text` 的对齐方式

``` kotlin
@Composable
fun TextDemo() {
    Column {
        Text(
            text = "每天摸鱼",
            modifier = Modifier.fillMaxWidth(),
            textAlign = TextAlign.Left
        )
        Text(
            text = "这好吗",
            modifier = Modifier.fillMaxWidth(),
            textAlign = TextAlign.Center
        )
        Text(
            text = "这非常的好",
            modifier = Modifier.fillMaxWidth(),
            textAlign = TextAlign.Right
        )
    }
}
```

![](../assets/elements/text/text5.png)  

!!! 注意
    需要注意区分的是，`TextAlign` 设置的是文本的对齐方式，而不是位置方向
    
    ![](../assets/elements/text/text9.png)  
    如果需要实现 `TextAlign.Right` 中的方向，请使用 `Modifier.align(Alignment.End)`，详情使用方法在[这里](../../layout/column/#2)


## 4. lineHeight 参数

使用 lineHeight 参数可以让我们指定 `Text` 中每行的行高间距

``` kotlin
Column {
    Text(
        text = "两面包夹芝士".repeat(15),
    )
    Spacer(Modifier.padding(vertical = 15.dp))
    Text(
        text = "两面包夹芝士".repeat(15),
        lineHeight = 30.sp
    )
}
```

![](../assets/elements/text/text10.png)


## 5. 可点击的 Text

有的时候也许您需要将文本当作按钮，那么只需要添加 `Modifier.clickable` 即可

代码如下：

``` kotlin
@Composable
fun TextDemo() {
    Text(
        text = "确认编辑",
        modifier = Modifier.clickable(
            onClick = {
                  // TODO
            },
        )
    )
}
```

### 取消点击波纹

但是我们会发现，`clickable` 有自带的波纹效果，如果我们想要取消的话，只需要添加两个参数即可：

``` kotlin
@Composable
fun TextDemo() {

    // 获取 context
    val context = LocalContext.current

    Text(
        text = "确认编辑",
        modifier = Modifier.clickable(
            onClick = {
                // 通知事件
                Toast.makeText(context, "你点击了此文本", Toast.LENGTH_LONG).show()
            },
            indication = null,
            interactionSource = MutableInteractionSource()
        )
    )

}
```

效果如下：

![](../assets/elements/text/text3.png)  

## 6. 特定的文字显示

如果我们想让一个 `Text` 语句中使用不同的样式，比如粗体提醒，特殊颜色

则我们需要使用到 `AnnotatedString`

`AnnotatedString` 是一个数据类，其中包含了：

* 一个 `Text` 的值
* 一个 `SpanStyleRange` 的 `List`，等同于位置范围在文字值内的内嵌样式
* 一个 `ParagraphStyleRange` 的 `List`，用于指定文字对齐、文字方向、行高和文字缩进样式

```kotlin
inline fun <R : Any> AnnotatedString.Builder.withStyle(
    style: SpanStyle,
    block: AnnotatedString.Builder.() -> crossinline R
): R
```

一个简单的代码演示：
``` kotlin
@Composable
fun TextDemo() {
    Column(
        modifier = Modifier.fillMaxWidth(),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(
            buildAnnotatedString {
                append("你现在观看的章节是 ")
                withStyle(style = SpanStyle(fontWeight = FontWeight.W900)) {
                    append("Text")
                }
            }
        )
    }
}
```

效果如下：

![](../assets/elements/text/text4.png)  

## 7. 文字超链接？（ClickableText）

在第 [#6](../text/#6) 部分我们已经介绍了可以通过 `AnnotatedString` 来完成在一个 `Text` 中给不同的文字应用不同的样式

在第 [#5](../text/#5-text) 部分我们已经介绍了可以通过 `Modifier.Clickable()` 来完成检测 `Text` 的点击

但是 `Modifier.Clickable()` 无法检测 `Text` 中的部分点击，那如果我们需要检测一个 `Text` 中的部分点击事件该怎么办呢？就像我们经常在 App 底下看到的用户协议等

其实很简单，`Compose` 也给我们准备了 `ClickableText`，来看看如何使用吧！

``` kotlin

val text = buildAnnotatedString {
    append("勾选即代表同意")
    withStyle(
        style = SpanStyle(
            color = Color(0xFF0E9FF2),
            fontWeight = FontWeight.Bold
        )
    ) {
        append("用户协议")
    }
}

ClickableText(
    text = text,
    onClick = { offset ->
        Log.d(TAG, "Hi，你按到了第 $offset 位的文字")
    }
)

```
![](../assets/elements/text/text11.png)![](../assets/elements/text/text12.png)

但是...怎么才能检测[用户协议]()这四个字符的点击事件呢？

也不用怕，`Compose` 还在 `buildAnnotatedString` 和 `ClickableText` 中引入了相应的方法

先来看看代码吧！

``` kotlin
val annotatedText = buildAnnotatedString {
    append("勾选即代表同意")
    pushStringAnnotation(
        tag = "tag",
        annotation = "一个用户协议啦啦啦，内容内容"
    )
    withStyle(
        style = SpanStyle(
            color = Color(0xFF0E9FF2),
            fontWeight = FontWeight.Bold
        )
    ) {
        append("用户协议")
    }
    pop()
}

ClickableText(
    text = annotatedText,
    onClick = { offset ->
        annotatedText.getStringAnnotations(
            tag = "tag", start = offset,
            end = offset
        ).firstOrNull()?.let { annotation ->
            Log.d(TAG, "你已经点到 ${annotation.item} 啦")
        }
    }
)
```
![](../assets/elements/text/text13.gif)

在上面的代码中

1. 多了一个 `pushStringAnnotation()` 方法，它会将给定的注释附加到任何附加的文本上，直到相应的 `pop` 被调用
2. `getStringAnnotations()` 方法是查询附加在这个 `AnnotatedString` 上的字符串注释。注释是附加在 `AnnotatedString` 上的元数据，例如，在我们的代码中 `"tag"` 是附加在某个范围上的字符串元数据。注释也与样式一起存储在 `Range` 中

### 小试牛刀

那么，你已经学会了如何自定义 `Text` 中的样式和点击事件，来尝试做出一个这样的效果？

![](../assets/elements/text/text14.gif)

实现的代码可以通过以下的方式来查看

1. [Mkdocs](../code/elements/text/用户协议.md)
2. [Github](https://github.com/compose-museum/compose-tutorial/blob/main/docs/code/elements/text/用户协议.kt)

## 8. 复制文字

默认情况下 `Text` 并不能进行复制等操作，我们需要设置 `SelectionContainer` 来包装 `Text`

``` kotlin
@Composable
fun TextDemo() {
    
    SelectionContainer {
        Column{
            Text(
                text = "每天摸鱼",
                modifier = Modifier.fillMaxWidth(),
                textAlign = TextAlign.Left
            )
            Text(
                text = "这好吗",
                modifier = Modifier.fillMaxWidth(),
                textAlign = TextAlign.Center
            )
            Text(
                text = "这非常的好",
                modifier = Modifier.fillMaxWidth(),
                textAlign = TextAlign.Right
            )
        }
    }
    
}
```
![](../assets/elements/text/text6.png)  

## 9. 文字强调效果

文字根据不同情况来确定文字的强调程度，以突出重点并体现出视觉上的层次感。

**Material Design** 建议采用不同的不透明度来传达这些不同的重要程度，你可以通过 `LocalContentAlpha` 实现此功能。  

您可以通过为此 `CompositionLocal` 提供一个值来为层次结构指定内容 **Alpha** 值。（`CompositionLocal` 是一个用于隐式的传递参数的组件，后续会讲到）

```kotlin
// 将内部 Text 组件的 alpha 强调程度设置为高
// 注意: MaterialTheme 已经默认将强调程度设置为 high
CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.high) {
    Text("这里是high强调效果")
}
// 将内部 Text 组件的 alpha 强调程度设置为中
CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.medium) {
    Text("这里是medium强调效果")
}
// 将内部 Text 组件的 alpha 强调程度设置为禁用
CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.disabled) {
    Icon("这里是禁用后的效果")
}
```

这是运行效果:

![](../assets/elements/text/content_alpha.png)

这张图可以很好的说明这个效果

![](../assets/elements/text/demo.png)


## 10. 更多

[Text 参数详情](../api/elements/text.md)

[Text 一些用法](https://developer.android.com/jetpack/compose/text)