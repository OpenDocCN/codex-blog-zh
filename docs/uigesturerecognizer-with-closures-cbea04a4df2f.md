# 带闭包的 UIGestureRecognizer

> 原文：<https://medium.com/codex/uigesturerecognizer-with-closures-cbea04a4df2f?source=collection_archive---------8----------------------->

`UIGestureRecognizer`最大的缺点之一是无法内嵌简单的闭包。要使用它们，必须在指向要调用的函数的`Selector`旁边指定一个目标对象。

这种范式的一个主要缺点是`UIGestureRecognizer`只将调用对象传递给被调用函数。这意味着如果多个对象调用同一个函数，它们需要在某些方面是可微的。

出于好奇，我在网上寻找解决办法。许多人建议使用 Objective C 运行时来添加一个关联的对象来管理闭包，但是这是一个棘手的问题。我偶然发现的一个更简洁的解决方案是简单地将手势识别器子类化，并在其中添加动作:

这个简单而优雅的解决方案干净利落地解决了这个问题。同样的解决方案也可以用于其他可子类化的目标+选择器风格的 API，比如使用`UIButton`。