# 所有 JavaScript 数组方法

> 原文：<https://medium.com/codex/all-javascript-array-methods-473e9f6107e1?source=collection_archive---------9----------------------->

![](img/d185b31065ac0ab857401f9d7ab33495.png)

大家好，今天我们将介绍每一个内置的 JavaScript 数组方法。我还包含了 ES6(ECMA 脚本 6)方法。希望这能帮助你作为一名 JavaScript 开发人员加快你的编码之旅。

## **1。推送方式**

push()方法向数组中添加一个新元素。

```
let arr = [“Apple”, “Amazon”, “Flipkart”];arr.push(“Walmart”);
```

输出:

```
["Apple", "Amazon", "Flipkart", "Walmart"]
```

## 2.**流行方法**

pop()方法从数组中移除最后一个元素。

```
let arr = [“Apple”, “Amazon”, “Flipkart”, “Walmart”];arr.pop();
```

输出:

```
["Apple", "Amazon", "Flipkart"]
```

## 3.**换挡方法**

shift()方法移除第一个数组元素，并将所有其他元素移动到更低的索引位置。

```
let arr = [“Apple”, “Amazon”, “Flipkart”];arr.shift();
```

输出:

```
["Amazon", "Flipkart"]
```

## 4.**解移方法**

unshift()方法将一个新元素添加到数组的开头，并取消旧元素的移位。

```
let arr = [“Amazon”, “Flipkart”];arr.unshift("Apple");
```

输出:

```
["Apple", "Amazon", "Flipkart"]
```

## 5.**串联方法**

concat()方法通过合并现有数组来创建一个新数组。

```
let arr = [“Apple”, “Amazon”, “Flipkart”];arr.concat("Walmart");
```

输出:

```
["Apple", "Amazon", "Flipkart", "Walmart"]
```

## 6.**加入方法**

join()方法返回一个字符串形式的数组。它不会改变原始数组。我们可以指定任何分隔符。否则，默认为逗号(，)。

```
let arr = [“Apple”, “Amazon”, “Flipkart”, “Walmart”];let str = arr.join("-");
```

输出:

```
Apple-Amazon-Flipkart-Walmart
```

## 7.**拼接方法**

splice()方法可用于向数组中添加新项(一个或多个)。

*   第一个参数定义了应该添加新元素的位置。
*   第二个参数定义应该删除多少个元素。
*   其余的参数定义了要添加的新元素。
*   此方法返回一个包含已删除项的数组。

```
let arr = [“Apple”, “Amazon”, “Flipkart”, “Walmart”];arr.splice(2, 1, "Google"); 
```

输出:

```
["Apple", "Amazon", "Google", "Walmart"]
```

通过拼接方法返回:

```
Flipkart
```

## 8.**反向方法**

reverse()方法反转数组中元素的顺序。

```
let arr = ["Apple", "Amazon", "Google", "Walmart"];arr.reverse();
```

输出:

```
["Walmart", "Google", "Amazon", "Apple"]
```

## 9.**切片法**

您可以使用 slice()移除元素，而不会在数组中留下空洞。

*   第一个参数定义了应该添加新元素的位置。
*   第二个参数定义应该删除多少个元素。

```
let arr = ["Apple", "Amazon", "Google", "Walmart"];arr.splice(1,1);
```

输出:

```
["Apple", "Google", "Walmart"]
```

# **ES6 方法**

![](img/c4c82bb6ab308ad15d921c2e0483f102.png)

## **1。查找方法**

*   find()方法返回通过测试的第一个元素的值。
*   find()方法为每个数组元素执行一个函数。
*   如果没有找到元素，find()方法返回“undefined”。
*   find()方法不执行空元素的函数。

```
let arr = [3, 5, 7, 8];arr.find((n) => n % 2 === 0); // returns even number
```

输出:

```
8
```

## 2.**查找索引方法**

*   findIndex()方法为每个数组元素执行一个函数。
*   findIndex()方法返回通过测试的第一个元素的索引。
*   如果没有找到匹配，findIndex()方法返回-1。
*   findIndex()方法不执行空数组元素的函数。

```
let arr = [3, 5, 7, 8];arr.findIndex((n) => n % 2 != 0);
```

输出:

```
0
```

## 3.**地图方法**

*   map()通过为每个数组元素调用一个函数来创建一个新数组。
*   map()为数组中的每个元素调用一次函数。
*   map()不执行空元素的函数。

```
let arr = [3, 5, 7, 8];arr.map((n) => n*2);
```

输出:

```
[6, 10, 14, 16]
```

## 4.**过滤方法**

*   filter()方法创建一个新数组，其中填充了通过函数测试的元素。
*   filter()方法不执行空元素的函数。
*   filter()方法不改变原始数组

```
let arr = [3, 5, 7, 8];arr.filter((n) => n % 2 != 0);
```

输出:

```
[3, 5, 7]
```

## 5.**每一种方法**

*   every()方法为每个数组元素执行一个函数。
*   如果函数为所有元素返回 true，every()方法返回“t *rue* ”。
*   如果函数为一个元素返回 false，every()方法将返回“ *false* ”。
*   every()方法不执行空元素的函数。

```
let arr = [3, 5, 7, 8];arr.every((n) => n < 6); //checks if every element is less than 6
```

输出:

```
False
```

## 6.**有的方法**

*   some()方法检查是否有任何数组元素通过了测试。

```
let arr = [3, 5, 7, 8];arr.some((n) => n < 6); //checks if any element is less than 6
```

输出:

```
True
```