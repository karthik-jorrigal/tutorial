
# 在开始之前，由于夹藏私货，不妨先来听一首作者推荐的一首歌吧！

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="100%" height=86 src="//music.163.com/outchain/player?type=2&id=1357774614&auto=0&height=66"></iframe>

在开始用 Jetpack Compose 来编写软件时，我们需要

### 1. 一台可以 **联网** 的电脑
### 2. **安装** [最新 Canary 版的 Android Studio 预览版](https://developer.android.com/studio/preview)
### 3. 选择创建 **Empty Compose Activity** [![c3JfgA.md.png](https://z3.ax1x.com/2021/04/07/c3JfgA.png)](https://z3.ax1x.com/2021/04/07/c3JfgA.png)

### 4. 保持版本更新

尝试使用最新的 `Compose` 版本和 `Kotlin` 版本

`build.gralde.kts (Project)`

```kotlin
buildscript {
    val compose_version by extra("1.0.0-beta06") // Compose 版本
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath("com.android.tools.build:gradle:7.0.0-alpha12")

        // Kotlin 版本，注意：Compose 版本有时候需要要求 Kotlin 到达一定的版本，请同步更新
        classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:1.4.32")

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle.kts files
    }
}
```

`build.gradle (Project)`
```
buildscript {
    ext {
        compose_version = '1.0.0-beta06'
    }
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath "com.android.tools.build:gradle:7.0.0-alpha12"
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.4.32"
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```


### 5. 配置 Gradle（可忽略）

您需要将应用的最低 API 级别设置为 21 或更高级别，并在应用的 build.gradle 文件中启用 Jetpack Compose，如下所示。另外还要设置 Kotlin 编译器插件的版本。

`build.gradle`

```
android {
    compileSdk 30
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "your id"
        minSdk 23
        targetSdk 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
        useIR = true
    }
    buildFeatures {
        compose true
    }
    composeOptions {
        kotlinCompilerExtensionVersion compose_version
        kotlinCompilerVersion '1.4.32'
    }
}
```

### 6. 编写第一个 **Compose** 程序
现在，如果一切都正常，我们可以看到，**MainActivity.kt** 上显示以下代码

``` kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyApplicationTheme { // 注意：这里会根据你创建的项目名而不同
                // A surface container using the 'background' color from the theme
                Surface(color = MaterialTheme.colors.background) {
                    Greeting("Android")
                }
            }
        }
    }
}

@Composable
fun Greeting(name: String) {
    Text(text = "Hello $name!")
}

@Preview(showBackground = true)
@Composable
fun DefaultPreview() {
    MyApplicationTheme {
        Greeting("Android")
    }
}
```
现在，我们将 **MainActivity.kt** 修改成以下：
``` kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyApplicationTheme {
                Greeting()
            }
        }
    }
}

@Composable
fun Greeting() {
    Text(text = "Hello World")
}
```
尝试运行，如果一切正常，你将会看到 Hello World 显示在您的 **虚拟机/手机** 上

## 7. 关于 Composable
一个基本的 **Compose** 视图是使用一个普通的 **Kotlin** 函数，该函数被注解为 **@Composable**，
所以我们使用 **Compose** 当中的东西时，需要在函数前加上 **@Composable**
