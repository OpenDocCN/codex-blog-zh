# 两点技术

> 原文：<https://medium.com/codex/the-two-pointer-technique-8c44b0b3890?source=collection_archive---------2----------------------->

![](img/165e81d025c2e97fc8736e05d1fde82e.png)

在我的算法和数据结构系列的第二部分中，我将解释一种在搜索算法中非常常用的技术:双指针技术。第一部分，JavaScript 中的*滑动窗口问题，不是理解本文内容的先决条件，但它涵盖了另一种对软件工程面试问题有用的常见编码模式，可以在这里阅读[。](https://hanreitz.medium.com/sliding-window-problems-27d599c6cc13)*

# 为什么是两个指针？

许多涉及存储在数组或链表中的数据的问题要求我们找到符合特定条件或标准的数据集。输入两个指针:我们可以使用单个迭代器通过蛮力来解决这些问题，但是这通常涉及到嵌套循环，这成倍地增加了解决方案的时间复杂度。相反，我们可以使用两个指针或迭代器变量来跟踪数据中子集的开始和结束，然后检查条件或标准。

# 认识两点问题

由于我目前正在加强我的数据结构和算法知识，我发现最困难的事情之一是识别何时需要使用双指针技术，如果我需要，是哪种类型。这对于其他人来说可能是显而易见的，但既然对我来说是/不是，我将在这里详细解释一下。

**可能需要两点方法的第一个线索**是该问题要求在排序数组或链表中找到一组符合特定模式或约束的元素。与未排序的数组相比，在排序的数组中有关于端点的附加信息。例如，如果一个整数数组按升序排序，我们知道数组的起始位置是最小或最负的整数，结束位置是最大或最正的整数。因此，如果一个问题出现在一个排序的数组中，那么很有可能使用双指针技术来解决这个问题，其中指针最初被赋予数组的第一个和最后一个成员的值。下面举例说明，以便进一步澄清。相比之下，在链表中，最有可能的解决方案是指针前后移动来“寻找”问题的约束或条件。

第二个线索可能需要一个双指针方法，这个问题要求在一个链表中找到、插入或删除一些东西。知道链表的确切位置或长度意味着遍历该位置之前的每个节点(如果需要长度，则遍历所有节点)。我们知道列表中的每个节点都连接到列表中的下一个节点，直到列表的末尾。在这种情况下，串联指针或快慢指针方法可能有助于解决问题。下面举例说明了快速指针和慢速指针方法。

# 示例:带有排序输入数组的两个求和问题

问题:给定一个按升序排序的数组，找出两个数字，使它们相加得到一个特定的目标。返回两个数的索引。

约束:输入数组按升序排序，并且只包含一个解。**数组是 1 索引的**。

伪代码:

```
function(array, target){
  set a left pointer to the first element of the array
  set a right pointer to the last element of the array
  loop through the array; check if left and right add to target
  sum is less than the target, increase left pointer
  sum is greater than the target, decrease right pointer
  once their sum equals the target, return their indices
}
```

伪代码说明了两个指针问题的最经典的情况，其中指针从一个排序数组的末端开始。在这种情况下，我们寻找一个数组的两个成员来添加到一个特定的目标值。下面是真正的代码，伪代码留在注释中:

```
var twoSum = function(numbers, target) { 
  // set a left pointer to the first element of the array
  let left = 1; 
  // set a right pointer to the last element of the array
  let right = numbers.length

  // loop through the array; check if left and right add to target
  while(numbers[left - 1] + numbers[right - 1] !== target){ 
    // sum is less than the target, increase left pointer
    if(numbers[left - 1] + numbers[right - 1] < target){
      left++ 
    // sum is greater than the target, decrease right pointer
    } else {
      right--
    }
  }
  // once their sum equals the target, return their indices 
  return[left, right]
};
```

为了练习这个问题或尝试不同的解决方案，[在 LeetCode 上查看](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)。

# 示例:从链接列表的末尾移除第 n 个元素

问题:写一个函数，接受一个链表的头节点和一个数字 n，然后从链表的末尾去掉第 n 个元素。

约束:n 将小于链表的长度。

伪代码:

```
function(head, n) {
  set up two pointers
  initially, both should be equal to the head
  check if the head is the only node + remove it
  iterate the second pointer n places ahead of the first
  if the second pointer is null, remove the head (because the head is the nth from the end if this is the case)
  iterate both pointers until the second pointer is the tail
  if there is a node two ahead of the first pointer, set it as the next node of the first pointer ('dropping' the nth node)
  if there is no node two ahead of the first pointer, the first pointer is now the tail and its next should be set to null
  return head
}
```

伪代码说明了这是一个两个指针的问题，迭代器一前一后地移动。一旦两个指针在链表中相距 n，它们以相同的速度移动，直到第二个指针到达尾部。以下是实际代码，带注释的伪代码:

```
var removeNthFromEnd = function(head, n) {
  // set up two pointers
  // initially, both should be equal to the head
  let left = head;
  let right = head;    // check if the head is the only node + remove it
  if(!head.next) return null // iterate the second pointer n places ahead of the first
  let i = 0;
  while(i++ < n){
      right = right.next
  } // if the second pointer is null, remove the head
  // (because the head is the nth from the end if this is the case)
  if(right === null){
      return head = head.next
  } // iterate both pointers until the second pointer is the tail
  while(right && right.next){
      right = right.next
      left = left.next
  }

  // if there is a node two ahead of the first pointer, 
  // set it as the next node of the first pointer 
  // ('dropping' the nth node)
  // if there is no node two ahead of the first pointer, 
  // the first pointer is now the tail and its next should 
  // be set to null
  left.next = left.next.next ? left.next.next : null // return head
  return head
};
```

要练习这个问题或尝试不同的解决方案，[在 LeetCode 上查看](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)。

# 示例:查找链表的中间

问题:给定一个单链表的头，找到并返回链表的中间节点。如果有两个中间节点，则返回第二个。

约束:节点的数量范围从 1 到 100，包括 1 和 100。

伪代码:

```
function(head){
  set a slow and a fast pointer to the head
  iterate the pointers through the list
  fast should move twice the speed of slow
  once fast reaches the tail, slow will be at the middle 
  if there are two middles, slow will be at the second middle
  return slow
}
```

这是一个从数学上考虑链表的简单问题。中点是到端点距离的一半，因此，如果我们有一个慢速指针以快速指针一半的速度移动，当快速指针位于尾部时，它将位于中间节点。请记住，我们可以通过下一个节点为空来定义尾节点。以下是实际代码，带注释的伪代码:

```
var middleNode = function(head) {
  // set a slow and a fast pointer to the head  
  let slow = head;
  let fast = head; // iterate the pointers through the list
  while(fast !== null && fast.next !== null){
    // fast should move twice the speed of slow
    slow = slow.next
    fast = fast.next.next
  }  // once fast reaches the tail, slow will be at the middle 
  // if there are two middles, slow will be at the second middle
  // return slow  
  return slow
};
```

要练习这个问题或尝试不同的解决方案，[请在 LeetCode 上查看](https://leetcode.com/problems/middle-of-the-linked-list/)。

在这篇文章中，作为我自己知识建设的一部分，我试图解释一种特定类型的搜索算法是如何使用双指针方法编写的。无论指针是一起开始并前后移动，采用快速和慢速方法，还是从数组的两端开始，这些方法都通过减少代码中所需的循环次数来节省时间。更多练习问题，查看 LeetCode 对该技术的简要说明和问题列表[这里](https://leetcode.com/articles/two-pointer-technique/)。对于另一个不是我的深入解释，请查看这篇[文章](https://algodaily.com/lessons/using-the-two-pointer-technique)。