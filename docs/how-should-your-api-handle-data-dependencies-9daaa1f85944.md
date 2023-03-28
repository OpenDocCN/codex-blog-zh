# 你的 API 应该如何处理数据依赖？

> 原文：<https://medium.com/codex/how-should-your-api-handle-data-dependencies-9daaa1f85944?source=collection_archive---------9----------------------->

*当一个数据结构中的两个字段被链接时，可用性可能会很棘手*

所以你的团队设计了一个 API，它接受一个地址作为输入。首先将地址国际化成国家中性的字段名，如`country`、`region`、`postalCode`、`city`和`address`。

突击测验:这些字段中哪些是**必填字段**？

对于国家代码，您可以将该字段标记为必填，并下载一个 ISO 3166 库[来验证 API 调用。但是还有更多:在美国，`region`字段是 ***也是*** 必需的，因为它包含…](https://www.npmjs.com/package/iso-3166-1)