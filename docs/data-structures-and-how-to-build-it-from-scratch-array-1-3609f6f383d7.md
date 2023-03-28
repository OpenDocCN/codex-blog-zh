# 数据结构和如何从头构建它(数组)#1

> 原文：<https://medium.com/codex/data-structures-and-how-to-build-it-from-scratch-array-1-3609f6f383d7?source=collection_archive---------37----------------------->

javascript 中的数组只是带有基于整数的键的对象，并且需要理解 oop 如何在 JavaScript 上工作。

我们将创建一个名为 **MyArray** 的类，它将有四个方法，分别是 **get** 、 **push** 、 **pop** 和 **delete** 。

```
Class MyArray {}
```

首先我们创建构造函数，将属性**数据**指定为对象，并将**长度** = 0。

```
constructor () {
    this.data = {};
    this.length = 0;
}
```

之后，我们将使我们的第一个方法**得到**。 **Get** 基本上是通过索引返回 **this.data** 里面的任何一项。

```
get(index) {
   return this.data[index];
}
```

接下来，我们将制作一个名为**推送**的方法。**按下**会在 **this.data** 内增加一项作为最后一项。在这个方法中，我们将把**this . data**(index = last item+1 或 this.length)设置为一个来自参数的项。不要忘记，我们还必须增加 this.length，因为我们刚刚增加了 1 项。

> *花絮:一个* ***参数*** *是一个函数定义中的变量。它是一个占位符，因此没有具体的值。一个* ***参数*** *是函数调用时传递的值。*

```
push(item) {
   this.data[this.length] = item;
   this.length++;
   return this.length;
}
```

接下来，我们将制作一个名为 **pop** 的方法。 **Pop** 是 push 的反义词。因此**弹出**将从最后一个索引中删除一个项目。如何确定 **this.data** 中任意一项的最后一个索引？嗯，很简单。我们只需要属性长度减一(因为索引从 0 开始)。所以我们可以使用 delete(内置函数)来删除该项。不要忘了我们还必须减少 this.length ，因为我们刚刚删除了 1 项。

```
pop() {
   const lastItem = this.data[this.length - 1];
   delete this.data[this.length - 1];
   this.length--;
   return lastItem;
}
```

最后，最后一个方法是**删除**。因此 **delete** 方法将删除具有特定索引的任何项目

```
delete(index) {
    const item = this.data[index];
    for (let i = index; i < this.length - 1; i++) {
       this.data[i] = this.data[i + 1];
    }
    delete this.data[this.length - 1];
    this.length--;

    return item;
}
```

这对你来说可能有点困惑，但是别担心，我会解释的。所以在 for 循环中，我们基本上用 i + 1 的数据替换 I 的数据(在索引之后)。例如，我们有["零"、"一"、"二"、"三"、"四]]，我们使用我们的**删除**方法(假设 index = 2)..

所以会是这样的

```
existing data
[“zero”, “one”, “two”, “three”, “four”]
   0       1     2       3       4first iterate (index = 2, i = 2)
["zero", "one", "three", "three", "four"]
    0      1       2        3       4
second iterate (index = 3, i = 3)
["zero", "one", "three", "four", "four"]
   0      1       2        3      4 our for loop (i = 4) is over because i === this.length - 1 or i < this.length - 1;
```

你可能会注意到有些不对劲。是的，我们仍然有索引= 4 的“4”。这就是为什么我们需要删除 this.data[this.length-1]并对 this.length 进行减量。

```
our final data after delete index = 2 (item "two").
["zero", "one", "three", "four"]
```

所以，就这样了。我们刚刚用类制作了自己的数组。这是 MyArray 类的完整代码。

```
Class MyArray {constructor() {
    this.data = {};
    this.length = 0;
}get(index){
   return this.data[index];
}push(item) {
   this.data[this.length] = item;
   this.length++;
   return this.length;
}pop() {
   const lastItem = this.data[this.length - 1];
   delete this.data[this.length - 1];
   this.length--;
   return lastItem;
}delete(index) {
     const item = this.data[index];
     for (let i = index; i < this.length - 1; i++) {
        this.data[i] = this.data[i + 1];
     }
     delete this.data[this.length - 1];
     this.length--;
     return item;
  }
}const myArray = new MyArray();
myArray.push("zero");
myArray.push("one");
myArray.push("two");
myArray.push("three");
myArray.push("four");
```

我们将在[数据结构和如何从头开始构建(散列表)#2](https://singgihaji.medium.com/data-structures-and-how-to-build-it-from-scratch-hash-table-2-95158a7883fc) 上再见！