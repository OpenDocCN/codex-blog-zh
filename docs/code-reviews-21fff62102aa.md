# 代码评审:为什么&如何成为一个有效的评审者

> 原文：<https://medium.com/codex/code-reviews-21fff62102aa?source=collection_archive---------18----------------------->

![](img/e805e48bea1ae7a409ab9b71be69d585.png)

代码评审有助于你成长，并学会接受和给出反馈。

今天让我们来谈谈代码评审，为什么它很重要，以及你应该如何学习成为一个更好的评审者，并给出可操作的反馈。

## 为什么代码审查很重要？

我曾在一家初创公司呆过一段时间，然后在优步呆了好几年，现在我已经看到了等式的两边。有一次，我从来没有做过代码审查，也没有得到过关于我的代码的反馈。在另一种情况下，任何人写的每一个代码都会经历一个反馈过程。

我认为已经有很多关于代码审查为什么重要的文章，但是我对思考过程只有一点补充。

识别他人代码中的改进更容易！这可能是代码审查如此重要的唯一原因。你会有不同的视角。这是关于获得另一对大脑，然后他们也想如何写这个代码，这可以带来你最初想不到的元素！

## 如何学习做代码评审？

当你作为一个初级开发人员或者一个从来没有做过代码评审的人开始工作时，给出可操作的反馈和重要的设计反馈会感觉有点令人畏惧。这个博客总结了我在这方面收到的每一条建议，其中最重要的是我自己关于什么对我最有效(也可以对你最有效)的建议。)

## 建议 1:

在你的本地机器上补丁代码并运行它(如果你使用 arc，使用 arc 补丁<diffid>)。这个想法是通过在您的本地机器上获取代码并确保它按预期工作来以某种方式试验代码。</diffid>

*我的观点:*修补代码是一个很好的方法！它不仅能让您运行代码并查看它，还能让您更容易地浏览代码并更好地理解它。

## 建议 2:

看看其他人是如何评审代码的，从这些评审中挑选出重要的观点，看看你是否能在给出反馈时将它们结合进来。

*我的观点:*这是个好建议，我最初也尝试过这样做。如果你试图遵循这一点，对反馈提出质疑，并理解为什么会给出和收到特定的反馈。方法不应该是一有机会就复制反馈，而是真正理解为什么/如何使代码变得更好。

## **建议三:**

寻找良师益友，向他们学习！

*我的观点:*这实际上是一个很好的建议，让我们来谈谈我是如何对待它的。我开始做更多的代码审查，花时间理解我必须审查的代码，然后和我的导师一起检查审查过的代码，以了解他们对此的想法。这个想法是为了理解我可能会错过的要点。它暂时工作得很好，但也需要一个投入的导师:)(我很感激我有一个)

## 建议 4:

*我的建议:*

虽然以上几点都很有帮助，但在没有任何特定顺序的情况下，我真的成为了一个更加自信和有用的评论家！

书籍+其他资源！

1.  **重构大师**:【https://refactoring.guru/refactoring/smells】T2。这是一个开始的好地方。帮助您理解多种代码味道和重构技术，以编写更好的代码。
2.  **头先设计模式**:[https://www . Amazon . com/Head-First-Design-Patterns-Brain-Friendly/DP/0596007124](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124)。这本书是必读的！它打开了一个好设计的世界，以及如何+为什么去思考它。有这么多的概念，光这本书就能教会你可伸缩设计以及它的重要性。这个惊人的播放列表[https://www.youtube.com/playlist?list = plrhzvicii 6 gnjpardno 4 uetuavr 9 embpc](https://www.youtube.com/playlist?list=PLrhzvIcii6GNjpARdnO4ueTUAVR9eMBpc)也是一种很好的跟随书籍的方式。
3.  **有效的 Java** :这本书对于 Java 开发者来说不需要介绍。编写好代码最有效的资源之一。我通读这本书的方式是浏览一个特定的章节，然后如果可能的话也浏览这个播客中的相同主题:[https://fragmentedpodcast.com/episodes/effective-java/](https://fragmentedpodcast.com/episodes/effective-java/)
4.  **用 RxJava 进行反应式编程**:[https://www . Amazon . com/Reactive-Programming-RxJava-Asynchronous-Applications/DP/1491931655/](https://www.amazon.com/Reactive-Programming-RxJava-Asynchronous-Applications/dp/1491931655/)这本书是一位对 RxJava 非常了解的同行推荐的，现在也是我向任何以任何形式使用 rx Java 的人推荐的第一本书。
5.  **Clean Code**:[https://www . Amazon . com/Clean-Code-Handbook-Software-craftness/DP/0132350882/](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/)我建议要么阅读这本书，了解每一个主题，要么寻找关于良好设计原则的视频。文卡特·苏布拉马年([https://www.youtube.com/watch?v=llGgO74uXMI](https://www.youtube.com/watch?v=llGgO74uXMI))写的那些很好理解。

在下一篇文章中，我将谈论我如何在日常基础上进行代码审查，以及我给予+收到的良好反馈。

我希望你能发现我成为一个更好的代码审查者的旅程。

请在评论中告诉我，你是否有其他方法来做更好的代码评审，以及哪些方法最适合你！