# 使用 Jest 单元测试成功测试 map 函数

> 原文：<https://medium.com/codex/using-jest-unit-tests-to-successfully-test-map-functions-95f2abc544d?source=collection_archive---------1----------------------->

![](img/69c18ad69d4281256f1588253852a4a2.png)

我知道这附近某处有山丘…照片**由** [**棉布罗**](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) **从** [**像素**](https://www.pexels.com/photo/sea-landscape-nature-man-9909207/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

## 今天我们将演示 map 是如何工作的，以及以某种方式实现 map 是如何破坏测试的。在下面的代码示例中，有两种方法可以使用 map 来做同样的事情。一个将一个*函数传递给一个映射*(即。使用相当普遍的…