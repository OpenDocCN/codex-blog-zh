# Android 中使用 Kotlin 的警报对话框

> 原文：<https://medium.com/codex/optimized-androids-dialogs-management-1b899ecaedb6?source=collection_archive---------7----------------------->

## 改进您在 Android 项目中处理全球警报的方式

![](img/d5435d347a2f67c0053145632e1c47fa.png)

由[绝对视觉](https://unsplash.com/@freegraphictoday?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 介绍

一旦软件项目的规模变大，保持代码的整洁就变得越来越困难，你很快就会看不到整个架构。
Android 开发也不例外。

在做了几个项目之后，我注意到警告对话框代码散布在我所有的应用程序中，我开始复制代码来重新定义相同类型的自定义对话框。

正如许多程序员已经知道的，我们工作的第一个良好实践是通过应用**不要重复自己(DRY)** 原则来避免代码重复。

另外，另一个问题是，通过快速点击触发对话框打开的视图，同一个对话框的多个实例出现在屏幕上。

我需要一种方式来集中:

1.  对话框定义
2.  对话框出现和消失行为

为了解决这两个问题，我决定定义一个对象类，并用它来定义和控制我所有的对话框。

以下是我的结构。

# 对象定义和方法

首先定义一个新的 Kotlin 对象，姑且称之为 *DialogController.kt* 。这个文件将存储你所有的对话框实例和定义。

这个想法是为每个对话框保留一个不同的变量和一个专用的函数来实例化和显示这个对话框。

在我的例子中，我将指导您创建一个警告对话框和一个确认对话框。当然，你可以把这个逻辑应用到任何类型的对话中。

首先定义两个变量，一个用于警报，一个用于确认。

```
object DialogController 
{
    private lateinit var dialogAlert: AlertDialog
    private lateinit var dialogConfirmation: AlertDialog
}
```

正如你所看到的，我将两个变量都定义为`lateinit`，这样它们只有在你调用相关函数后才会被初始化。
这很有用，因为`MaterialAlertDialogBuiler`构造函数需要一个`Context`类型的参数。
该参数因活动而异，因此之前初始化这两个变量是没有用的。

下一步是定义处理对话框外观的函数。
这里的想法是只有在没有其他对话框被初始化和显示的情况下才显示一个对话框。

```
private fun cantShowDialog(): Boolean = 
    this::dialogAlert.isInitialized && dialogAlert.isShowing ||
    this::dialogConfirmation.isInitialized && dialogConfirmation.isShowing
```

只有当屏幕上当前没有显示其他对话框时，该函数才会返回`true`。

最后，让我们从警报对话框开始创建初始化函数。

```
fun showDialogAlert(context: Context, title: String, msg: String) 
{
    if (cantShowDialog()) { return } dialogAlert = MaterialAlertDialogBuilder(context)
        .setTitle(title)
        .setMessage(msg)
        .setPositiveButton("Close", null)
        .setCancelable(false)
        .create()
        .apply {
            setCanceledOnTouchOutside(false)
            show()
        }
}
```

正如您所看到的，该函数做的第一件事是检查是否有可能显示一个新的对话框，如果有，它继续定义相关的变量并显示所需的标题和消息。

用同样的逻辑，定义处理确认对话框的函数。

```
fun showDialogConfirmation(
    context: Context, 
    title: String, 
    msg: String, 
    callback: () -> Unit
) {
    if (cantShowDialog()) { return } dialogConfirmation = MaterialAlertDialogBuilder(context)
        .setTitle(title)
        .setMessage(msg)
        .setNegativeButton("Cancel", null)
        .setPositiveButton("Confirm", callback)
        .setCancelable(false)
        .create()
        .apply {
            setCanceledOnTouchOutside(false)
            show()
        }
}
```

这里的主要区别是使用回调函数作为参数。
这将在正面按钮点击事件时触发。

现在，您可以通过整个应用程序轻松访问对话框，只需调用:

```
DialogController.showDialogAlert(this, "Alert", "This is an alert")DialogController.showDialogConfirmation(this, "Confirmation", "Are you sure you want to proceed?") {
    // Code executed once confirmed...
}
```

# 最终代码

这是刚刚定义的类的完整代码。

DialogController.kt

# 结论

这个简单的技巧让我简化了我的整体代码，并完全控制我的警报。

这个例子只展示了几个简单的对话框，你可以定制你的 *DialogController* 类来定义你需要的更复杂的实例。

编码快乐！