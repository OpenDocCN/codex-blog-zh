# 像专家一样编写代码的 5 个有用的 JavaScript 片段

> 原文：<https://medium.com/codex/5-useful-javascript-snippets-to-code-like-a-pro-cd4874c08165?source=collection_archive---------5----------------------->

## 这里有一些日常编程问题的 JavaScript 代码片段。

![](img/a043fccbe666e4c0d1f03ea813618dc0.png)

由 freepik.com 设计

# 1.单行交换

使用破坏性方法在没有任何临时变量的情况下交换一行中的两个值。

```
let x = 1;
let y = 2;
[y, x] = [x, y];
console.log(x, y)          // prints 2 1
```

# 2.检查等于

这个代码片段将帮助您检查数组元素是相同还是不同。

```
const isEqual = arr => arr.every(val => val ===
arr[0]);
console.log(isEqual([10,10,11,11,12,13]))    // prints false
console.log(isEqual([10,10,10,10]))     // prints true
```

# 3.去掉重复的

这将帮助您从数组中删除重复项。当数组中有包含重复元素的长元素时，这很有用。

```
function duplicates(array) {
  return [...new Set(array)];
}
console.log(duplicates([1, 1, 1, 2, 4, 5, 6, 6, 7, 7])) 
// prints [1, 2, 4, 5, 6, 7]
```

# 4.获取当前时间

这段代码将获取您所在时区的当前时间。

```
var date = new Date();  
let hour = date.getHours();  
let min=  date.getMinutes();  
let sec = date.getSeconds(); 
console.log(hour + ":" + min + ":" + sec)   // prints 22:57:12
```

# 5.轻松阅读 JSON

通过使用这个代码片段，使您的 JSON 数据更加清晰易读。

```
const jso = JSON.stringify({ name: 'Ishaan', age : 19, profession: developer}, null, '\t');
console.log(jso)
// Prints 
{
 "name": "Ishaan",
 "age": 19,
 "profession": developer
}
```

# 最后的想法

我希望这篇文章对你有所帮助，并且是值得的。请随意将这篇文章分享给你的程序员朋友。

**编码快乐！**