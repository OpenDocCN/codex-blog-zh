# 您应该知道的 13 个有用的 JavaScript 数组技巧和窍门

> 原文：<https://medium.com/codex/13-useful-javascript-array-tips-and-tricks-you-should-know-85185e805d4d?source=collection_archive---------0----------------------->

![](img/0cd2939b15e0f6b3f22505cc1999760c.png)

马库斯·乌尔本斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

数组是 Javascript 最常见的概念之一，它为我们处理存储在其中的数据提供了很多可能性。考虑到数组是 Javascript 中最基本的主题之一，您在编程之初就要学习它，在本文中，我将向您展示一些您可能不知道的技巧，它们可能对编码非常有帮助！让我们开始吧。

# 1.从数组中删除重复项

关于 Javascript 数组，如何从 Javascript 数组中提取唯一值，这是一个很流行的面试问题。这里有一个快速简单的方法来解决这个问题，你可以使用一个新的 Set()来实现这个目的。我想向你们展示两种可能的方法，一种是。from()方法和带有扩展运算符(…)的 second。

# 2.替换数组中的特定值

有时在创建代码时，需要替换数组中的特定值，有一个很好的简单方法可以做到这一点，但您可能还不知道。为此，我们可以使用。splice(start，value to remove，valueToAdd)并向其传递所有三个参数，指定我们希望开始修改的位置、我们希望更改多少值以及新值。

# 3.没有映射数组。地图()

可能每个人都知道。map()方法，但是有一个不同的解决方案可以用来获得类似的效果和非常干净的代码。我们可以利用。from()方法。

# 4.清空数组

你是否有一个数组充满了元素但出于任何目的都需要清理它，你又不想一个一个的移除条目？只用一行代码就可以完成，非常简单。清空一个数组，需要把数组的长度设置为 0，就这样！

# 5.将数组转换为对象

碰巧我们有一个数组，但出于某种目的，我们需要一个包含这些数据的对象，将数组转换为对象的最快方法是使用众所周知的 spread 运算符(…)。

# 6.用数据填充数组

有些情况下，当我们创建一个数组，我们想用一些数据填充它，或者我们需要一个数组有相同的值，在这种情况下。fill()方法提供了一个简单明了的解决方案。

# 7.合并数组

你知道如何将数组合并成一个数组吗？concat()方法？有一种简单的方法可以将任意数量的数组合并成一行代码。正如你可能已经意识到的，spread 运算符(…)在处理数组时非常有用，在这种情况下也是如此。

# 9.从数组中删除 falsy 值

首先，让我们定义虚假值。在 Javascript 中，falsy 值为 false、0、“”、null、NaN、undefined。现在我们可以知道如何从数组中删除这种值。为了实现这一点，我们将使用。filter()方法。

# 10.从数组中获取随机值

有时我们需要从数组中随机选择一个值。为了以一种简单、快速、简短的方式创建它，并保持代码整洁，我们可以根据数组长度获得一个随机的索引号。让我们看看代码:

# 11.反转数组

当我们需要翻转数组时，没有必要通过复杂的循环和函数来创建数组，有一个简单的数组方法可以为我们做所有的事情，只需一行代码，我们就可以反转数组。让我们检查一下:

# 12.。lastIndexOf()方法

在 Javascript 中，有一个有趣的方法，允许查找给定元素最后一次出现的索引。例如，如果我们的数组有重复的值，我们可以找到它最后出现的位置。让我们看看代码示例:

# 13.对数组中的所有值求和

Javascript 工程师面试中经常遇到的另一个挑战。没有可怕的东西来到这里；这个问题可以用。一行代码中的 reduce 方法。让我们来看看代码:

# 结论

在本文中，我向您介绍了 13 个技巧和提示，它们可以帮助您编写代码，并保持代码简洁明了。另外，记住在 Javascript 中有很多不同的技巧值得探索，不仅仅是关于数组，还有不同的数据类型。我希望您喜欢文章中提供的解决方案，并且您将使用它们来改进您的开发过程。

好好编码！

作为一个新手，你可以看看我的朋友[jrpotodev](https://medium.com/u/e1ddaee057c5?source=post_page-----85185e805d4d--------------------------------)写的 React js 中有哪些容易混淆的部分。

[](https://levelup.gitconnected.com/react-js-the-confusing-parts-4e9aea20c94c) [## React JS —令人困惑的部分

### 了解初学者容易遇到的 React 的常见混淆部分。

levelup.gitconnected.com](https://levelup.gitconnected.com/react-js-the-confusing-parts-4e9aea20c94c) 

如果您不是会员，请考虑通过以下链接注册帮助支持作者:

[](https://crunchyknuckles.medium.com/membership) [## 阅读来自 Tenish 的每一个故事(以及媒体上成千上万的其他作家)

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

crunchy nuckles . medium . comom](https://crunchyknuckles.medium.com/membership) 

我写的一些关于 Javascripts 和 React 的文章:

[](/geekculture/how-to-send-emails-from-a-form-in-react-emailjs-6cdd21bb4190) [## 如何从 React (EmailJS)中的表单发送电子邮件

### 介绍

medium.com](/geekculture/how-to-send-emails-from-a-form-in-react-emailjs-6cdd21bb4190) [](https://javascript.plainenglish.io/10-awesome-javascript-libraries-you-should-try-out-in-2021-34ee431cd341) [## 2021 年你应该尝试的 10 个超棒的 JavaScript 库

### JavaScript 是网络上最流行的语言之一。尽管它最初只是为网页开发的…

javascript.plainenglish.io](https://javascript.plainenglish.io/10-awesome-javascript-libraries-you-should-try-out-in-2021-34ee431cd341) [](https://javascript.plainenglish.io/these-npm-tricks-will-make-you-a-pro-1373e7fd34f7) [## 这些 NPM 技巧将使你成为一名职业选手

### 我一路走来学到了很多我希望一开始就知道的东西。让我们称之为技巧，彻底…

javascript.plainenglish.io](https://javascript.plainenglish.io/these-npm-tricks-will-make-you-a-pro-1373e7fd34f7)