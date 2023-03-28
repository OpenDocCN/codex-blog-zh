# 2022 年对数据治理的需求

> 原文：<https://medium.com/codex/need-of-data-governance-in-2022-9a7f4637870f?source=collection_archive---------6----------------------->

## 云数据架构

## 从可选到强制

**简介**

像任何其他治理一样，数据治理也需要规范实践和流程，特别是围绕数据接收、存储、访问和使用。

数据治理是一种组织、控制和有效管理数据的系统或方法，以便可以轻松地访问数据并用于洞察生成、决策、预测和建议。

实现良好的数据治理策略对于让数据对用户有用和可消费是至关重要的。

*与安全性一样，数据治理也应被视为日常活动。在编译总体数据策略时，数据架构师还应该关注治理计划。*

![](img/a806eced9c5fecee3547bd212a93dce6.png)

照片由[马克·达弗尔](https://unsplash.com/@2mduffel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在实施数据治理时，需要考虑以下五个关键功能

1.  **统一元数据管理**

元数据和实际数据一样重要。

实现一个系统，该系统支持将元数据存储在一个可以有效控制且用户可以轻松访问的位置。

没有适当的元数据，用户将无法发现数据，从而将数据湖转变为没有实际价值的数据沼泽。

**2。标准&清除访问控制**

实现可以支持细粒度访问的访问控制策略——不仅在表级别，而且在列级别。这将有助于管理对 PII/PHI 属性的访问。根据使用案例限制不合格用户对敏感属性的访问。

例如,“salary”属性可以限制为属于“Sales”部门的雇员，而来自 HR 部门的雇员应该能够访问它。

**3。启用数据共享**

**避免创建数据的重复副本。** *(这主要针对使用数据湖或湖屋方法构建的系统。如果您正在实施数据网格，那么这可能不适合您)*

建立只有一个真实来源的系统。

这只有在您拥有支持有效数据共享的工具和流程的情况下才有可能。

**4。支持数据沿袭**

数据沿袭可能是数据生态系统中最被低估的功能之一。端到端的沿袭可以表明数据是如何从源流向目标的&在这个过程中它是如何被转换的。

这种沿袭对于审计数据和执行任何影响分析都是至关重要的。

例如，考虑修改 category_code 这样的属性的长度而引发的变更请求(CR)。数据沿袭有助于找到该属性填充或派生数据的确切表。

**5。提高数据质量**

虽然数据质量可以被视为数据生态系统中的一个独立过程，但拥有适当的数据治理至关重要。

大多数数据生态系统在存储数据方面做得非常好&让用户可以快速获得数据。尽管如此，这些系统中只有少数能够保证数据的准确性、完整性和完整性。

拥有适当的控制可以提高数据质量、准确性和真实性，并有助于消除与不正确或不一致的见解生成相关的任何担忧。

# 我们如何实现数据治理？

下面是实现这些的一些选项。您也可以探索其他选择&选择适合您的用例。

1.  **将你的数据分类**

使用 [AWS 胶水数据目录](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html)存储元数据。如果您使用非本地服务，如雪花，您可以导出雪花元数据&在 Glue catalog 中导入它。

如果您正在寻找企业级平台，您可以探索像 [Collibra](https://www.collibra.com/us/en/platform/data-catalog) 或 [Alation](https://www.alation.com/product/data-catalog/) 这样的选项。我没有使用过这些，但是已经实现了使用 [Informatica 进行元数据管理](https://www.informatica.com/in/products/informatica-platform/metadata-management.html)的系统，它在管理元数据和维护业务术语表方面做得非常好。

**2。实施细粒度的访问控制**

您可以使用 [AWS Lake Formation](https://aws.amazon.com/lake-formation/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc) 服务来提供 IAM 用户&角色级别的访问控制。你可以在数据库中提供访问控制，表&属性级别&限制对 PII/PHI 列的访问。

Lake Formation 与其他 AWS 服务有很好的集成。一些[新特性](https://aws.amazon.com/blogs/aws/aws-lake-formation-general-availability-of-cell-level-security-and-governed-tables-with-automatic-compaction/)也支持在单元级别控制访问。

还有其他类似于 [Immuta](https://www.immuta.com/) 的平台，您可以进一步探索实现整体访问控制。

**3。启用跨单元数据共享**

如果您正在构建一个数据湖或湖屋，您将与其他部门、业务单位或团体共享数据。使用 AWS Lake Formation 通过 Athena 从其他 AWS 帐户提供访问和查询数据。

Databricks 可能有一个最好的共享功能，叫做 [delta sharing](https://databricks.com/blog/2021/05/26/introducing-delta-sharing-an-open-protocol-for-secure-data-sharing.html) ，与消费者共享数据，消费者可以使用 Spark/Python 或一些 BI 工具访问这些共享数据。

Redshift 还有一个跨集群共享数据的特性，也支持跨账户数据共享。

如果你使用雪花作为仓库，你可以使用[读者账户](https://docs.snowflake.com/en/user-guide/data-sharing-reader-create.html#overview)与其他雪花用户甚至外部系统共享数据。

**4。提供数据沿袭**

我还没有看到任何提供 E2E 数据血统的云原生服务。如果您计划实现数据沿袭，也可以在 Databricks 中探索 [Unity 目录。](https://databricks.com/product/unity-catalog)

我之前在 Hadoop 时代使用过[cloud era Navigator](https://www.cloudera.com/products/product-components/cloudera-navigator.html#:~:text=Cloudera%20Navigator%20is%20the%20only,metadata%20management%2C%20and%20policy%20enforcement.)&发现它有助于进行影响分析。

您也可以使用 AWS EMR 检查 [Apache Atlas。然而，它只能在 Hadoop 生态系统中提供血统。](https://aws.amazon.com/blogs/big-data/metadata-classification-lineage-and-discovery-using-apache-atlas-on-amazon-emr/)

气流有一些使用 Atlas 的[实验特征，可以在某种程度上赋予血统。我没有尝试过，但是如果这是你唯一的选择，你可以尝试一下。](https://airflow.apache.org/docs/apache-airflow/1.10.4/lineage.html)

**5。提高数据质量**

使用云原生服务，如 [AWS Glue DataBrew](https://aws.amazon.com/glue/features/databrew/) 来分析数据&在 Glue ETL 中进行适当的检查&验证，以加载完整的&准确数据。

您还可以探索其他工具，如 Informatica、Talend，来实现可以跨各种工作负载重用的数据质量流程。

# 结束语

[2022 年，许多企业可能开始实施数据湖或湖屋解决方案](/@gauravthalpati/cloud-data-architecture-trends-in-2022-and-beyond-f0404f2b1eaa)。

他们将利用一些开放数据文件格式、数据共享功能、独立的第三方查询引擎或外部访问控制产品。

要成功实施和采用此类系统，数据治理将是基础支柱。数据治理现在变得比以往任何时候都更加重要。

> 它不再是“值得拥有”或第 2 阶段的功能。

**与安全性一样，治理也应该是您的第 0 天活动&在决定整体数据策略时，应该仔细评估。**

健全的数据治理战略可以帮助企业实施数据生态系统，这些生态系统不仅仅是*更容易构建*，而且 ***易于访问&使用*** 用于其 AI & BI 工作负载。