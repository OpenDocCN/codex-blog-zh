# 贪婪的阴暗面

> 原文：<https://medium.com/codex/the-dark-side-of-greed-650d3806c64d?source=collection_archive---------9----------------------->

## 如何平衡短期和长期目标

![](img/05ab605a46dc7963b7b1e0b2b85711d3.png)

[亨利设计工作室](https://unsplash.com/@henleydesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/baby-minimal-white-background-cake-smash?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

假设你正在和你的朋友玩游戏。给你一个数字列表，你可以从列表的开头或结尾选择。你的目标是最大化你的产出。在你挑选之后，轮到你的朋友从列表的开头或结尾挑选一个数字。每个玩家只能从列表的开头或结尾选择。游戏进行如下:

1.  第一个玩家从列表的开头或结尾选择一个数字。
2.  第二个玩家从列表的开头或结尾选择一个数字。
3.  每个玩家的输出是他们所选数字的总和。
4.  当列表为空时，游戏结束。

为此，我们编写了一个名为**的函数 optimize_output** 。optimize_output 函数的目标是在给定一系列数字的情况下，找到第一个玩家的最大输出。为此，该函数使用贪婪方法，通过考虑列表的第一个和最后一个元素来计算第一个玩家的最大输出，然后从列表中删除第一个玩家选择的元素。

贪婪算法是一种简单而直观的解决优化问题的方法，它包括在每一步做出局部最优选择，希望找到全局最优解。换句话说，贪婪方法旨在最大化眼前利益，而不考虑所做决策的长期后果。

```
def optimize_output(numbers):
  # initialize the sum to zero
  total_sum = 0

  # keep track of the index of the first and last elements in the list
  start_pointer = 0
  end_pointer = len(numbers) - 1
  my_turn = True

  # keep going until the list is empty
  while start_pointer <= end_pointer:
    # if the first element is larger, select it
    if numbers[start_pointer] > numbers[end_pointer]:
      selected_value = numbers[start_pointer]
      start_pointer += 1
    # otherwise, select the last element
    else:
      selected_value = numbers[end_pointer]
      end_pointer -= 1

    if my_turn:
        total_sum += selected_value

    my_turn = not my_turn

  return total_sum
```

## 利益

贪婪算法的主要优点之一是它的简单和易于实现。设计和实现贪婪算法几乎不需要什么努力，而且在实践中经常会产生好的结果。此外，贪婪方法可用于解决各种各样的优化问题，包括组合优化、调度和路由中的问题。

贪婪算法相对容易理解和实现。因为它们涉及到在每一步做出局部最优的选择，它们背后的逻辑通常是简单易懂的。这使得针对特定问题设计和实现贪婪算法变得相对容易。

贪婪算法的另一个好处是它们可以非常高效。在许多情况下，贪婪算法能够在比其他算法更短的时间内找到问题的解决方案。这对于关注速度的问题尤其有用，例如实时处理大量数据时。它们通常很容易修改和适应新问题。因为它们涉及做出局部最优的选择，所以修改贪婪算法来处理不同类型的输入数据或针对不同的目标进行优化通常是很简单的。

## 限制

然而，贪婪方法有一些限制，并不总是产生最优解。贪婪算法的主要缺点之一是，它容易在中间步骤做出次优选择，这可能导致次优的整体解决方案。发生这种情况是因为贪婪的方法只考虑眼前的收益，而不考虑未来的选择会如何影响整体结果。

贪婪算法的一个局限是它们可能不总是找到最优解。例如，考虑寻找从一个节点到另一个节点的最短路径的问题。贪婪算法可能通过总是选择离目的地最近的邻居节点来在每一步做出局部最优选择。然而，这种策略并不总是导致最短的总路径。在某些情况下，可能需要在一些中间步骤中走较长的路径，以便最终达到较短的整体解决方案。

贪婪算法的另一个限制是它们可能不适用于所有问题。为了使贪婪算法有效，问题必须具有“贪婪特性”，这意味着算法做出的局部最优选择将导致全局最优解。如果一个问题没有这个属性，贪婪算法可能不会产生正确的解决方案。

**optimize_output** 的方法时间复杂度为 O(n)，比[递归解决方案](/codex/mastering-recursion-a-guide-to-solving-optimization-problems-f592ee313795)更有效。然而，它可能不总是找到全局最优解，因为它是基于做出局部最优选择，而不是一次性考虑整个问题。

## 例子

假设我们有下面的数字列表:**【5，6，9，7】**

如果我们用贪婪的方法，第一个玩家会选择数字 7，第二个玩家会选择数字 9。第一个玩家的最大输出是 **7 + 6 = 13** 。

然而，如果第一个玩家选择数字 5，第二个玩家选择数字 7，第一个玩家的最大输出将是 **5 + 9 = 14** 。这是比使用贪婪方法获得的结果更好的结果。

发生这种情况是因为贪婪的方法只考虑眼前的收益，而不考虑未来的选择会如何影响整体结果。在这种情况下，贪婪的方法会犯错误，首先选择较大的元素，而更好的策略是首先选择较小的元素，以便将较大的元素留给第二个玩家。

总之， **optimize_output** 函数是一个简单直观的贪婪算法，易于设计和实现，并且在实践中经常产生良好的结果。然而，它容易在中间步骤做出次优选择，并不总是产生最优解。因此，必须仔细考虑此函数中使用的贪婪方法的局限性，并使用其他算法或方法(如果它们更适合于手头的问题)。

**通过玩 Numbrail 了解更多关于优化的知识**

[Numbrail](https://c212.net/c/link/?t=0&l=en&o=3734426-1&h=2372476900&u=http%3A%2F%2Fnumbrail.com%2Funlimited&a=Numbrail) 是一个优化难题，挑战玩家使用战略思维和优化技能在结构化的游戏环境中收集 61 分。该游戏旨在激发人们对计算机科学的兴趣，并提醒玩家优化是日常生活的重要组成部分。Numbrail 最初是一个技术面试问题，现已发展成为一个复杂而具有挑战性的游戏。