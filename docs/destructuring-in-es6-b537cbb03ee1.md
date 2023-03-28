# ES6 中的析构

> 原文：<https://medium.com/codex/destructuring-in-es6-b537cbb03ee1?source=collection_archive---------15----------------------->

![](img/e6d2d6aef8ab9194c1d3a60ceb0a7a11.png)

塔妮娅·梅尔尼克祖克在 [Unsplash](https://unsplash.com/s/photos/split?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

destructure 的意思是拆除某物的结构。在 Javascript 中，它可以是一个数组、一个对象或一个字符串，它们被单独分解，然后重新分配给变量。

## **破坏数组**

在 ES6 之前，如果您要将一个数组的值赋给单个变量，它会像这样完成:

```
var scores = [500, 400, 300];

var x = scores[0],
    y = scores[1],
    z = scores[2];

    console.log(x,y,z); // 500 400 300
```

但是后来 ES6 出现了。让我们在下面的例子中看看这是如何改变的:

```
let scores = [500, 400, 300];

let [x, y, z] = scores;

console.log(x,y,z); //500 400 300
```

很好理解。我们析构了 scores 数组的值，并创建了一些变量，这些变量将按照定义的顺序继承数组的元素。因此，假设 x 是分数数组的第一个元素，即 500，y 是第二个元素，即 400，z 是第三个元素。

> 注意**【x，y，z】**是**而不是**一个**新数组**。

在我们析构一个数组并创建比数组元素更多的变量的情况下，每个没有元素继承的变量都会返回 undefined。下面的例子:

```
let scores = [500, 400, 300];

let [x, y, z, w] = scores;

console.log(x,y,z,w); //500 400 300 undefined
```

我们添加了一个新变量“w ”,但是由于没有元素留给它从析构数组中继承，它返回为未定义。

> 可以将默认值赋给 w，因为它已经没有可继承的元素了，请参见下面的示例:

```
let scores = [500, 400, 300];

let [x, y, z, w = 200] = scores;

console.log(x,y,z,w); //500 400 300 200
```

***跳过原数组中的元素***

在您想要跳过继承顺序并跳转到数组中不同元素的情况下，可以通过在原始数组中要跳过的元素的位置放置一个逗号，逗号之间留有空格来实现。看一看:

```
let scores = [500, 400, 300];

let [, x, y] = scores;

console.log(x, y); // 400 300
```

我们跳过了数组(500)中的第一个元素，从第二个元素开始赋值。

***使用休息参数***

> 假设你已经熟悉了 [**休止参数**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) 和 [**传播语法**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 以及如何使用它们。

```
let scores = [500, 400, 300];

let [x, ...y] = scores;

console.log(x); // 500

console.log(y); // [400, 300]
```

在上面的示例中，使用 rest 参数语法“…”将 x 赋给数组中的第一个元素，将“其余”的元素赋给 y，这将返回一个包含分配给它的元素的数组。

## **破坏物体**

在上面的例子中，我们正在解构数组。现在让我们从一个例子开始，看看如何析构对象:

```
let scores = {
    pass: 70,
    avg: 50,
    fail: 30
};

let { pass, avg, fail} = scores;

console.log(pass, avg, fail); // 70 50 30
```

您已经可以看到不同之处——花括号和我们在析构时使用对象名作为变量名的事实；更改名称会返回 undefined，但这并不意味着您必须使用它们。让我们看看如何覆盖对象名:

```
let scores = {
    pass: 70,
    avg: 50,
    fail: 30
};

let { pass: one, avg: two, fail: three} = scores;

console.log(one, two, three); // 70 50 30
```

现在我们已经指定了新的变量名，它将覆盖默认的对象名。

## 析构字符串

```
let [user, interface] = 'UI';

console.log(user); // U

console.log(interface); // I
```

这里，字符串“UI”被单独分开并分配给所创建的变量。但是如果字符串中有更多类似“UI 很重要”的字符会怎么样呢？输出会是什么？输出将保持不变，因为字符串的析构不是一个单词接一个单词地进行，而是一个字符接一个字符地进行，所以“U”字符首先被赋值，然后是“I ”,即使它们在字符串中被组合在一起。

对于本文来说就是这样。我希望在这一点上，你已经理解了析构是如何工作的，并且随着你作为一名开发人员的进步，你会很舒服地使用它们。感谢您的阅读。[你也可以订阅，把我的文章直接发到你的邮箱里。](/subscribe/@ihechivinabba)快乐编码！