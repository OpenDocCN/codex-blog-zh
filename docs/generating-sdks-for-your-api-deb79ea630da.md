# 为您的 API 生成 SDK

> 原文：<https://medium.com/codex/generating-sdks-for-your-api-deb79ea630da?source=collection_archive---------2----------------------->

*为 REST API 生成库系列文章的第一篇*

所以，你写了一个 [REST API](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design) 。既然 JavaScript 内置了对执行 web 请求的支持，既然它可以将 JSON 视为数据对象，[我们就完成了，对吧](https://www.uship.com/blog/shipping-code/does-your-rest-api-need-an-sdk)？大家可以直接开始[写代码](https://www.smashingmagazine.com/2018/01/understanding-using-rest-api/)？

根据它的设计，一个积极的开发人员或黑客可能愿意努力找出您的 REST API，但是其他人在面临困惑和障碍时可能会放弃。程序员依赖于:

*   文档，包括 [Swagger 或开放 API](https://swagger.io/specification/) …