# 使用 React JS 创建评论框和评论发布功能

> 原文：<https://medium.com/codex/create-a-comment-box-and-comment-posting-function-using-react-js-c6004087118?source=collection_archive---------0----------------------->

![](img/051c6d0800471c830d338035b05512a1.png)

在使用 vanilla JS 完成 Instagram clone 项目后，现在我正在学习 React 的基础知识，并将我编写的 vanilla JS 代码更改为 React 代码。React 最重要的概念是**状态**和**道具**。

光是理解它们的含义和作用就费了很多周折。我甚至还没有真正适应普通的 JS，但现在我不得不学习 React，它只是在我的脑海里发生了太多的事情。

总之，这篇文章的主题是**如何使用 React** 创建一个具有评论发布功能的评论框。这个帖子只指 UI，不考虑后端。我将介绍:

*   **如何为用户输入的注释值设置状态**
*   **如何启用评论框上的“发布”按钮**
*   **如何使用状态和道具，并在评论列表中发表评论**
*   **为什么对每个评论应用唯一键很重要**

## 如何为用户输入的注释值设置状态&启用注释框中的“发布”按钮

因此，在 Instagram feeds 上，当评论输入框中没有输入任何内容时,“发布”按钮被禁用(无法点击),只有当输入内容时，按钮才会被启用并变成蓝色。请参考下面的图片。

![](img/9c8e165b6ddcf0319ed39db325d51131.png)

默认

![](img/854450d1263604099e70ae2dd92d216a.png)

启用发布按钮

让我们看看下面的代码:

```
...
class Main extends Component {constructor() {
 super(); *this*.state = {
  **commentValue: “”,**
  commentLine: [{ commentId:””, text: “”, }],
  };
}...
```

1.  我们需要从在 states 对象中设置组件的初始状态开始。我们现在在主组件上，但最终我们将通过 props 把状态传递给子组件 CommentBox。我这里用的是`class`，但是你也可以用函数。
2.  我将初始的`commentValue`(输入到 input 元素中的值)的状态设置为空，当有东西输入时，它将被改变。我们将在后面的文章中讨论`commentLine`状态。

```
handleCommentValue = (e) => {*this*.setState({
  commentValue: e.target.value,
 });
};
```

3.现在，我们需要将`commentValue`的状态改为用户在输入元素中输入的内容。在这种情况下，我们需要一个事件处理函数。我将其命名为`handleCommentValue`，当`e`(事件)发生时，`setState()`函数被执行。`setState()`将状态更改为指定值`e.target.value`——用户输入的值。

```
render() {
 *return* (
  <>
  ... <CommentBox
    ***commentValue*={*this*.state.commentValue}
    *handleCommentValue*={*this*.handleCommentValue}**
    *enterCommentLine*={*this*.enterCommentLine}
    *submitCommentLine*={*this*.submitCommentLine}
  />...
</>
```

4.然后，我们需要将状态和事件处理函数传递给包含注释框元素的子组件。因为`CommentBox`是一个组件，所以可以通过写`<CommentBox />`在父组件中返回它。在标签内部，状态和事件处理函数可以像写属性一样传递，比如`commentValue={this.state.commentValue}`。属性的名称可以是您喜欢的，但是我将它们设置为与州名相同。稍后我们将回到最后两个状态(enterCommentLine 和 submitCommentLine)。

```
**class CommentBox extends Component** {render() { const { **commentValue, handleCommentValue,** 
 enterCommentLine, submitCommentLine} = *this*.props; **const enableCommentButton = () => {
  *return* (commentValue ? false : true);
}** **const changeCommentButtonStyle = () => {
  *return* (commentValue ? "comments-button-enabled" : 
  "comments-button-disabled");
}***return* ( <div *className*="comments-box">
  <input *onKeyPress*={enterCommentLine} ***value*={commentValue}**
    *id*="comments-input" ***onChange*={handleCommentValue}**
    *type*="text" *placeholder*="Add a comment..." /> <button *onClick*={submitCommentLine} *type*="submit"     
    *className*="comments-button"***id*={changeCommentButtonStyle()}**
    ***disabled*={enableCommentButton()}**>Post</button>
  </div> )}
}
```

5.现在让我们把注意力集中在加粗的代码上。从#4 开始，我们将`commentValue`和`handleCommentValue`传递给了 CommentBox 组件。为了在组件的元素中使用它们，我们需要通过编写`const { **commentValue, handleCommentValue**} = *this*.props;`将它们设置为`props`。

6.注意，`handleCommentValue`被分配给`onChange`属性，该属性跟踪用户在元素中输入的值，并将`commentValue`设置为这些值。此外，`commentValue`被分配给`value`属性，以便在我们提交/发布评论时重置输入元素。

7.在`render()`内部而在`return()`外部，声明了两个函数——从 Post 按钮元素中去除`disabled`属性的`enableCommentButton()`,以及在按钮被启用时改变按钮样式的`changeCommentsButtonStyle()`。然后，这两个函数被插入到按钮标签— `***id=*{changeCommentButtonStyle()} *disabled*={enableCommentButton()}**` 。请注意，这些函数用{}包装，您可以在这里用 JSX 语言编写 JS 语法。通过将该函数应用于`id`，我们可以使用 CSS 来应用适当的样式。

