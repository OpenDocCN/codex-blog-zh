# 如何用 React Portal 改进 DOM 结构？

> 原文：<https://medium.com/codex/how-to-improve-dom-structure-with-react-portal-5cbb95ad37ef?source=collection_archive---------19----------------------->

在本文中，我将向您展示如何修复 React 门户中的溢出:隐藏问题。React 门户在父组件的 DOM 节点中呈现子组件。他们还使用批量更新来更改实际的 DOM。这两种技术都有助于您构建大型应用程序，而无需重新加载页面。请继续阅读，了解如何解决这个问题，并利用 React 门户为您带来优势！

![](img/8aaad6801cf1878d167101d7cb0dbc2f.png)

卢克·切瑟在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片