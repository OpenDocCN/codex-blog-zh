# JavaScript 中的短路

> 原文：<https://medium.com/codex/short-circuiting-in-javascript-cbe15b5bb2c?source=collection_archive---------7----------------------->

![](img/fe548bc89054a049d88413e7f693ec80.png)

在 JavaScript 中，**短路是指表达式的部分求值，以避免不必要的计算**。这篇博文描述了 JavaScript 中的短路操作符和赋值，以及如何使用它们。

# 短路运算符:&&、||、和？？

短路运算符仅在必要时计算表达式的右侧。

比如对于逻辑 AND 运算符`&&`，当左边的运算符为 false 时，右边的运算符不会改变结果。不管右边是真还是假，结果都是假的。因此 JavaScript 跳过右边的计算，使用左边的值。

JavaScript 中有三种短路二元运算符:

*   [**逻辑与**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) ( `A && B` ): `B`只有在`A`为[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)时才被求值。
*   [**逻辑或**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR) ( `A || B` ): `B`只有在`A`为 [falsy](https://developer.mozilla.org/en-US/docsGlossary/Falsy) 时才求值。
*   [**无效合并**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator) ( `A ?? B` ): `B`只有在`A`为[无效](https://developer.mozilla.org/en-US/docs/Glossary/Nullish)时才被求值。

`A`和`B`可以是任何表达式。它们的评估可能会调用包含复杂计算或有副作用的函数和 getter 调用。
因为`A`和`B`可以返回任何值，所以这三个短路运算符计算并返回任何值，而不仅仅是布尔值。

这意味着您可以使用短路操作符用于提供默认值 ( `||`和`??`)的**，用于检查空值(`&&`，在可选链接操作符`?.`可用之前)，以及用于 React** ( `&&`)中的 [**条件渲染。**](https://reactjs.org/docs/conditional-rendering.html#inline-if-with-logical--operator)

```
// default values
a = a || 123; // assigns 123 to a if a is falsy
b = b ?? 123; // assigns 123 to b if b is nullish// optional chaining with && ( .? is a modern alterative)
if (obj.m != null && obj.m() === '123') {
   // ...
}// React: conditional rendering
return <>  
  {user != null &&
    <div>Welcome back, ${user.name}!</div>
  }
<>;
```

# 短路赋值:&&=，||=，和？？=

**用**[**es 2021**](https://h3manth.com/ES2021/)**可以在赋值表达式** ( `&&=`、`||=`、`??=`)中使用短路运算符。仅当当前变量或属性值不触发短路时，才会执行短路赋值。这种行为有助于避免不必要的更新。

这里有一个例子(`-Infinity`是[真理性](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)):

```
let a = 3;
let b = 3;
let c = 0;
let d = 0;a &&= -Infinity;
b ||= -Infinity;
c &&= -Infinity;
d ||= -Infinity;console.log(a); // -Infinity
console.log(b); // 3 (short-circuiting ||, because 3 is truthy)
console.log(c); // 0 (short-circuiting &&, because 0 is falsy)
console.log(d); // -Infinity
```

# 用短路运算符重构赋值

短路赋值看起来非常类似于带有短路操作符表达式的常规赋值。有人可能会认为，它们可以在不改变行为的情况下进行如下重构:

```
a = a && x; /* becomes */ a &&= x;
a = a || x; /* becomes */ a ||= x;
a = a ?? x; /* becomes */ a ??= x;
```

然而，当我为 [P42](https://p42.ai/) 中的
’[将操作员推入任务](https://p42.ai/documentation/code-action/push-operator-into-assignment)和[将操作员拉出任务](https://p42.ai/documentation/code-action/pull-operator-out-of-assignment)重构开发安全评估时，我发现这些重构在某些情况下会导致行为变化。

考虑下面的例子:

```
class C {
  constructor(name) {
    this.name = name;
  } get x() {
    return this._x;
  } set x(value) {
    console.log(`set ${this.name}.x to ${value}`);
    this._x = value;
  }
}// nullish coalescing operator
const a = new C("a");
a.x = a.x ?? 3;
a.x = a.x ?? 4;
console.log(a.x)// nullish assignment 
const b = new C("b");
b.x ??= 3;
b.x ??= 4;
console.log(b.x)
```

令人惊讶的是，将操作员移动到分配中会改变要执行的操作:

```
// output for nullish coalescing operator
"set a.x to 3"
"set a.x to 3"
3// output for nullish assignment 
"set b.x to 3"
3
```

虽然这些微小的差异在大多数时候并不重要，但出于两个原因，意识到这些差异是有好处的:

*   它们会在重构过程中导致崩溃，因为它们会改变现有的行为
*   它们有利于减少性能热点中的操作数量

# 摘要

JavaScript 中的短路操作符(`&&`、`||`、`??`)只在必要时才计算它们右边的表达式。它们的赋值等价物(`&&=`、`||=`、`??=`)仅在当前值将
导致短路操作符右侧的执行时更新一个值。