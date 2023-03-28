# JavaScript 的危险和差异。innerHTML，。innerText，和。文本内容

> 原文：<https://medium.com/codex/the-dangers-and-differences-of-javascript-innerhtml-innertext-and-textcontent-d82711e873c8?source=collection_archive---------24----------------------->

在 JavaScript 中，有几种方法可以编辑元素的内容，但是如何知道选择哪一种呢？让我们进一步了解一下。innerHTML，。innerText，和。让这个决定更容易。

首先快速定义一下，元素属性。innerHMTL 获取或设置元素中包含的 HTML 或 XML 标记。它可以用来“检查页面的当前 HTML 源代码，包括自页面最初加载以来所做的任何更改”。

```
 *let content = element.innerHTML*
```

这种方法可以很容易地让您用新的内容替换元素的现有内容，但不会没有任何后果。这可能不是最安全的选择，因为可能会在字符串中插入恶意内容。为了解决这个问题，使用元素。当使用用户提供的数据来“净化”内容时，应该考虑 SetHTML()。

![](img/7d6326def6972c6f9270b436e65e79b4.png)

由于这种潜在的安全风险，请注意，如果您的项目代码经过任何类型的安全审查并使用. innerHTML，它很可能会被拒绝。

潜在安全风险较小的替代方法是。我们可以在这里看到一个例子。

```
 *      let text = element.innerText;*
```

。innerText 和 node.textContent，我们接下来会讲到，很容易混淆，但一个重要的区别是。innerText 知道文本的呈现外观。当它检索内容时，它“近似于用户用光标突出显示元素的内容，然后将其复制到剪贴板时得到的文本”。然后它会‘用给定的值替换元素的子元素，将任何换行符转换成`[<br>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/br)`元素’。

最后但同样重要的是，我们有 Node.textContent。但是它不知道像. innerText 这样的文本的外观。

的一些关键元素。文本内容:

*   `textContent`获取*所有*元素的内容，包括`[<script>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)`和`[<style>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style)`元素。相比之下，`innerText`只显示“人类可读”的元素。
*   `textContent`返回节点中的每个元素。相比之下，`innerText`知道样式，不会返回“隐藏”元素的文本。
*   由于`innerText`考虑了 CSS 样式，读取`innerText`的值触发[回流](https://developer.mozilla.org/en-US/docs/Glossary/Reflow)以确保最新的计算样式。(回流可能计算量很大，因此应尽可能避免。)
*   `textContent`和`innerText`在被修改时都会删除子节点，但是在 Internet Explorer(版本 11 及以下)中修改`innerText`也会永久删除*所有的子文本节点。在这样做之后，不可能将节点再次插入任何其他元素或同一元素。*

使用时。innerHMTL，它允许跨站脚本(XSS)的漏洞，根据开放 Web 应用安全项目，这是 2017 年第七大最常见的 Web 应用漏洞。在决定使用什么属性时，不要忘记始终保护您的数据！

![](img/f62f31e03f1632eb4c1d1340546702cc.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

来源

[](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) [## element . innerhtml-Web API | MDN

### 设置 innerHTML 的值可以让您轻松地用新内容替换元素的现有内容。注意:这个…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) [](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText) [## html element . innertext-Web API | MDN

### HTMLElement 接口的属性表示节点及其后代的呈现文本内容。作为一个…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText) [](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) [## node . text content-Web API | MDN

### 一个字符串，或者。它的值取决于具体情况:如果节点是或 doctype，则 textContent 返回。注意:要获得所有…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) [](https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting) [## 跨站点脚本(XSS) - MDN Web 文档词汇表:Web 相关术语的定义| MDN

### 跨站点脚本(XSS)是一种安全漏洞，它允许攻击者向网站注入恶意…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting)