# 用普通的 JavaScript 进行拖放

> 原文：<https://medium.com/codex/drag-n-drop-with-vanilla-javascript-75f9c396ecd?source=collection_archive---------4----------------------->

## 简单的实际开发和示例

尽管听起来很可怕，JavaScript 中的拖放功能并没有那么难。如果您对这种语言和文档对象模型(DOM)有基本的了解，那么本教程自然会向您走来。

![](img/93a5565beea5bbd6f5a8afb5bd3d8e1e.png)

肯尼·埃利亚松在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 入门指南

让我们从一些样板 HTML 代码开始，我们将在整个会议中使用。

```
<div class="container">
  <div class="draggable">I love HTML</div>
  <div class="draggable">CSS is fun</div>
</div>
<div class="container">
  <div class="draggable">JS is flaky</div>
</div>
```

你可以随心所欲地设计这个。基本的想法是有两个独立的容器，其中有几个您希望拖动的子元素。

# DnD 的有利因素

注意: DnD 是“拖放”的简称。

默认情况下，元素不能自己拖放——废话。所以我们需要给每个元素附加一个属性。

```
<div class="container">
  <div class="draggable" **draggable="true"**>I love HTML</div>
  <div class="draggable" **draggable="true"**>CSS is fun</div>
</div>
<div class="container">
  <div class="draggable" **draggable="true"**>JS is flaky</div>
</div>
```

简单。我们现在已经告诉浏览器为拖放功能准备各自的元素。

# 可拖动元素上的触发事件

那么当元素开始拖动时应该执行什么代码呢？当用户放下元素时应该调用什么函数？我们用两个简单的事件监听器— `dragstart`和`dragend`配置可拖动元素。

```
// script.jsconst draggables = document.querySelectorAll(".draggable")
draggables.forEach(draggable => {
  draggable.addEventListener("**dragstart**", function() {
    this.classList.add("dragging")
  }) draggable.addEventListener("**dragend**", function() {
    this.classList.remove("dragging")
  })
}
```

请注意，当用户正在拖动时，我是如何切换类名的。你可以使用 CSS 中的`.draggable.dragging`选择器自由改变元素的样式。

# 将元素放入容器中

现在，我们只能拖动元素，但不知道元素被放在哪里。我们现在需要在容器上集成这个功能。

```
const containers = document.querySelectorAll(".container")
containers.forEach(container => {
  container.addEventListener("**dragover**", function() {
    const draggedElement = document.querySelector(".dragging")
    this.appendChild(draggedElement)
  })
})
```

当当前有一个元素被拖动到容器上时，触发`dragover`函数。因为`draggedElement`在 DOM 中只能有一个实例，所以对象先前的位置被删除。

# 知道将元素放在哪里

现在我们成功地将对象从一个地方迁移到另一个地方，我们只能将对象附加到容器的末尾；我们不一定希望如此。为了将对象放在两个不同对象的中间，我们需要计算元素的相对位置，并确定将子节点附加到容器中的什么位置。

```
const getDragAfterElement = (container, y) => {
  const notDraggedCards =
    [...container.querySelectorAll("**.card:not(.dragging)**")]

  return notDraggedCards.reduce((closest, child) => {
    const box = **child.getBoundingClientRect()**
    const offset = y - box.top - box.height / 2 if (offset < 0 && offset > closest.offset) {
      return { offset, element: child }
    } else return closest
  }, { offset: Number.NEGATIVE_INFINITY }).element
}
```

让我们一行一行地分解这个函数。

1.  我们正在检索当前没有被**拖动的所有卡片。**
2.  我们得到每个孩子的约束。
3.  利用指针的 y 值，我们测量从每个盒子的*水平中心*的距离。
4.  我们返回负的和最接近零的元素。

现在我们知道了我们的`.dragging`在哪个元素的上面，我们可以将这个节点插入到容器中我们想要它的地方。

```
container.addEventListener("dragover", function(**e**) {
  const draggedElement = document.querySelector(".dragging")
  const afterElement = **getDragAfterElement**(container, e.clientY) if (afterElement == undefined)
    this.appendChild(draggedElement) // add to the end
  else
    this.**insertBefore**(draggedElement, afterElement)
}
```

瞧啊。我们已经非常容易地实现了 DnD 特性。

# 结论

有时你可能认为一个特性很难实现，直到你学会如何去做。这段代码有很多改进，你可以即兴发挥。尝试各种风格，制作元素动画，与 API 交互，等等。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

灵感来自[来自网络开发简化版](https://www.youtube.com/c/WebDevSimplified)的凯尔。

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```