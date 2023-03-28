# 在 Spring Boot JPA 或查询 DSL 中注册自定义或非标准 SQL 函数

> 原文：<https://medium.com/codex/register-custom-or-non-standard-sql-functions-in-spring-boot-jpa-or-query-dsl-34980fb42cf1?source=collection_archive---------3----------------------->

![](img/e6d3a44eb58a1ddf2717f5a9d85dcb5d.png)

克里斯·里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JPA 支持大多数可用于编写查询的标准 SQL 函数。但是我们如何使用 JPA/Hibernate 中的非标准或自定义 DB 函数呢？原生查询是这样做的一种方式，但是如果我们使用 Criteria API 或 Query DSL 编写动态查询，如何使用它呢？

# 向 JPA 和 MetadataBuilderContributor 注册 SQL 函数

从 Hibernate 5.2.18 开始，即使是通过 JPA 进行引导，也可以使用`MetadataBuilderContributor`实用程序来自定义`MetadataBuilder`。

`MetadataBuilderContributor`接口可以这样实现:

在上面的代码片段中，我通过提供一个 MySQL JSON 函数来覆盖`contribute`方法。

`JSON_CONTAINS()`函数检查一个 JSON 文档是否包含另一个 JSON 文档。

**JSON_CONTAINS(target_json，candidate_json)**

**参数**

`*target_json*`必选。一个 JSON 文档。

`*candidate_json*`必选。包含的 JSON 文档。

如你所见，我已经创建了一个自定义键`“json_contains_key”`。这实际上将在 Criteria API 或 Query DSL 中使用，并将被 Hibernate 解析为`JSON_CONTAINS()`。

并通过`[hibernate.metadata_builder_contributor](https://vladmihalcea.com/hibernate-sql-function-jpql-criteria-api-query/)`配置属性提供自定义的`MetadataBuilderContributor`给 Hibernate。

```
spring.jpa.properties.hibernate.metadata_builder_contributor=com.abhicodes.customdialect.util.SQLFunctionContributor
```

# 在查询中使用自定义函数 DSL 查询

因为 **JSON_CONTAINS** ()返回一个布尔值，所以我使用`Expressions.booleanTemplate`来形成谓词，传入自定义键和两个必需的参数:目标 JSON 和候选 JSON。此后，我将谓词传递给存储库以获取值。

你可以在 [Github](https://github.com/Abhi-Codes/custom-dialect) 上找到完整的工作代码。

如果你喜欢这篇文章，请花点时间为我鼓掌👏(可以多次鼓掌)，关注我，甚至请我喝咖啡【https://www.buymeacoffee.com/abhiandy】T2