## **如何使用状态和道具，并在评论列表中发表评论**

现在，我们需要通过按 Enter 或单击 post 按钮来发布评论。我创建了一个评论组件，它代表用户提交的每个评论(`commentLine`)。但是首先，让我们继续使用父 Main 组件，然后是 CommentBox 组件。现在，父主组件处于 commentLine 状态。

```
*this*.state = {
  commentValue: “”,
  **commentLine: [{ commentId:””, text: “”, }],**
};
```

1.  注意，`commentLine`是一个数组，它包含一个对象的每个元素，该对象有`commentId`和`text`数据。我们将在下一节讨论`commentId`。

```
setCommentLine = () => {*this*.setState({
  commentLine: [
   …*this*.state.commentLine,
   { commentId: commentCounter++, text: *this*.state.commentValue }], commentValue: “”,
});
```

2.在父主组件中，我声明了设置`commentLine`状态的`setCommentLine`函数。当我们考虑这种状态时，它需要更改为输入元素中输入的任何内容。因此，`commentValue`状态被分配给`text`。在`commentLine`被发布后，它应该是空的— `commentLine: ""`。现在让我们再做两个执行`setCommentLine`的函数。

```
submitCommentLine = (e) => {
 e.preventDefault();
 *this*.setCommentLine();
};enterCommentLine = (e) => {
 *if* (e.charCode === 13) {
  *this*.setCommentLine();
 }
};
```

3.`setCommentLine`当我们通过输入或点击发布按钮发布评论时，需要执行该功能。因此，我们需要两个事件处理函数，一个用于单击按钮，另一个用于按 Enter。在每个功能内部，执行`setCommentLine`功能。然后，这两个函数再次传递给子 CommentBox 组件。

```
*...
return* ( <div *className*="comments-box">
  <input ***onKeyPress*={enterCommentLine}** *value*={commentValue}
   *id*="comments-input" *onChange*={handleCommentValue}
   *type*="text" *placeholder*="Add a comment..." /> <button ***onClick*={submitCommentLine}** *type*="submit"    
   *className*="comments-button"*id*={changeCommentButtonStyle()}
   *disabled*={enableCommentButton()}>Post</button>
 </div>)}
}
```

4.在子 CommentBox 组件中，注意`enterCommentLine`被赋给 input 元素的`onKeyPress`属性，`submitCommentLine`被赋给 button 元素的`onClick`属性。

## **为什么对每个评论应用唯一键很重要**

现在，让我们看看当用户发布评论时添加的每个评论元素。

```
**let commentCounter = 1;**class Main extends Component {constructor() {
super();***this*.state** = {
  commentValue: “”,
  commentLine: [{ **commentId:””**, text: “”, }],
  };
}....setCommentLine = () => {***this*.setState**({
 commentLine: [
  …*this*.state.commentLine,
  { **commentId: commentCounter++**, text: *this*.state.commentValue }], commentValue: “”,
});
```

1.  在我们进入子注释组件之前，让我们回忆一下`commentLine`有一个带有`commentId`和`text`属性的对象元素。`commentId`是赋予每个评论的唯一键。另外，请注意，`commentCounter`变量是在全局范围内声明的，它在`setState()`中用来给`commentId`分配一个唯一的编号。

```
class Comment extends Component {render() {
 **const { commentLine } = *this*.props;** *return* (
  <ul *className*=”comments-list”>
  {commentLine.map((val) => {*return* 
    **<li *className*=”each-comment”  
     *key*={val.commentId}>{val.text}
    </li>**
    })
   }
  </ul>
)};
}
```

2.现在我们在子评论组件上。它需要使用来自父组件的 props 返回带有数据的`li`元素。由于`commentLine`是一个包含有两个属性的对象元素的数组，我们可以使用`map()`方法来处理并返回每个属性。这里，`commendId`被分配给`li`元素的`key`，而`text`被分配给值。

那么，为什么给数组中的元素一个唯一的键很重要呢？

我们使用`key`给数组中的元素一个稳定的标识。稳定的标识有助于跟踪哪个项目被更改、添加或删除。当 React 在 DOM 节点的子节点上递归时，它同时迭代两个子节点列表，只有在有差异时才变异。这意味着，如果一个新的不同的`li` 元素进入其他具有相同值的`li`元素的开头，这可能会导致问题，因为 React 将变异每个子元素，就好像这些元素都是不同的一样。更详细的解释在这里:

[](https://reactjs.org/docs/reconciliation.html#recursing-on-children) [## 和解-反应

### React 提供了一个声明式 API，因此您不必担心每次更新时会发生什么变化。这使得…

reactjs.org](https://reactjs.org/docs/reconciliation.html#recursing-on-children) 

因此，我们需要给数组中的元素一个惟一的键，以避免应用程序的低效率和可能的问题。我使用了每个元素增加 1 的`commentCounter`，但是当组件在你的应用中被反复使用时，这不是一个理想的方法。理想情况下，key 的惟一 id 将从后端给出，或者您也可以利用生成随机数的库。