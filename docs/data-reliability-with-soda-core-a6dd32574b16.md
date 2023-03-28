# Soda Core 的数据可靠性

> 原文：<https://medium.com/codex/data-reliability-with-soda-core-a6dd32574b16?source=collection_archive---------5----------------------->

数据对所有组织来说都至关重要，但许多组织都在努力获取高质量的数据。最常见的原因是缺乏关于数据可靠性的文档或验证假设的部落知识。通常，在组织级别没有建立具体的解决方案，这导致了不可靠的数据。不幸的是，这可能会对许多组织产生重大的财务影响，因为他们无法从数据中获得正确的见解。

有很多解决方案可以缓解数据可靠性和数据可观察性问题。 [Soda Core](https://www.soda.io/core) 是开源市场上鲜为人知但功能强大的工具之一。在本文中，我将简要解释什么是 Soda Core，并演示如何利用这个开源工具来提高管道内外的数据质量测试。

![](img/e4bd526deea21b85c418954ba4a25e69.png)

## 什么是苏打芯？

Soda Core 是一个命令行界面(CLI)工具，使您能够扫描数据源中的数据，以发现无效、缺失或意外的数据。它用于管道内外的数据质量测试、数据可观察性和数据监控。它对 SQL 表和 Spark 数据帧都有效，由几个组件组成，如指标收集、数据分类、检查评估和警报。Soda Core 使用 Soda Checks 语言(SodaCL)，这是一种人类可读的特定于领域的语言，用于数据可靠性。

Soda Core 支持以下数据源，你可以在他们的[官方文档页面](https://docs.soda.io/soda-core/configure.html)找到他们的配置信息。

*   亚马逊雅典娜
*   亚马逊红移
*   阿帕奇火花数据帧
*   GCP 大质问
*   IBM DB2
*   MS SQL Server
*   关系型数据库
*   一种数据库系统
*   雪花

## 从苏打核心开始

要开始使用，您的系统上必须有 Python 3.8 或更高版本。还建议使用 Python 虚拟环境安装 Soda 核心。因此，让我们首先创建一个虚拟环境，并使用以下命令激活它:

```
python -m venv .venv
source .venv/bin/activate
```

现在您的虚拟环境已经准备好了，让我们将它的 Python 包管理器 pip 升级到最新版本:

```
pip install --upgrade pip
```

最后，我们需要根据您需要处理的数据源类型来安装 Soda 核心包。对于这个演示，我将使用苏打核心雪花。

```
pip install soda-core-snowflake
```

现在我们需要创建以下两个 YAML 文件:

*   **configuration.yml**

此文件包含数据源的连接详细信息。Soda Core 使用这个文件来访问您的数据源。建议利用系统变量安全地检索值，尤其是像密码这样的敏感数据。

```
data_source your_datasource_name:
  type: snowflake
  connection:
    username: "YOUR_USER_ID"
    password: ${SNOWFLAKE_PASSWORD}
    account: you_account.us-east-1
    database: YOUR_DATABASE
    warehouse: YOUR_WAREHOUSE
    connection_timeout: 300
    role: YOUR_ROLE
    client_session_keep_alive:
    session_parameters:
      QUERY_TAG: soda-queries
      QUOTED_IDENTIFIERS_IGNORE_CASE: false
  schema: YOUR_SCHEMA_NAME
```

*   **checks.yml**

该文件包含 Soda Core 在扫描数据源中的数据集时执行的测试。这些检查使用 [Soda 检查语言](https://docs.soda.io/soda-cl/soda-cl-overview.html) (SodaCL)编写。有许多内置的指标可供我们使用，您甚至可以使用 SQL 查询定义自己的指标。在本演示中，我们将执行两项检查。

1.  遍历在 _SCHEMA_NAME 模式中名称以 _DIMENSIONS 结尾的每个表，并检查它们是否为空。我们排除了任何以 TEMP_ 开头的表名。
2.  第二项检查是确保 DATE_DIMENSION 表中的 date_id 列不为 NULL。

```
for each dataset T:
  datasets:
    - include %_DIMENSION
    - exclude TEMP_%
  checks:
    - row_count > 0checks for DATE_DIMENSION:
  - missing_count(date_id) = 0
```

最后，通过发出以下命令，您可以对数据源运行扫描了。你可以在这里找到扫描命令选项及其描述[。](https://docs.soda.io/soda-core/scan-core.html#add-scan-options)

```
soda scan -d your_datasource_name -c configuration.yml checks.yml
```

一旦上述命令成功执行，您应该会看到如下所示的输出。

```
Soda Core 3.0.4
Instantiating for each for ['CUSTOMER_DIMENSION','DATE_DIMENSION','PRODUCT_DIMENSION']
Scan summary:
4/4 checks PASSED:
   DATE_DIMENSION in YOUR_SCHEMA_NAME
     missing_count(date_id) = 0 [PASSED]
   CUSTOMER_DIMENSION in YOUR_SCHEMA_NAME
     row_count > 0 [PASSED]
   DATE_DIMENSION in YOUR_SCHEMA_NAME
     row_count > 0 [PASSED]
   PRODUCT_DIMENSION in YOUR_SCHEMA_NAME
     row_count > 0 [PASSED]
All is good. No failures. No warnings. No errors.
```

## 从这里去哪里？

既然您已经使用 Soda Core 进行了有效的数据质量检查，那么您可以将它与一个数据编排工具(如 Airflow)相集成，以自动化和安排您对坏数据的搜索。您甚至可以通过将它连接到[苏打云](https://docs.soda.io/soda-cloud/overview.html)来扩展功能，这允许您使用度量商店、与其他人协作以及使用问题解决工作流。不断发展的 SodaCL 提供了许多内置的人类可读检查，并且能够定义我们自己的度量标准来使用 SQL 查询定制检查。

## 结论

我觉得这个项目有很大的潜力，特别是因为它允许您以最小的努力对多个数据源进行数据质量检查。它提供了与编排工具的简单集成，并允许您轻松检查各种已知和未知的根本原因。就依赖性而言，它是轻量级的，并且学习曲线是简单明了的。我对苏打核心的未来感到兴奋。