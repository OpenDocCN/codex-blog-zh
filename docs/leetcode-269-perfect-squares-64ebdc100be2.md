# Leetcode [269]。完美的正方形

> 原文：<https://medium.com/codex/leetcode-269-perfect-squares-64ebdc100be2?source=collection_archive---------10----------------------->

## 今日报价

> 石头是被锤子的最后一击打碎的，这并不意味着第一击是无用的。

![](img/e4b5df25607cca6a0c5ebded598adadd.png)

照片由[莫里茨·门格斯](https://unsplash.com/@mphotographym?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你对问题不感兴趣，直接跳到最后。

## 问题描述

给定一个整数`n`，返回*与*和`n`之和的最小的完美平方数。

**完美平方**是整数的平方；换句话说，它是某个整数与自身的乘积。例如，`1`、`4`、`9`和`16`是完美的正方形，而`3`和`11`不是。

**例 1:**

```
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```

**例 2:**

```
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

## 解决办法

```
class Solution {

    int memo[];

    public int numSquares(int n) {

        memo = new int[n+1];
        Arrays.fill(memo, -1);

        return solveUtil(n);
    }

    public int solveUtil(int n) {

        if(n <= 0) return 0;

        if(memo[n] != -1) return memo[n];

        int ans = Integer.MAX_VALUE;
        for(int i=1;i*i<=n;i++) {
            ans = Math.min(ans, 1 + solveUtil(n-(i*i)));
        }

        return memo[n] = ans;
    }
}
```

让我们忽略记忆化部分，它只是捕捉中间结果，这样我们就不需要重新计算。

假设 n = 6

如果我们试图只从 1 开始

6 = 1*1 + 1*1 + 1*1 + 1*1 + 1*1 + 1*1 ( 6 )

如果我们试图只从 1 和 2 开始

6 = 1*1 + 1*1 + 2*2 ( 3 )

我们不能从 3 开始构建，因为 3*3 = 9 大于 6。

我们可以说每一步的成本是 1(这很重要)。

现在，我的主要目标是将这些代码转换成迭代解决方案。

## 迭代解

```
class Solution {

    public int numSquares(int n) {

        int memo[] = new int[n+1];
        Arrays.fill(memo, Integer.MAX_VALUE);

        memo[0] = 0; // base condition

        for(int i=1;i<=n;i++) { // recursive call
            for(int j=1;j*j<=i;j++) { // inner loop
                memo[i] = Math.min(
                               memo[i],
                               1 + memo[i-(j*j)] // subrecursive call
                           );
            }
        }

        return memo[n];
    }
}
```

我希望你能够映射递归代码和迭代代码。

为了练习，我在这里给出了更多的问题，你可以试试

[](https://leetcode.com/problems/minimum-cost-for-tickets/) [## 最低票价- LeetCode

### 你已经提前一年计划了火车旅行。你将旅行的一年中的日子被给定为…

leetcode.com](https://leetcode.com/problems/minimum-cost-for-tickets/) [](https://leetcode.com/problems/coin-change/) [## 硬币兑换-电子代码

### 给你一个整数数组硬币代表不同面额的硬币和一个整数数量代表一个…

leetcode.com](https://leetcode.com/problems/coin-change/) [](https://leetcode.com/problems/coin-change-ii/) [## 硬币零钱 II - LeetCode

### 给你一个整数数组硬币代表不同面额的硬币和一个整数数量代表一个…

leetcode.com](https://leetcode.com/problems/coin-change-ii/) 

在即将到来的博客中，我也将发布这些问题的解决方案，请保持联系。

如果你喜欢这个博客，请投赞成票，这将激励我写更多的东西，虽然我没有从中赚钱，但不要担心，它给了我一种快乐的感觉，我正在为社区做一些有益的事情。

如果你想发表一些其他主题的博客，请在评论中告诉我我能做得更好。

## 动机部分

生活就是学习新事物和享受生活，不要等待幸福的到来，而是让每一刻都充满乐趣。

## 最后的想法

你不是被你的工作定义的，永远记住这一点。

谢谢大家(来自一个内向的男生)！！！快乐编码，坚持学习到那时候。

## 让我们连接

https://www.linkedin.com/in/vishal7663/

https://twitter.com/Vikasss_7663