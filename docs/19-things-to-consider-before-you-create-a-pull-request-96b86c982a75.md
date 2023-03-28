# 创建拉式请求前需要考虑的 19 件事

> 原文：<https://medium.com/codex/19-things-to-consider-before-you-create-a-pull-request-96b86c982a75?source=collection_archive---------6----------------------->

## 让您的拉动式请求更有成效

![](img/408ff9c0af3712e355c7287f96781069.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

让你的代码被分析会让人感觉受到侵犯和不舒服。更糟糕的是，审查其他人的代码可能感觉像一个痛苦而模糊的练习，你在寻找问题，甚至不知道从哪里开始。

代码评审是软件工程过程中的一个必要部分，因为你一个人不可能发现你写的一段代码中的每一个问题。不过，没关系。即使是世界上最好的篮球运动员也会投篮不中。

让他人评审我们的工作可以确保我们以最少的错误向用户交付最好的产品。

本文旨在提供一些坚实的要点，您和您的团队可以遵循这些要点来提高工作效率。

# 要避免的事情

## 1.基本格式器和棉绒

避免讨论静态分析工具可以处理的细节。不要争论细微差别，比如代码格式和是使用`let`还是`var`。有一个格式化程序和 linter 可以让你的计算机为你做评论，从而为你的团队节省很多时间。

总结:代码评审应该尽可能自动化。

## 2.基本讨论或困惑

这些讨论应该在编写代码之前进行。一旦你浇注了混凝土地基，就不要试图争论平面图。

总结:代码评审应该避免 API 讨论。

## 3.不明确的提交消息

提交消息应该清晰，并且应该准确地描述新代码。避免编写如下消息。

```
Changed some crap
damn it
one more to fix this stupid bug
```

这些都很有趣，令人满意，但是当你在周六早上起床时却没有帮助，因为你在周五晚上推送代码，却不知道当你`git blame`提交时坏代码在做什么。编写提交消息，准确描述代码，并包括来自您的问题跟踪系统(如果有)的票据编号。这将使搜索提交日志变得更加容易。

## 4.避免公关中的待办事项

待办事项注释可以让你和你的工程师同事知道有些事情需要在以后解决。它们表明缺少了什么，不应该让人觉得任务完成了。

# 要遵循的东西

## 5.错别字应该改正

尽可能避免吹毛求疵，把这些留给你的 linter、编译器和格式化程序。当你做不到的时候，比如在打字错误的情况下，留下一个友好的评论，建议修改。有时候是一些小事造成了很大的不同！

## 6.变量名和函数名应该清晰

命名是计算机科学中最难的问题之一。我们都给变量、函数和文件起过令人困惑的名字。如果你读的名字不清楚，建议你的队友一个更清晰的名字。

```
// This function could be better named as namesToUpperCase
function u(names) {
  // ...
}
```

## 7.函数应该简短

函数应该做一件事！长函数通常意味着它们做得太多。告诉你的队友把这个功能分成多个不同的功能。

```
// This is both emailing clients and deciding which are active. Should be
// 2 different functions.
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

## 8.文件应该是连贯的，最好是简短的

就像函数一样，文件应该只包含一件事。一个文件代表一个模块，一个模块应该为你的代码库做一件事情。

例如，如果你的模块叫做`fake-name-generator`，它应该只负责创建假名字，比如“Keyser sze”如果`fake-name-generator`还包括一堆用于查询姓名数据库的实用函数，那么它应该在一个单独的模块中。

没有规定一个文件应该有多长，但是如果它很长，就像下面这样，并且包含了彼此不相关的函数，那么它可能应该被拆分。

```
// Line 1
import _ from 'lodash';
function generateFakeNames() {
  // ..
}// Line 1128
function queryRemoteDatabase() {
  // ...
}
```

## 9.复杂的代码应该被注释

如果你给事物起了个好名字，但逻辑仍然混乱，那么是时候做个评论了。

```
function leftPad(str, len, ch) {
  str = str + '';
  len = len - str.length;while (true) {
    // This needs a comment, why a bitwise and here?
    if (len & 1) pad += ch;
    // This needs a comment, why a bit shift here?
    len >>= 1;
    if (len) ch += ch;
    else break;
  }return pad + str;
}
```

# 考虑副作用

## 10.函数应该尽可能的纯净

```
// Global variable is referenced by the following function.
// If we had another function that used this name, now it'd be an array and it
// could break it. Instead it's better to pass in a name parameter
let name = 'Ryan McDermott';function splitIntoFirstAndLastName() {
  name = name.split(' ');
}splitIntoFirstAndLastName();
```

## 11.I/O 功能应该处理失败案例

任何进行 I/O 的函数都应该在出错时进行处理。

```
function getIngredientsFromFile() {
  const onFulfilled = buffer => {
    let lines = buffer.split('\n');
    return lines.forEach(line => <Ingredient ingredient={line} />);
  };// What about when this rejected because of an error? What do we return?
  return readFile('./ingredients.txt').then(onFulfilled);
}
```

# 考虑边缘情况

## 12.应处理空案例

例如，如果您有一个列表组件，如果您显示一个漂亮的表来显示它的所有数据，一切都很好。你的用户喜欢它，你得到了提升！但是当没有数据返回时会发生什么呢？在空的情况下你显示了什么？你的代码应该对任何可能发生的情况都有弹性。如果你的代码中有不好的事情发生，最终它会发生。

## 13.应该处理大案

在上面的列表中，如果 10，000 件商品从库存中返回，会发生什么情况？在这种情况下，您需要某种形式的分页或无限滚动。确保总是从数量的角度评估潜在的边缘情况，尤其是当涉及到 UI 编程时。

## 14.用户输入应该受到限制

用户可以输入无限量的数据发送给你。如果一个函数接受任何类型的用户数据，设置限制是很重要的。

```
router.route('/message').post((req, res) => {
  const message = req.body.content;// What happens if the message is many megabytes of data? Do we want to store
  // that in the database? We should set limits on the size.
  db.save(message);
});
```

## 15.函数应该处理意外的用户输入

用户总是会用他们给你的数据给你惊喜。不要期望在用户的请求中总能得到正确类型的数据，甚至是任何数据。[并且不依赖客户端单独验证](https://twitter.com/ryconoclast/status/885523459748487169)。

```
router.route('/transfer-money').post((req, res) => {
  const amount = req.body.amount;
  const from = user.id;
  const to = req.body.to;// What happens if we got a string instead of a number as our amount? This
  // function would fail
  transferMoney(from, to, amount);
});
```

# 安全性

## 16.XSS 应该不可能

跨站点脚本(XSS)是 web 应用程序安全攻击的最大载体之一。当您获取用户数据并将其包含在您的页面中而没有先对其进行适当的清理时，就会发生这种情况。这可能会导致您的站点从远程页面执行源代码。

## 17.个人身份信息(PII)不应泄露

每次接收用户数据时，你都要承担巨大的责任。如果您在 URL、分析跟踪中向第三方泄露数据，或者甚至将数据暴露给不应该访问的员工，您将极大地伤害您的用户和您的业务。小心别人的生命！很严重。

# 最后，测试

## 18.应该测试新代码

所有的新代码都应该包含一个测试，不管它是修复了一个错误还是一个新特性。如果是 bug 修复，应该有测试证明 bug 已经修复。如果它是一个新特性，那么每个组件都应该进行单元测试，并且应该有一个集成测试来确保该特性与系统的其余部分一起工作。

## 19.测试实际上应该测试函数做的所有事情

测试应该覆盖一个函数所做的一切，包括边缘情况和它所操作的限制。

## 这篇文章的灵感来自这个 [GitHub](https://github.com/ryanmcdermott/code-review-tips#why-review-code) 项目。快乐编码。

## 感谢阅读！