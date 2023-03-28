# Ruby 中的数据结构和算法:二叉树的深度优先搜索(有序)

> 原文：<https://medium.com/codex/data-structures-algorithms-in-ruby-depth-first-search-in-order-for-binary-trees-6074bc1b9d78?source=collection_archive---------3----------------------->

我们现在来看二叉查找树的三种主要深度优先搜索(DFS)的最后一种:有序遍历。就我而言，DFS In-Order 可能是最有用的，因为返回值是按照从最低值到最高值的顺序排列的。

首先，非常简要地回顾一下我们到目前为止对二叉查找树遍历所做的介绍:

二叉查找树是一种数据结构，其中每个节点最多有两个子节点(因此…