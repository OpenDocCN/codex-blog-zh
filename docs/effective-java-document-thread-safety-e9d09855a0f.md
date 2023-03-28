# 有效的 Java:文档线程安全

> 原文：<https://medium.com/codex/effective-java-document-thread-safety-e9d09855a0f?source=collection_archive---------6----------------------->

![](img/64f49dffa78a482a23269c574294fcb2.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

您编写的类的用户需要知道它们的行为。用户需要知道的类的属性之一是该类是否是线程安全的。在这个文档之外，这个类的用户需要猜测这个类的线程安全性。这可能导致过度同步或不充分同步，从而导致不变问题。