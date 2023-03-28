# 掌握递归:解决最优化问题指南

> 原文：<https://medium.com/codex/mastering-recursion-a-guide-to-solving-optimization-problems-f592ee313795?source=collection_archive---------10----------------------->

## 用递归有效解决问题:博弈论中的一个案例研究

![](img/15222e0bbc9bbdca7b5c8d3a7de21c2a.png)

由 [Unsplash](https://unsplash.com/s/photos/optimization?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [NisonCo PR 和 SEO](https://unsplash.com/@nisoncoprseo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

想象你正在和一个朋友玩游戏，你们轮流从一个列表中挑选数字。每回合，你可以从列表的开头或结尾选择一个数字，该数字将从列表中删除。游戏继续进行，直到列表为空。你的目标是最大化你选择的数字的总和。

为了解决这个问题，我们可以使用一种叫做“递归”的技术。递归是一种解决问题的方法，它把问题分解成越来越小的部分，直到它变得足够简单，可以直接解决。

递归是一种编程技术，涉及根据函数本身定义函数。它可以是解决优化问题的强大工具，因为它允许创建算法，将问题分解为更小的子问题，解决这些子问题，然后组合子问题的解决方案，以找到原始问题的解决方案。这个过程可以重复，直到问题小到可以轻松解决。递归可能是解决优化问题的有效方法，因为它允许代码的重用，并且通常可以得到更简洁和可读的解决方案。然而，确保递归在某个点终止是很重要的，因为无限递归会导致运行时错误和其他问题。

在这种情况下，我们可以使用递归来考虑这两个选项(从列表的开头或结尾挑选)，并选择导致最大总得分的一个。为此，我们编写了一个名为 **optimize_output** 的函数，该函数将一系列数字作为输入，并返回通过优化选择数字所能获得的最高总分。

optimize_output 函数考虑了三种不同的情况:

1.  如果列表只有一个元素，我们只需返回该元素。
2.  如果列表有两个元素，我们返回两个中的最大值。
3.  如果列表有两个以上的元素，我们需要考虑两个选项:从列表的开头或结尾选取。为此，我们使用对 optimize_output 的递归调用来计算这两个选项的最佳值。然后我们返回两者中的最大值。

```
def optimize_output(numbers):
  # base case: if the list has only one element, return it
  if len(numbers) == 1:
    return numbers[0]

  # if the list has two elements, return the maximum of the two
  if len(numbers) == 2:
    return max(numbers[0], numbers[1])

  # if the list has more than two elements, we need to consider both
  # options: picking from the beginning or the end of the list
  # we will use a recursive call to compute the optimal value for both
  # options and return the maximum of the two
  return max(numbers[0] + min(optimize_output(numbers[2:]), optimize_output(numbers[1:-1])),
             numbers[-1] + min(optimize_output(numbers[1:-1]), optimize_output(numbers[:-2])))
```

使用这种方法，我们可以通过对 optimize_output 进行一系列递归调用来解决问题，从最初的数字列表开始。该函数将继续进行递归调用，直到它到达一个基本情况(当列表只有一个或两个元素时)，此时它将返回最佳值。

为了更容易使用 optimize_output 函数，我们可以编写第二个名为 **pick_number** 的函数，它将一个数字列表作为输入，并返回应该选择的数字(从列表的开头或结尾)。pick_number 函数的工作方式是使用 optimize_output 函数计算两个选项的最佳值(从列表的开头或结尾进行挑选)，然后返回导致最大总得分的选项。

```
def pick_number(numbers):
  # base case: if the list has only one element, return it
  if len(numbers) == 1:
    return "BEGINNING"

  # if the list has two elements, return the maximum of the two
  if len(numbers) == 2:
    return "BEGINNING" if numbers[0] > numbers[1] else "END"

  # compute the optimal value for both options: picking from the beginning
  # or the end of the list
  beginning_option = numbers[0] + min(optimize_output(numbers[2:]), optimize_output(numbers[1:-1]))
  end_option = numbers[-1] + min(optimize_output(numbers[1:-1]), optimize_output(numbers[:-2]))

  # return the option that leads to the maximum total score
  if beginning_option > end_option:
    return "BEGINNING"
  else:
    return "END"
```

例如，考虑列表[5，6，9，7]。如果我们用这个列表作为输入来调用 pick_number，它将计算两个选项的最优值:从列表的开头选择数字 5，或者从列表的结尾选择数字 7。

在这种情况下，开始选项的最佳值是 5 +子列表[6，9]的最大最佳值。你的朋友会选择 7 个来最大化他们的机会。结果等于 **5 + 9 = 14** 。end 选项的最佳值是 7 +子列表[5，6]的最佳值的最大值。你的朋友会选择 7 个来最大化他们的机会。结果等于 **7 + 6 = 13** 。

由于开始选项的最佳值(14)大于结束选项的最佳值(13)，pick_number 将返回数字 5(开始)。这是最佳选择，因为它会在所有可能的选择中产生最高的总分(14)。

使用这种方法的好处之一是理解和实现起来相对简单。它也是高效的，因为递归调用能够重用先前计算的结果，这节省了时间并避免了不必要的计算。

然而，这种方法也有一些缺点。一个潜在的问题是，如果数字列表非常大，该函数可能需要很长时间才能运行，因为它需要进行许多递归调用。此外，这种方法需要大量内存来存储递归调用的中间结果，如果数字列表非常大，这可能会是一个问题。

总的来说，optimize_output 和 pick_number 函数为从列表中选择数字以最大化总分的问题提供了一个简单而有效的解决方案。虽然它们有一些限制，但它们是解决这类问题的有用工具。

**通过使用 Numbrail 了解更多关于优化的信息**

[Numbrail](https://c212.net/c/link/?t=0&l=en&o=3734426-1&h=2372476900&u=http%3A%2F%2Fnumbrail.com%2Funlimited&a=Numbrail) 是一个优化难题，挑战玩家在结构化的游戏环境中使用战略思维和优化技巧收集 61 分。该游戏旨在激发人们对计算机科学的兴趣，并提醒玩家优化是日常生活的重要组成部分。Numbrail 最初是一个技术面试问题，现已发展成为一个复杂而具有挑战性的游戏。