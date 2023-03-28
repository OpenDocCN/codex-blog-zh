# Angular Guards:禁止使用浏览器的后退按钮导航到特定的 URL

> 原文：<https://medium.com/codex/angular-guards-disabling-browsers-back-button-for-specific-url-fdf05d9fe155?source=collection_archive---------1----------------------->

## 如何在 Angular 中禁用浏览器后退按钮导航，并保持窗口历史完整。

![](img/a0245863f9b2b041b77b77201f5e6e4a.png)

照片由[戈罗登科夫](https://www.istockphoto.com/portfolio/gorodenkoff)拍摄

Angular 中的`[CanDeactivate](https://angular.io/api/router/CanDeactivate)` Guard 可以用来避免导航到与应用程序相同域的另一个页面。然而，多年来，[直到](https://github.com/angular/angular/issues/13586#issuecomment-881627456) …