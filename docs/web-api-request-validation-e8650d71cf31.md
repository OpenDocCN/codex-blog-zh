# Asp.Net Web API 请求验证

> 原文：<https://medium.com/codex/web-api-request-validation-e8650d71cf31?source=collection_archive---------1----------------------->

## 使用模型绑定、自定义属性和流畅验证来分离验证逻辑

![](img/062e65e14d2a879d28bdd0e49d113b9d.png)

图片由[萨姆·威廉姆斯](https://pixabay.com/users/10634669-10634669/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4125016)来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4125016)

验证数据是软件开发中最频繁和最日常的活动之一，尽管前端验证存在于几乎所有的应用程序中，但后端验证是强制性的，因为大多数时候我们可以在前端禁用它。