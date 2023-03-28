# Android 中的活动与片段

> 原文：<https://medium.com/codex/activity-vs-fragment-in-android-d9595a79119?source=collection_archive---------0----------------------->

## 这两个基本 Android UI 组件的比较以及如何使用 Kotlin 正确使用它们

![](img/14acd000c0126a8e93d36a2dcc5d0865.png)

照片由[格伦·凯莉](https://unsplash.com/@glencarrie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

很多时候，在论坛和 StackOverflow 上阅读，我遇到过有人询问如何正确建立片段视图，或者难以理解与活动的区别。

这篇文章的目的是解释什么区分活动和片段，如何正确地设置它们各自的布局，以及何时使用其中的一个或另一个。

先说他们的定义。

# 什么是活动？

仅仅通过阅读这两种结构的定义，就可以看出它们是非常不同的。

根据 Android 开发者的[官方文档](https://developer.android.com/reference/android/app/Activity)，

> 一个**活动**是用户可以做的一个单一的、集中的事情。几乎所有的活动都与用户交互，所以 Activity 类负责为您创建一个窗口，您可以在其中放置带有`setContentView(View)`的 UI

# 什么是碎片？

另一方面，这是来自[官方文档](https://developer.android.com/guide/fragments)的片段定义:

> 一个**片段**代表了你的应用程序 UI 的可重用部分。片段定义和管理自己的布局，有自己的生命周期，可以处理自己的输入事件。片段不能独立存在——它们必须由一个活动或另一个片段托管。

从定义中可以看出，关键的区别在于**片段依赖于活动才能存在**，因此它只代表用户界面的一部分。

相反，可以将活动看作是一个容器，所有其他 UI 组件(包括片段)都将放在这个容器下。没有活动，就没有用户界面。

# 关于视图绑定的快速说明

随着 [Android Jetpack](https://developer.android.com/jetpack) 的推出，引用视图的旧方式已经更新。

现在，视图绑定自动生成一个类，允许直接引用布局中的所有视图 ID，而不是使用`findViewById`方法。

要在 Android 应用程序中设置视图绑定，您需要在您的`build.gradle`应用程序文件中指定`viewBinding`标志:

```
android {
    ...
    buildFeatures {
        viewBinding true
    }
}
```

有关视图绑定的更多信息，请查看官方文档。

# 如何设置活动

为了正确地创建和设置一个活动，您需要创建一个新的 Kotlin 类，它扩展了`AppCompactActivity`类。

几个**文件命名约定**:

*   对于布局文件，使用*蛇形语法*指定布局类型，后跟布局名称。比如`activity_main.xml`。
*   对于布局类，指定布局的名称，后跟布局的类型，使用*骆驼大小写语法*。比如说`MainActivity.kt`。

首先，您需要为您的活动创建一个布局文件。
Android 中的布局文件是 XML 文件，保存在您项目的`layout` resource 文件夹中。

因此，创建一个简单的`activity_main.xml`文件，并用一个容器布局填充(我将使用一个`LinearLayout`，只是为了简单起见)，以及一个`TextView`。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout       
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```

现在让我们回到我们的活动类，第一步是**在`onCreate`方法中初始化布局**。
然后，使用`setContentView`方法初始化活动将引用的布局。

下面是一个简单活动的示例:

```
private lateinit var binding: ActivityMainBindingclass MainActivity: AppCompatActivity() 
{
    override fun onCreate(savedInstanceState: Bundle?) { 
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater) 
        val view = binding.root       
        setContentView(view) binding.textView = "This is an Activity example"
    }
}
```

如你所见，你可以通过使用绑定的布局引用来引用你的`TextView`。

一旦你完成了你的活动设置**，你需要在你的*Android manifest . XML***文件中声明它，在`<application>`标签中，让 Android 系统知道它的存在:

```
<activity android:name=".MainActivity" />
```

如果您需要指定一个活动作为应用程序的起点，您需要做的就是在您的`<activity>`标签中添加两个特定的`<intent-filter>`:

```
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

完成设置后，您的活动将在应用程序启动时显示。

# 如何设置片段

为了在您的应用程序中利用片段，**您需要将所需的依赖关系**添加到您的项目`build.gradle`文件中:

```
buildscript {
    ...

    repositories {
        google()
        ...
    }
}

allprojects {
    repositories {
        google()
        ...
    }
}
```

一旦您添加了所需的存储库，您只需要在您的应用程序`build.gradle`文件中指定 AndroidX 片段库:

```
dependencies {
    def fragment_version = "<specify-the-latest-version>"

    implementation "androidx.fragment:fragment-ktx:$fragment_version"
}
```

有关如何正确设置环境的更多信息，请参考[官方文档](https://developer.android.com/guide/fragments/create)。

要创建一个片段，您需要创建一个扩展`Fragment`类的类。

同样，我将为这个片段创建一个简单的布局文件，名为`fragment_main.xml`，类似于上面的文件:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout       
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```

片段的布局在`onCreateView`方法中初始化:

```
private var _binding: FragmentMainBinding? = null
private val binding set() = _binding!!class MainFragment: Fragment() 
{
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
       savedInstanceState: Bundle?
    ): View? {
        _binding = FragmentMainBinding.inflate(inflater, container, false)
        val view = binding.root binding.textView = "This is a Fragment example" return view
    } override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

与活动不同，片段不需要在 *AndroidManifest.xml.* 中声明

您可能还注意到了一个事实，即视图绑定引用是可选的。
一旦片段从应用程序中移除，这个变量就在`onDestroyView`方法中被清除。
这是为了避免潜在的内存泄漏问题，因为内存中保存了未使用的对象引用。

然后你需要将你的片段添加到你的活动布局文件中，使用`FragmentContainerView`布局来使用和显示它:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout       
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"> <androidx.fragment.app.FragmentContainerView
        android:layout_width="match_parent"
        android:layout_height="0dp" 
        android:layout_weight="1"
        android:name="com.example.MainFragment" />    <androidx.fragment.app.FragmentContainerView
        android:layout_width="match_parent"
        android:layout_height="0dp" 
        android:layout_weight="1"
        android:name="com.example.Main2Fragment" /></LinearLayout>
```

`FragmentContainerView`将显示在`android:name`属性中指定的片段。

如您所见，**由于组件的模块化，您可以在单个活动中指定多个片段，**。

# 结论

虽然活动和片段一开始看起来很相似，可以互换，但实际上，它们是非常不同的。

我希望这个指南能帮助你更好地理解并在你的 Android 应用中更好地使用这些 UI 组件。

编码快乐！