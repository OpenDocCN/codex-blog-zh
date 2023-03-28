# 为什么我们选择 Prisma 作为 Wasp 的数据库层

> 原文：<https://medium.com/codex/why-we-chose-prisma-as-a-database-layer-for-wasp-2db01d158429?source=collection_archive---------7----------------------->

![](img/9bf95eaf786e419133e9c953a41a53a7.png)

Wasp 是一个全栈 JS web 开发框架，涵盖前端、后端和数据库。当选择在其上构建数据库层的解决方案时，我们选择了 Prisma，尽管当时它还是一种新技术，我们相信今天我们做出了一个伟大的选择-->继续阅读了解原因！

在 Wasp，[我们的目标是](https://wasp-lang.dev/docs/vision)通过专门的高级语言简化全栈 web 开发。这种语言允许您简洁地描述 web 应用程序的主要部分，避免了许多常见的样板文件和配置，同时为您提供了许多功能并确保了最佳实践。Wasp 本质上是一个全栈 web 框架，作为一种专用语言来实现，与 React & Node.js 协同工作！

当我们开始开发 Wasp 时，我们希望它简单易学，切中要点，所以我们决定:

*   Wasp 语言应该只在高层使用，所以你仍然会使用 React、NodeJS、HTML、CSS 等等。实现您的自定义逻辑。如果一个全栈的 web app 是一支管弦乐队，Wasp 就是指挥。
*   Wasp 语言应该是声明性的和简单的，非常类似于 JSON，但是“更聪明”,因为它理解 web 应用程序的概念，并确保你的应用程序遵循这些概念。

考虑到这一点，我们专注于识别值得用 Wasp 语言捕捉的高级 web 应用程序概念。我们确定了 web 应用程序的以下部分:

*   一般应用程序信息(标题、标题、图标等)
*   页面和路线
*   数据模型(又名实体)，例如用户、任务、组织、文章等等。
*   操作(客户端和服务器之间的通信；数据模型上的 CRUD、第三方 API 等等)
*   部署

# [实体](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#entities)

其中，实体位于所有事物的中间，存在于整个代码库中，是 web 应用程序所有其他部分的核心:客户端、服务器和数据库。然而，它们也是最难实现的部分！

当我们开始时，我们想象一个实体在 Wasp 中看起来像这样:

```
entity User {
  id: Id,
  username: String @unique,
  email: String @unique
  groups: [Group]
}
```

虽然将这种初始语法添加到我们的语言中是可行的，但为了使它成为一个合适的解决方案，还有更大的任务要处理:

*   扩展语法，使其足够灵活以适应现实生活中的用例
*   支持迁移(数据和模式)
*   生成用户可以从 JS/TS 调用的代码，以查询和更新数据库中的实体
*   可能还有很多其他我们还没有想到的事情！

# 猫鼬，红杉，…还是棱镜？[](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#mongoose-sequelize--or-prisma)

我们已经决定为 JS/TS 选择一个 ORM(ish)解决方案，并在此基础上构建其余的特性。我们开始评估不同的品种:猫鼬、红杉、类猫鼬……

但是后来我们看了看 Prisma，赢家很明显！Prisma 不仅照顾到了我们所关心的一切，而且它还有一个额外的特点，使它非常适合:

```
model User {
  id          Int     @id @default(autoincrement())
  username    String  @unique
  password    String
}
```

不，这不是关于 Wasp 语言中实体语法的另一个想法→这是 Prisma 模式语言(PSL)！！！

# 棱镜模式语言(PSL)[](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#prisma-schema-language-psl)

事实上，Prisma 是独一无二的，它有一种特殊的、声明性的语言来描述数据模型(schema)，这正是我们需要 Wasp 的。

因此，我们决定使用 Prisma 和他们的 PSL 来描述 Wasp 语言中的实体(数据模型),而不是实现我们自己的描述实体的语法。

今天，实体在 Wasp 语言中是这样描述的:

```
... some Wasp code ...

entity User {=psl
  id          Int     @id @default(autoincrement())
  username    String  @unique
  password    String
psl=} 

... some Wasp code ...
```

所以在 Wasp 中间，你只要切换到写 PSL (Prisma Schema Language)来描述一个实体就行了！

另一件很棒的事情是，PSL 在本质上是一种非常简单的语言，所以我们[为它实现了我们自己的解析器](https://github.com/wasp-lang/wasp/blob/main/waspc/src/Wasp/Psl/Parser/Model.hs)→这意味着 Wasp 实际上理解你写的东西，即使它是 PSL，并且可以完全使用它。因此，通过使用 PSL 而不是我们自己的语法，我们没有损失任何东西，反而获得了 Prisma 带来的所有特性。

# 其他福利[](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#other-benefits)

除了 PSL，还有很多其他原因让我们觉得 Prisma 非常适合我们:

*   它的目标是 Javascript / Typescript。
*   它负责迁移，并有一个很好的工作流程来完成迁移。
*   它支持不同的数据库:Mongo、PostgreSQL、CockroachDB 等等，这对 Wasp 非常重要，因为我们的目标是在未来支持不同的栈。
*   它有 Prisma Studio-UI 用于检查您的数据库，我们也通过 Wasp CLI 提供给您。
*   它持续快速改进，非常注重良好的开发者体验，这也是我们在 Wasp 的关注点。
*   社区非常热情，核心团队非常乐于助人——我们所有的问题都得到了非常迅速的回答！

# 挑战[](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#challenges)

虽然 Prisma 与 Wasp 的整合进行得非常顺利，但还是有一些小问题:

*   让 Prisma CLI 在 Wasp 以编程方式调用时提供交互式输出是很棘手的，最后，我们不得不使用一种有点卑鄙的方法来欺骗 Prisma CLI，让它认为它是以交互方式调用的。我们已经向 Prisma 提出了这个问题，所以希望这个问题一旦解决，我们就能删除它:[https://github.com/prisma/prisma/issues/7113](https://github.com/prisma/prisma/issues/7113)。
*   在早期，有一些错误，但是，他们总是很快得到解决，所以更新到最新的 Prisma 版本通常是解决方案。
*   让 Prisma 在服务器根目录之外使用它的模式花了我们一些时间，但是我们最终还是让它工作了！

这些大部分是由于我们扩展了 Prisma 的使用范围，但是总的来说 Prisma 被证明是相当灵活的！

# [概要](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#summary)

Prisma 使用声明性语言来描述模式，关注人体工程学，并以 JS/TS 作为目标语言，这对我们来说真是一种幸运——如果没有它，要让这些实体在 Wasp 中工作就要付出更多的努力。

当我们开始使用它时，Prisma 仍然有点早，它肯定是我们堆栈中最不成熟的技术——但我们决定赌一把，因为它非常适合，而且非常有意义。今天，随着 Prisma 成为成熟和受欢迎的解决方案，我们非常高兴我们做出了这个选择！

# 未来[](https://wasp-lang.dev/blog/2022/11/28/why-we-chose-prisma#future)

Prisma 已经在 Wasp 中扮演了重要角色，但我们还有更多的计划和希望:

*   支持 Prisma 的枚举和类型声明
*   展示更多 Prisma 的 CLI 命令，尤其是数据库播种
*   在 Wasp 中增加对多个数据库的支持(Prisma 已经支持)
*   在 Wasp 语言中改进 IDE 对 PSL 的支持

如果你有兴趣在这些方面提供帮助，请就这个问题联系我们[https://github.com/wasp-lang/wasp/issues/641](https://github.com/wasp-lang/wasp/issues/641)，或者无论如何，加入我们的[不和谐服务器](https://discord.gg/rzdnErX)！