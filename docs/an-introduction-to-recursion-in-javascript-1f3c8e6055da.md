# JavaScript 中递归的介绍

> 原文：<https://medium.com/codex/an-introduction-to-recursion-in-javascript-1f3c8e6055da?source=collection_archive---------6----------------------->

![](img/e8f0c26af9467a89d63121573194e228.png)

在我更好地理解和使用算法的过程中，我最近决定攻克递归。最简单地说，递归是一种从函数内部调用函数的技术。递归不仅是计算机科学的一种技术，而且在计算机存在之前很久就是一种数学模式。

# 简单递归:斐波那契数列

事实上，人们可能最熟悉的递归是斐波那契数列，其中序列中的下一个数字由前两个数字的和决定。该序列被植入两个初始值:0 和 1。从这一点出发，递归可以用来无限地生成序列:

0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144…

由于这是一个简单且众所周知的递归，我将把它作为我编写递归方法的第一个例子。首先，必须决定函数是否需要任何输入，以及是否有任何特殊条件。对于斐波那契数列，我们知道第 0 个和第 1 个成员分别是 0 和 1，所以我们可以将它们设置为函数的特殊条件，以找到斐波那契数列的第 n 个成员。

```
const fibonacci = n => {
  if(n < 2){
    return n
  }
}
```

现在，如果我们向我们的函数请求序列的第 0 或第 1 个成员，它将如预期的那样返回 0 或 1。但是如何返回更高指数的数字呢？答案是在函数的前一个输出上调用函数，也就是说，在序列的前几个成员上调用函数。用伪代码来说明这一点:

```
const fibonacci = n => {
  if(n < 2){
    return n
  }
  // if we enter 4 as n, we want our function to add the values
  // at the 3rd and 2nd indices
  // we can get those using our same function for 3 and 2, or
  // fibonacci(n - 1) and fibonacci(n - 2)
}
```

现在让我们用有效的 JavaScript 来写:

```
const fibonacci = n => {
  if(n < 2){
    return n
  }
  return fibonacci(n - 1) + fibonacci(n - 2)
}
```

现在，我们的函数将重复执行(调用自身),直到它计算出索引 n 处的斐波那契数。对 4 的 n 调用我们的函数将产生数字 3，这是斐波那契数列的第四个索引成员:

```
n = 4, fibonacci(n) === 3
0, 1, 1, 2, 3
```

这很好，但是有更低时间复杂度的方法来执行简单的操作，比如查找序列的第 n 个成员。如果我需要做更复杂的事情呢？

# 复杂递归:组合学

一个非常常见的编码问题要求我们找出给定一组条件的组合数。例如，在给定一系列数字[1，n]的情况下，找出长度为 k 的唯一组合的数量。对于这个问题，顺序无关紧要，意味着[1，2，3]被认为与[3，2，1]相同。我们如何使用递归来解决这样的问题？

第一步是决定函数需要什么样的输入才能被调用。

```
const comboFinder = (n, k, index = 1, temp = [], returnArray = []) => {
  // n is the highest number in the specified range
  // k is the length of combinations we're after
  // index represents where we are in the range [1,n] 
  // temp is an array to store combinations until they are length k
  // returnArray is the array of combinations to return at the end
}
```

调用这个函数的人只需要提供 n 和 k，因为其他三个变量在函数的参数列表中都有默认值。下一步是考虑我们希望函数做什么。我们知道我们需要进行组合，包括范围内的每个数字(由 index 表示)，所以很可能我们需要一个循环来完成这个动作。

```
const comboFinder = (n, k, index = 1, temp = [], returnArray = []) => {
  for(let i = index; i <= n; i++){
    let modTemp = [...temp, i]
    comboFinder(n, k, i + 1, modTemp, returnArray)
  }
}
```

这个 for 循环将索引移动到范围内的每个数字上，查找每个数字的组合。在循环中，comboFinder 调用自身将索引以外的数字添加到临时数组中。但是就目前的代码而言，没有任何东西添加到 returnArray 中，也没有任何东西将 temp 数组限制为长度 k。所以我们需要添加一些代码，以便在 returnArray 达到长度 k 时将 temp 添加到 return array 中，然后重置它以进行更多的循环。

```
const comboFinder = (n, k, index = 1, temp = [], returnArray = []) => {  
  if(temp.length ===k){
    returnArray.push(temp)
    return returnArray
  }
  for(let i = index; i <= n; i++){
    let modTemp = [...temp, i]
    comboFinder(n, k, i + 1, modTemp, returnArray)
  }
}
```

需要注意的是，JavaScript 语言的一个特性在 if 语句中发挥了作用——也就是说，如果将整个数组推送到另一个数组上，就像在 if 语句中一样，被推的数组会被销毁，因为 JavaScript 的 push 函数是破坏性的。这对 comboFinder 函数很重要，因为我们希望在达到长度 k 并被添加到 returnArray 后，temp 被清除为空值。这个函数现在只缺少一个成功的元素:返回值。我们需要在函数结束时返回 returnArray，否则什么都不会发生。

```
const comboFinder = (n, k, index = 1, temp = [], returnArray = []) => {  
  if(temp.length ===k){
    returnArray.push(temp)
    return returnArray
  }
  for(let i = index; i <= n; i++){
    let modTemp = [...temp, i]
    comboFinder(n, k, i + 1, modTemp, returnArray)
  }
  return returnArray
}
```

仅此而已。现在，我们的 comboFinder 函数可以接受范围最大值 n，并在该范围内查找长度为 k 的所有唯一组合。优雅地说，该解决方案还处理边缘情况，例如 n 为 1 或 k 为 1。

递归虽然是一种简单的解释技术，但却很难使用。本质上，递归函数只是一个调用自身的函数，但是正如我们在两个例子中看到的，它可以变得比这复杂得多。知道何时以及如何调用函数本身只能通过实践和接触递归问题来实现。有时，最好完全避免递归，因为它会引入指数级的时间复杂度。然而，在更困难的问题中，这可能比寻找解决方案所需的多个循环所带来的复杂性要小得多。递归是软件工程师工具箱中必不可少的一种有用而优雅的技术。