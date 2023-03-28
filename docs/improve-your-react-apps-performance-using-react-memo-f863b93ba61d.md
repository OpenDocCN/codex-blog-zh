# 使用 React.memo 提高 React 应用程序的性能

> 原文：<https://medium.com/codex/improve-your-react-apps-performance-using-react-memo-f863b93ba61d?source=collection_archive---------3----------------------->

![](img/f09e136f84ce0191a1ffcf8b98ec10f9.png)

约纳斯·冯·沃纳在 Unsplash[上拍摄的照片](https://unsplash.com/s/photos/germany-long-exposure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

没有人喜欢缓慢、反应迟钝的应用程序。很多时候是因为不必要的用道具重新渲染组件造成的，这些道具连变化都没有。您可能甚至没有意识到这一点，但是 React 仍然执行组件的渲染，即使它具有相同的属性。但是不要担心，`React.memo`来救援了！

# React.memo()是什么？