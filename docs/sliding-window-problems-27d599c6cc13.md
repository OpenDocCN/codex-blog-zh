# JavaScript 中的滑动窗口问题

> 原文：<https://medium.com/codex/sliding-window-problems-27d599c6cc13?source=collection_archive---------2----------------------->

![](img/418564a91d672af491237c30de85e334.png)

我最近从 Flatiron School 软件工程训练营毕业，并通过一种更结构化的数据结构和算法方法扩展了我的技能。这似乎是拥有传统计算机科学学位的学生比 bootcamp 毕业生更具竞争力的最大知识领域，所以我决定就我正在学习的结构、算法和技术开设一个博客系列。上周，我和其他熨斗毕业生一起开始探索滑动窗口问题。

在其最基本的形式中，滑动窗口技术包括在数据集合中移动一定大小的“窗口”,如数组或字符串，并在窗口的每个“停止点”收集信息。窗口的大小可以是固定的或可变的，其中窗口基于一组规则“伸展”或“收缩”。滑动窗口可以在各种算法中使用，以解决许多类型的问题。在这里，我讨论了一个涉及固定窗口技术的基本问题，以及另一个涉及可变窗口技术的问题。

# 固定窗户

固定窗口问题通常会有一个问题中规定的大小，例如，“找出下列数组中三个连续数字的最大和。”窗口大小为三，操作将是求和与比较。首先，我将声明一个变量用于存储最高总和，另一个变量用于存储当前总和。

```
// in myFile.jsconst maxSumOfThree = arrayInput = {
  let highestSum;
  let currentSum;
}
```

两个和都需要是“let”变量而不是“const”变量，因为它们的基本值是要改变的。一个简单的强力解决方案是在数组中移动窗口，每次都完整地重新生成 currentSum 变量。要明确的是，即使在这个问题中有一个“窗口”,这也不被认为是滑动窗口技术的使用。

*蛮力解决*

```
// in myFile.jsconst maxSumOfThree = arrayInput => {
  let highestSum = -Infinity
  let currentSum for(let i = 0; i < arrayInput.length - 3; i++){
    currentSum = 0;

    for(let j = i; j < i + 3; j++){
      currentSum += arrayInput[j]
    } highestSum = Math.max(highestSum, currentSum)
  }
  return highestSum
};
```

这个解决方案可以工作，但是仔细观察就会发现这里有很多重复的地方。考虑以下输入数组示例:

```
[1,2,65,7,2,3,45,7,8,5,34,9]
```

通过上面概述的解决方案，当 i = 0 时，我们的窗口是[1，2，65]。在下一次迭代中，当 i=1 时，我们的窗口是[2，65，7]。其中两个值仍然相同，那么为什么每次都要完全重写这个窗口及其总和呢？这相当于在循环的每次迭代中构建一个窗口并打破它。相反，每次只删除“旧的”第一个值并添加“新的”最后一个值怎么样？也就是说，如何“滑动”窗户？

*稍微好一点的解决方案——滑动窗口技术*

```
// in myFile.jsconst maxSumOfThree = arrayInput => {
  const reducer = (previous, current) => previous + current
  let currentSum = arrayInput.slice(0,4).reduce(reducer)
  let highestSum = currentSum for(let i = 1; i <= arrayInput.length - 3; i++){
    currentSum -= arrayInput[i - 1]
    currentSum += arrayInput[i + 4]
    highestSum = Math.max(highestSum, currentSum)
  }
  return highestSum
};
```

为了实现这种重复性更低的解决方案，我们做了一些更改。首先，currentSum 已经初始化为数组中前三个数的和(通过 slice 和 reduce)。接下来，highestSum 被初始化为 currentSum 的第一个值。最后，for 循环已被修改为从索引 1 开始—这样就已经有一个“左手”号要删除—并且还要删除/添加结束号，而不是每次都完全重写窗口。最后的步骤仍然是相同的:比较 currentSum 和 highestSum，并输出 highestSum。

当然还有改进的空间，但是这个解决方案更好，因为它消除了嵌套的 for 循环。这是一种基本的固定大小滑动窗口技术，用于找到给定大小的子阵列的最高和。

# 可变窗口

在可变窗口问题中，问题的焦点不是在给定大小的数据输入子片段中寻找什么。相反，它通常专注于寻找满足给定条件的窗口大小。

```
terminal $ const myArray = [7,4,2,1,8,9,5,2,5]
```

考虑 myArray，如上面的代码片段中所定义的，我们可能会遇到这样的问题:“数组中两个重复值之间的最小索引差是多少？”人类可以很容易地看到这个数组，看到 2 和 5 是唯一重复的值，并计算出最接近的数字 5 的两个实例之间的索引差是(8–6)= 2。但是如何通过编程来执行这个操作呢？

```
// in myFile.jsconst smallestRepeatWindow = arrayInput => {
  let smallestWindow = Number.MAX_SAFE_INTEGER
  let window = []
  let left = 0;
}
```

首先，考虑问题的形状，为最小窗口大小、当前窗口内容以及当前窗口的“左”和“右”索引初始化一些跟踪器变量可能是个好主意。接下来，我想找到一种方法来识别当前窗口中的重复。

```
// in myFile.jsconst smallestRepeatWindow = arrayInput => {
  let smallestWindow = Number.MAX_SAFE_INTEGER
  let window = []
  let left = 0; for(let right=0; right<arrayInput.length; right  ++){
    window.push(arrayInput[rightBound]) while([...new Set(window)].length < window.length){
      if(arrayInput[left] === arrayInput[right]){
        smallestWindow = Math.min(smallestWindow, (right - left))
      }
      left++
      window.shift()
    }
  }return smallestWindow
};
```

首先，for 循环遍历数组，迭代地扩展窗口的右边界。for 循环中的 while 循环通过检查从当前窗口创建的集合(集合值必须是唯一的)的长度是否小于窗口本身来查找重复值。如果为真，条件检查左右边界索引是否具有相同的值。如果是，则检查并设置 smallestWindow(如果需要)。如果不是，则增加左边界，并从窗口中移除其先前的值。这种 for-while 组合允许窗口根据需要增长和收缩，直到找到包含重复的最小窗口。

可变窗口比固定大小的窗口技术更难编写。关于滑动窗口技术的一个很好的额外资源，请查看简单工程师的这个视频。

滑动窗口技术在各种各样的编程问题中都有应用，并且作为一个技术面试问题非常常见。对于滑动窗口技术的额外练习，试着在 Leetcode 上解决这些问题。