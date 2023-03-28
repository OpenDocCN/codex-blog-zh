# 用 JavaScript 统一独特的价值观

> 原文：<https://medium.com/codex/unite-unique-values-with-javascript-466b1e7792c3?source=collection_archive---------9----------------------->

![](img/29ef5e85921db27a09e807baf8121041.png)

成为算法大师的旅程可能会很漫长，有时会非常令人沮丧。希望我们能不断克服疑虑，学习所有关于数据结构和算法的知识。几天前，我做了一个很好的数组练习。这个练习的目标是将多个数组中的所有内容合并成一个数组。该示例获得了三个具有随机数的上述数组，如下所示:

```
[0, 1, 3, 2, 8, 1], [5, 2, 9, 1, 4, 7, 4], [2, 1, 6, 10, 6]
```

目标是将它们组合成一个数组。

```
[0, 1, 3, 2, 8, 5, 9, 4, 7, 6, 10]
```

访问参数内容的一种方法是使用关键字`[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)`。没错，`arguments`不团结人类，但是字面上用`arguments`这个词会团结计算机数据。因此，让我们深入一些代码，了解如何在单个数组上获取一些数据。

# 解决方案 1:嵌套 For 循环

在上面的解决方案中，我们创建了结果变量，并继续使用“暴力”方法来解决问题。在这种情况下，我们需要实现两个 For 循环，因为我们要处理多个数组。

```
for (let i = 0; i < arguments.length; i++) { let arrayArguments = arguments[i];
```

在第一个循环中，我们将通过使用参数迭代参数的索引来创建`arrayArguments`变量。在那行代码中，我们正在访问我们试图联合的数组。简而言之，我们在函数的参数中声明的数组(`function uniteUnique1(arr)`)。

```
for (let j = 0; j < arrayArguments.length; j++) { let indexValue = arrayArguments[j]
```

在遍历完参数中的所有数组后，我们将使用内部循环来处理各个元素。这就是为什么我称这个新变量为`indexValue`的原因，因为它在一个数组的索引中迭代精确的值。

```
if (result.indexOf(indexValue) < 0) { result.push(indexValue);
```

现在我们已经遍历了每个数组及其值，我们将检查值是否在最终的结果数组中，如果不在，就添加它。

在推送这些值之后，我们通过返回结果数组来结束函数。

```
console.log(uniteUnique1([0, 1, 3, 2, 8, 1], [5, 2, 9, 1, 4, 7, 4], [2, 1, 6, 10, 6]));//output: [0, 1, 3, 2, 8, 5, 9, 4, 7, 6, 10]
```

# 解决方案 2: While 循环

我们通过声明`concatArray`开始第二个解决方案，因为它将是我们将在`while`循环中连接的数组:

```
while(arguments[i]) { concatArray = concatArray.concat(arguments[i]); i++;}
```

连接数组后，就可以通过过滤单个项目及其位置来创建结果变量了。这样，我们可以返回`concatArray`，它们的项目索引等于它们的每个位置:

```
result = concatArray.filter(function(item, pos) { return concatArray.indexOf(item) === pos;})
```

然后，终于到了`return result`的时候了。

```
console.log(uniteUnique2([1, 3, 2, 8, 3], [2, 1, 4, 7, 9, 6, 9], [2, 1, 6, 9]));//output: [1, 3, 2, 8, 4, 7, 9, 6]
```

# 解决方案 3:单行展开语法

在这个解决方案中，我们使用强大的[扩展语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)来扩展将要合并的可迭代数组。我们将使用`[Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)`来存储每个唯一值，使用`[.flat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)`从我们通过使用`Set`存储的值中创建一个新数组。为了让这个解决方案起作用，论点也应该展开:

```
function uniteUniqueOneLine(…arr)
```

由于`Set`适用于任何原始数据类型，让我们尝试使用一些字符串来代替数字:

```
console.log(uniteUniqueOneLine([“Earth”, “Fire”], [“Wind”, “Water”], [“Heart”, “Heart”]));
```

![](img/1fbbf72ce233f01286d854ddcb5a8dcd.png)

//输出:

所以我们有了它，我们有几个方法供我们在解决任何 JavaScript 问题时参考。我希望你学到了一些新的东西，对未来有所帮助。权力是你的！

编码快乐！

# 总结:

1.  介绍
2.  嵌套循环
3.  While 循环
4.  扩展语法

# 参考资料:

1.  MDN Web Docs，[Arguments 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)
2.  MDN Web Docs，[传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
3.  MDN 网络文档，[设置](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
4.  MDN 网络文档，[平面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)