# 停止测试你的(反)序列化器

> 原文：<https://medium.com/codex/stop-testing-your-serializers-9b568ecd652f?source=collection_archive---------5----------------------->

## 自动化测试的[目标之一是“作为安全网的测试”,但这并不意味着你应该对所有的事情进行单元测试。序列化(反序列化)是不应该直接测试的一个例子。](/swlh/automated-testing-goals-d3ace8fd223a)

[根据六边形架构](/codex/clean-architecture-for-dummies-df6561d42c94)，一个**适配器**属于你 app 的外层；它是组件和外部世界之间的粘合剂，无论是数据库、REST API 的网关、你自己的 REST API 等等。适配器应该…