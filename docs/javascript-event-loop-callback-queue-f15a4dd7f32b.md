# JavaScript:事件循环和回调队列解释！

> 原文：<https://medium.com/codex/javascript-event-loop-callback-queue-f15a4dd7f32b?source=collection_archive---------0----------------------->

![](img/69d3d42642b2df35ca81e8ef87ee3c40.png)

克劳迪奥·施瓦兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当 JavaScript 在浏览器上运行时，以下组件开始发挥作用:

*   带有**调用栈**和**内存堆**的 JavaScript 引擎
*   **事件循环**
*   **回调队列**
*   **Web API**

## JavaScript 引擎