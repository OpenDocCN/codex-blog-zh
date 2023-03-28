# 你的 API 应该使用枚举吗？

> 原文：<https://medium.com/codex/should-your-api-use-enums-340a6b51d6c3?source=collection_archive---------1----------------------->

*枚举值是一种巧妙的验证技术，对于 API 来说可能是有风险的*

[枚举值](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/enum)是一种利用类型安全的好方法。它们使得原本难以记忆的数字或字符串变得易于使用。不幸的是，它们也会给 API 开发者带来风险。

当我的团队设计 [AvaTax REST API](https://developer.avalara.com/api-reference/avatax/rest/v2/methods/) 时，我是 enums 的忠实粉丝；但是我想和你们分享我们所面临的问题，以及为什么我们的团队在设计[锁步平台 API](https://developer.lockstep.io/docs) 的时候减少了枚举的使用。我们来分析一下。