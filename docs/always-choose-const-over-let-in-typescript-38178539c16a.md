# 在 TypeScript 中总是选择“const”而不是“let”

> 原文：<https://medium.com/codex/always-choose-const-over-let-in-typescript-38178539c16a?source=collection_archive---------7----------------------->

我们生活在一个代码的世界里。根据文章“计算机代码写了多少？”根据 Sage McEnery 的说法，在过去的 20 年中，大约有 2.8 万亿行代码被编写出来。如果你是一名软件开发人员，你最终会遇到这些问题，因为从圣诞灯泡的控制器到隔壁的电动汽车和商场的收银机，所有东西都包含代码。

![](img/cec57d65367ea2aed662998a1a2c7c5a.png)

我曾经相信，当我刚开始作为一名软件开发人员工作时，我只会阅读和更新我开发的代码。今天，审查和修改别人的代码占据了我大约 80%的工作。

这就是**干净代码**发挥作用的地方。

可读性是干净代码的基本标准之一。想象一下，必须用混乱的代码维护软件。噩梦，对吧？尽管我还有很多东西要学，但我已经有了一些保持代码整洁的标准。其中之一就是我在 TypeScript 中总是用`const`代替`let`。

## 好吧，但是为什么？

想一想你需要维护一个`express.js`后端服务功能的情况

```
⬇️ **EXAMPLE SERVICE FUNCTION**const getUsers = async (): Promise<User[]> => {
  let users = await db.findAll...
  return users?.data || []
}
```

服务的主要职责通常是与数据库进行交互，并将数据映射或过滤到适当的响应结构中

```
❌ **EXTENDED WITH FILTER AND MAPPER WITH REDEFINING**const getUsers = async (): Promise<User[]> => {
  let users = await db.findAllInDB...
  **users = users?.data || []
  users = users.filter(user => user?.data?.name && user?.data?.id)
  users = users.map(user => {
    id: user.id,
    firstName: user.name.split(' ')[0],
    loyaltyPoints: await getUserLoyaltyPoints(user.id)
  })**
  return users
}
```

在我看来，上面的代码一点也不干净。在初始定义`users`变量后，进行了三次更新。随着项目的扩展，阅读它变得越来越困难，因为你总是必须阅读完整的行并记住变化；`users`变为 `users.data`，然后`filter`被应用，`map`被应用，以此类推。

产生更可读代码的干净方法是总是用`const`和有意义的名字重新定义变量

```
🚀 **REDEFINING WITH CONST IS A GOOD PRACTICE**const getUsers = async (): Promise<User[]> => {
  const users = await db.findAllInDB...
  **const existingUsers = (users?.data || []).filter(
   user => user?.data?.name && user?.data?.id
  )
  const formattedUsers = existingUsers.map(user => {
    id: user.id,
    firstName: user.name.split(' ')[0],
    loyaltyPoints: await getUserLoyaltyPoints(user.id)
  })
  return formattedUsers**
}
```

这些步骤更容易理解。在`users`的数据库检索之后，我们定义`existingUsers`(即实际存在的带有`id`和`name`的用户)，然后返回带有`firstName`和`loyaltyPoints`的`formattedUsers`。

正如我前面提到的，函数或项目越大，如果单个变量被重复覆盖，代码就越难理解。

👋我希望这在你寻找新的学习材料时对你有用。感谢您花时间阅读这篇文章！如果你想在未来读到更多这样的故事，请订阅我的电子邮件列表。