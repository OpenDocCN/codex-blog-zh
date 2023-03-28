# 使用 React-Hooks 和样式组件构建待办事项列表应用程序

> 原文：<https://medium.com/codex/building-a-to-do-list-app-using-react-hooks-and-styled-component-7e413a16b91e?source=collection_archive---------0----------------------->

![](img/b7437813c1a98416596950e22fd5edc1.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

在 React 16.8 中引入了钩子。它们允许通过使用功能组件来使用状态和其他 React 特性。React 中有各种类型的挂钩可用，例如**使用状态**、**使用效果**和**使用上下文**等等。对于待办事项列表项目，我们将只使用 useState 钩子。

**使用状态—** 允许向功能组件添加状态。

另一方面，Styled-component 是一个流行的库，用于对 react 应用程序进行样式化。它允许在 JavaScript 中编写实际的 CSS。你可以在[上找到更多你需要知道的关于带有 React](/@tmndungu/styled-components-in-react-all-you-need-to-know-d991d2bbf700) 的样式化组件的信息。

如果你想在 YouTube 上学习这个教程，可以点击下面的链接。

Github 上有完整的工作解决方案

[](https://github.com/tndungu/TodoListApp) [## GitHub—tndungu/TodoListApp:Todo List App 使用 React 挂钩和样式化组件。

### 使用 React 挂钩和样式组件的待办事项列表应用程序。—GitHub—tndungu/TodoListApp:Todo List App 使用 React Hooks…

github.com](https://github.com/tndungu/TodoListApp) 

# 待办事项应用

我们将要构建的待办事项应用程序将允许用户向待办事项列表中添加任务。一旦添加了任务，用户就可以在任务完成后将其标记为已完成。当您单击一项任务时，如果该任务处于待定状态，则会通过在该任务上划一条线来将其标记为已完成。将会有一个计数显示未完成和已完成的任务。

# 逐步指南

**1。创建一个反应应用**

*   **纱线**:

```
yarn create react-app todo-list
```

*   npm :

```
npx create-react-app todo-list
```

cd 进入 todo-list 和`yarn start`或`npm start`(如果使用 npm)。

**2。设计用户界面**

在 src 文件夹中，打开`App.js`,去掉中间的所有东西

标签。`App.js`文件应该如下所示:

```
import React from 'react';
import './App.css';function App() {
 return (
   <div className="App">
        Hello World! Starting a to-do List
   </div>
 );
}export default App;
```

我们将使用样式组件来设计我们的应用程序。使用以下命令安装样式化组件包。

```
npm install styled-components
```

通过在 import 部分添加以下内容，导入 App.js 中的样式化组件。

```
import styled from "styled-components";
```

我们的第一个组件将是**容器** div，使用样式化组件完成样式化。我们的应用程序现在看起来如下。

```
import React from 'react';
import './App.css';
import styled from "styled-components";const Container = styled.div`
  display: flex;
  align-items: center;
  flex-direction: column;
`;function App() {
 return (
   <Container>
   </Container>
 );
}export default App;
```

**3。完成用户界面**

包括输入、按钮、span 和 div 在内的其他组件将使用 styled-components 进行样式化，最终我们将得到如下的 UI 定义。我们的 UI 定义将在此时完成。

```
import React from 'react';
import './App.css';
import styled from "styled-components";const Container = styled.div`
  display: flex;
  align-items: center;
  flex-direction: column;
`;const Button = styled.button`
  display:inline-block;
  flex: 1;
  border: none;
  background-color: teal;
  color: white;
  height: 30px;
  width: 50px;
  border-radius: 2px;
  cursor: pointer;
`;const Text = styled.input`
  border: 2px solid #000;`;const TaskCount = styled.span`
  margin: 10px;
`;const Tasks = styled.div`
`;const TaskCount = styled.span`
  margin: 10px;
`;const Tasks = styled.div`
`;function App() {
 return (
  <Container>
      <div>
          <h2>Todo List</h2>
          <Text value={input} />
          <Button>Add</Button>
        <Tasks>
          <TaskCount>
            <b>Pending Tasks</b> 
          </TaskCount>
          <TaskCount>
            <b>Completed Tasks</b>
          </TaskCount>
        </Tasks>
        <div>
          <ul>
              /* List items consisting of tasks will be listed here */
          </ul>
        </div>
        <Button>Clear</Button>
      </div>
    </Container>
 );
}export default App;
```

**4。添加任务并标记已完成的任务**

至此，UI 完成。我们需要添加功能，使用户能够添加新的任务和标记已完成的任务。

我们将使用一个 useState()钩子来存储状态。在我们的进口中，我们增加了以下内容:

```
import React, { useState } from "react";
```

使用 useState()钩子，我们将初始化下面的状态，这些状态将存储状态并管理对变量的更改。

输入—跟踪任务用户输入的变量。

todoList —将由一个包含所有任务列表的数组组成。每个任务都被定义为一个对象，如下所示:

```
{
     id: id, //unique id
     task: Task, // a task to be added to the todo list
     complete: false, //false means task is incomplete, true means task is completed
 }
```

completedTaskCount —将跟踪已完成任务的计数。

在 App.js 的 App 函数中，我们将添加以下内容:

```
const [input, setInput] = useState("");
const [todoList, setTodoList] = useState([]);
const [completedTaskCount, setCompletedTaskCount] = useState(0);
```

在文本和按钮组件中，我们将添加事件处理函数`setInput()`和`handleClick()`。`setInput()`将设置用户任务输入的值，而`handleClick()`将任务添加到 todoList。

```
<Text value={input} onInput={(e) =>setInput(e.target.value)} />
<Button onClick={() => handleClick()}>Add</Button>
```

handleClick()函数:

```
const handleClick = () => {
  const id = todoList.length + 1;
  setTodoList((prev) => [
    ...prev,
    {
      id: id,
      task: input,
      complete: false,
    },
  ]);
  setInput("");
};
```

**5。显示任务列表**

在 **ul** 标签中，我们将定义一个 map 函数，该函数将遍历 todoList []数组并创建 **li** 列表项以供显示。每个列表项(任务)将包含属性，包括:id、complete、onClick()事件处理程序和任务。它将如下所示:

```
<ul>
  {todoList.map((todo) => {
    return (
      <LIST
        complete={todo.complete}
        id={todo.id}
        onClick={() => handleComplete(todo.id)}
        style={{
          listStyle: "none",
          textDecoration: todo.complete && "line-through",
        }}
      >
        {todo.task}
      </LIST>
    );
  })}
</ul>;
```

`textDecoration`风格将有条件地对已完成的任务应用下划线，而未完成的任务没有下划线。点击**添加**按钮，将调用`handleComplete(id)`功能，它将执行以下操作。

*   根据之前的状态，将任务对象的 complete 属性修改为 true 或 false。
*   根据 complete 属性更改为 true 还是 false，递增/递减 completedTaskCount 变量。

函数定义如下:

```
const handleComplete = (id) => {
    let list = todoList.map((task) => {
      let item = {};
      if (task.id == id) {
        if (!task.complete){
            //Task is pending, modifying it to complete and increment the count
            setCompletedTaskCount(completedTaskCount + 1);
        } 
        else {
            //Task is complete, modifying it back to pending, decrement Complete count
            setCompletedTaskCount(completedTaskCount - 1);
        }item = { ...task, complete: !task.complete };
      } else item = { ...task };return item;
    });
    setTodoList(list);
  };
```

**6。最终待办事项列表**
最终待办事项列表代码如下所示。注意，我们一直只修改了`App.js`文件。

```
import { useState } from "react";
import styled from "styled-components";
import "./index.css";const Container = styled.div`
  display: flex;
  align-items: center;
  flex-direction: column;
`;const Button = styled.button`
  display:inline-block;
  flex: 1;
  border: none;
  background-color: teal;
  color: white;
  height: 30px;
  width: 50px;
  border-radius: 2px;
  cursor: pointer;
`;const Text = styled.input`
  border: 2px solid #000;
  width: 200px;
  padding: 5px;
  border-radius: 2px;
  margin: 5px;
`;const TaskCount = styled.span`
  margin: 10px;
`;const Tasks = styled.div`
`;const LIST = styled.li`
    listStyle:"none";
    text-decoration: "line-through";
`;const App = () => {
  const [input, setInput] = useState("");
  const [completedTaskCount, setCompletedTaskCount] = useState(0);
  const [todoList, setTodoList] = useState([]);const handleClick = () => {
    const id = todoList.length + 1;
    setTodoList((prev) => [
      ...prev,
      {
        id: id,
        task: input,
        complete: false,
      }
    ]);
    setInput("");
  };
  const handleComplete = (id) => {
    let list = todoList.map((task) => {
      let item = {};
      if (task.id == id) {
        if (!task.complete){
            //Task is pending, modifying it to complete and increment the count
            setCompletedTaskCount(completedTaskCount + 1);
        } 
        else {
            //Task is complete, modifying it back to pending, decrement Complete count
            setCompletedTaskCount(completedTaskCount - 1);
        }item = { ...task, complete: !task.complete };
      } else item = { ...task };return item;
    });
    setTodoList(list);
  };return (
    <Container>
      <div>
          <h2>Todo List</h2>
          <Text value={input} onInput={(e) =>setInput(e.target.value)} />
          <Button onClick={() => handleClick()}>Add</Button>
        <Tasks>
          <TaskCount>
            <b>Pending Tasks</b> {todoList.length - completedTaskCount}
          </TaskCount>
          <TaskCount>
            <b>Completed Tasks</b> {completedTaskCount}
          </TaskCount>
        </Tasks>
        <div>
          <ul>
            {todoList.map((todo) => {
              return (
                <LIST
                  complete = {todo.complete}
                  id={todo.id}
                  onClick={() => handleComplete(todo.id)}
                  style={{
                    listStyle: "none",
                    textDecoration: todo.complete && "line-through",
                  }}
                >
                  {todo.task}
                </LIST>
              );
            })}
          </ul>
        </div>
      </div>
    </Container>
  );
};export default App;
```

# 结论

恭喜你！现在，您已经使用 React 挂钩和样式化组件构建了一个待办事项列表应用程序。您还了解了 React 和 ES6 JavaScript 的其他方面。开发起来相当简单。上面的样板文件可以用来扩展应用程序，使其包含更多的功能。如果你想了解更多，你可以做以下事情。

*   一旦任务完成，而不是删除，你可以让它消失。
*   在任务列表下方添加一个名为“ **Clear** 的按钮，点击该按钮将清除所有任务。

如果您需要进一步的帮助，请随时在下面发表评论。