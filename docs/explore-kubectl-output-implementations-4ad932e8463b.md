# 探索 Kubectl 输出实现

> 原文：<https://medium.com/codex/explore-kubectl-output-implementations-4ad932e8463b?source=collection_archive---------11----------------------->

## kubectl 围棋中的“特殊”策略模式

![](img/22f7140962a8ef5b9393d1b1a83b479d.png)

来源:Unsplash， [@rachel_4](https://unsplash.com/photos/j3SBec5HBQU)

在我的[上一篇文章](https://laiyuanyuan-sg.medium.com/kubectl-output-101-851f8e61fd51)中，我们通过不同格式的例子介绍了`kubectl get -o/–output`的用法，这些例子有点复杂，甚至包括简单的编码。为了更好地掌握和记忆输出备忘单，没有什么比理解其背后的逻辑更有帮助的了。所以让我们揭开它的设计和实现…