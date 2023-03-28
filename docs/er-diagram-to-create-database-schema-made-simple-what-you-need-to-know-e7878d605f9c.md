# ER 图创建数据库模式变得简单:你需要知道什么

> 原文：<https://medium.com/codex/er-diagram-to-create-database-schema-made-simple-what-you-need-to-know-e7878d605f9c?source=collection_archive---------5----------------------->

![](img/a951c58e2574dfd79f92812b92e3f212.png)

[https://www.educba.com/dbms-er-diagram/](https://www.educba.com/dbms-er-diagram/)

在这篇博客中，我将带你浏览 ER(**)图:什么是 ER 图？如何创建 ER 图？我们为什么需要它？开发数据库时要做的最重要的事情之一是构建一个数据库模式，它是所有不同的表和这些表上不同的属性。ER 图可用于弥合数据库或存储需求与将在数据库管理系统中实现的数据库模式之间的差距。**

**—我相信你一定在想，为什么我们需要在 DBMS 中使用 ER 图？**

**我们出于各种原因使用 ER 图，其中一些原因如下:**

*   **为我们的数据库提供了一幅逻辑图，并帮助我们将其可视化。**
*   **使用属性、关系和实体，它帮助我们确定不同表之间的关系。**
*   **给出数据库的高级概述，以及将要存储的数据类型。**

**在这里，我有一家公司的这些数据要求，这份文件的作用是描述所有不同的数据、所有信息以及这些信息之间的关系，这是一份很好的文件，如果他们为一家公司工作，希望你设计一个数据库，他们可能会得到这份文件。假设他们希望这个人创建一个数据库来存储公司的信息。**

```
The company is organized into branches. Each branch has a unique number, a name, and a particular employee who manages it.The company makes its money by selling to clients. Each client has a name and a unique number to identify it.The foundation of the company is its employees. Each employee has a name, birthday, sex, salary and a unique number.An employee can work for one branch at a time, and each branch will be managed by one of the employees. We'll also want to keep track of when the current manager started as a manager.An employee can act as a supervisor for other employees at the branch; an employee may also serve as the supervisor for employees at other branches. An employee can have at most one supervisor.A branch may handle several clients, with each client having a name and a unique number to identify it. A single client may only be handled by one branch at a time.Employees can work with clients controlled by their branch to sell them stuff. If necessary, multiple employees can work with the same client. Well, want to keep track of how many dollars' worth of stuff each employee sells to each client they work with.Many branches will need to work with suppliers to buy inventory. For each supplier, we'll keep track of their name and the type of product they're selling at the branch. A single supplier may supply products to multiple branches.
```

***让我们一起去看看吧！***

> ****1 —公司由分公司组成。每个分支机构都有一个唯一的号码、一个名称和一个管理它的特定员工。****

**![](img/8d911aa439cb9ce1a7a0bcddf34447b8.png)**

**该公司分为分公司。每个分支都有自己的编号和名称。因此，branch 将是我们的实体，它将有两个属性，一个 branch ID 将是我们的主键，因为它有一个唯一的编号和一个分支名称。**

> **这家公司通过向客户销售来赚钱。每个客户端都有一个名称和唯一的编号来标识它。**

**![](img/ff0aa2c3cd8e904e31f6a70b3004eb19.png)**

**然后公司通过向客户销售来赚钱。结果，创建了一个新实体。每个客户端都由一个名称和一个唯一的编号来标识。因此，我们有我们的客户，谁有一个客户 ID 来识别他们，以及他们的客户名称，这只是名称。**

> ****3 —公司的基础是员工。每个员工都有姓名、生日、性别、工资和唯一的编号。****

**![](img/1da4a248ea95da4b664c3de3f9399591.png)**

**在这里，实体是雇员，我们有雇员 ID，它是主键、生日和姓名，所以我们得到名和姓、工资，然后是性别。我们还有一个派生属性，它将是年龄，可以从雇员的出生日期以及他们在任何给定时间点的年龄派生出来。**

> ****4 —一名员工一次可以为一家分公司工作，每个分公司将由其中一名员工管理。我们还希望跟踪当前经理开始担任经理的时间。****

**![](img/da3548ecb754020e2a8a27e5c7ac05fd.png)**

**它说雇员一次可以为一个分支机构工作。所以，这有一个关系(基数 1:N)，即“为之工作”。这意味着这里的一个雇员可以为一个分公司工作，而一个分公司也可以有一个雇员为他们工作；它被定义为全面参与(参考:术语表)。**

**因此，接下来我们有另一个关系(基数 1:1)。上面写着“每个分支机构将由在那里工作的一名员工管理。我们还希望跟踪当前经理开始担任经理的时间”。定义了一个属性(start_date ),用于跟踪员工开始担任经理的时间。所有分支机构都必须有人管理，所以，这将是一个全面的参与，而不是所有的员工都需要成为分支机构的经理。因此，它被定义为单一参与或部分参与(参考:词汇表)。**

> ****5 —员工可以担任分公司其他员工的主管；员工也可以担任其他分支机构员工的主管。一名员工最多只能有一名主管。****

**![](img/71047300ef8bc64d84e9493d6a4ccef0.png)**

**一名员工最多只能有一名主管。所以，在这里我们得到了这种监督关系。监督关系实际上是一种员工对自己的关系。所以，这是一种员工之间的关系。一名员工可以被另一名员工监督，一名员工可以是另一名员工的主管。**

> ****6 —一个分支机构可能处理几个客户，每个客户都有一个名称和唯一的识别号。一个分支机构一次只能处理一个客户。****

**![](img/7f4aa2c5f110ea960de1020569a817be.png)**

**它是分支和客户端之间的关系(基数 1:N)。客户是完全参与的，因为每个客户都必须由一个分支机构处理，但分支机构是部分参与的，这意味着不是所有的分支机构都需要有客户，可能有一个公司分支机构不与任何客户合作。**

> **员工可以与分公司控制的客户合作，向他们出售商品。如有必要，多名员工可以与同一个客户一起工作。嗯，我想记录每个员工向他们的每个客户销售了多少价值的东西。**

**![](img/249cf2c064934b8d150ef4fdb7afd24b.png)**

**这是雇员和客户之间的关系(基数 N:M)。所有客户都需要通过一名员工与分行进行互动，因此，一名员工**与**共同参与，但并非所有员工都需要与客户进行互动。**

**公司想要记录每个员工向他们合作的每个客户销售了价值多少美元的东西！**

**在这个工作关系中，销售属性被定义为，雇员可以向客户销售，而客户可以从雇员那里购买。**

> **许多分支机构需要与供应商合作购买库存。对于每个供应商，我们将记录他们的名字和他们在分店销售的产品类型。一个供应商可以向多个分支机构供应产品。**

**![](img/ea6745e9560fb313133264d2e02473c8.png)**

**这里，需要使用弱实体和标识关系。弱势实体是“分支供应商”。它有一个供应商名称和一个供应类型，但是分支供应商将供应一个特定的分支。跟踪哪个分支机构的供应商向哪个分支机构供货。我们将不得不使用这种识别关系，这意味着分支供应商向分支机构供货，而分支机构由供应商供货。**

**![](img/4e400c8d235f2b02c341d79f72bb5989.png)**

**这基本上是我们的 ER 图，它是从“需求文档”中获取的，能够绘制出所有不同的实体、实体上所有不同的属性以及所有不同的关系。ER 图只是将所有信息链接在一起，以直观的方式表示出来。**

***现在，我们能做的就是利用这个 ER 图，根据不同的关系，不同的基数比，不同的参与度。让我们继续将它转换成数据库模式！***

****第一步:常规实体类型的映射****

**对于每个常规实体类型，创建一个包含该实体所有简单属性的关系(表)。**

****步骤 2:弱实体类型的映射****

**对于每个弱实体类型，创建一个包括弱实体的所有简单属性的关系，新关系的主键应该是弱实体的部分键加上其所有者的主键。**

****步骤 3:二元一对一关系类型的映射****

**将关系的一方作为外键包含在另一方中，支持完全参与。**

****步骤 4:二进制 1 到 N 关系类型的映射****

**将主键的 1 侧作为外键包含在 N 侧关系中。**

****步骤 5:二元 M 到 N 关系类型的映射****

**创建一个新的关系(表)，其主键是两个实体主键的组合，还包括任何关系属性。**

**![](img/3ed566ff290214897b0d80411723f666.png)**

# ****词汇表****

**实体—实体只是一个我们想要建模和存储信息的对象。**

**属性—属性是关于实体的特定信息。**

**主键—主键将是唯一标识数据库表中条目的属性。主键就像普通属性一样，但是我们要加下划线。**

**弱实体-不能仅通过其属性唯一识别的实体。**

**关系基数——关系中可与该关系相关联的实体的实例数量。**

**多值属性-可以有多个值的属性。它就像一个属性，只是多了一个圆圈。**

**多个实体-您可以在图表中定义多个实体。**

**派生属性—可以从其他属性派生的属性。**

**复合属性—可以分解为子属性的属性。**

**将它转换成关系只需要五个步骤。但是，如果有更高级类型的 ER 图，那么将会有更多的步骤。每个步骤本身都是一个数据库表。**

**这概括了我们的 ER 图，如果您有一组非常简单的数据库存储或需求，那么显然模式将非常简单，您可能不需要像 ER 图这样的东西。但是像这样的东西，ER 图非常有用。我们已经看到了不同类型的关系、实体和属性，以及将所有数据需求相互联系起来，这有助于我们创建一个数据库模式，该模式实际上将在创建数据库时简化 SQL 中的工作。**

**让我们来看看使用 SQL 创建公司数据库的最快方法[，以了解如何使用查询语言创建数据库的更多信息。](/@deepeshnishad/the-quickest-way-to-create-company-database-using-sql-f0033fdc02e7)**