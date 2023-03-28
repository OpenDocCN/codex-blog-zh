# 嘿，别碰那些！-应用状态和功能纯度

> 原文：<https://medium.com/codex/hey-leave-those-alone-application-state-and-function-purity-d64c285a4703?source=collection_archive---------25----------------------->

![](img/b863349666981dd4ea1b73fb789dcc29.png)

照片由来自 [Pexels](https://www.pexels.com/photo/clear-glass-bottle-on-table-544112/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Tirachard Kumtanom](https://www.pexels.com/@tirachard-kumtanom-112571?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

你有没有遇到过这样的错误，你没有得到一个错误记录到控制台，或者你没有一个大的错误出现在你的应用程序的屏幕上

> “你好，你在 index.js 文件的第 71 行搞砸了…”

相反，你看到没有错误屏幕，没有错误日志，应用程序运行正常，但你刚刚意识到你所有的交付被发送到错误的地址，或者有一个奇怪的 NGN 100 被添加到所有客户的订单总数？

**应用状态和副作用**

应用程序状态是指应用程序中已分配的变量的当前条件和值。所有被赋值的变量都被分配到内存中的一个小槽中(顺便说一下，在一个叫做**堆**的小地方)，它们一直在那里，直到垃圾收集发生(这在 JavaScript 中是自动处理的)。

副作用是当我们的函数修改超出其自身范围的状态时，我们在应用程序中引起的变化。这实际上是告诉我们，当我们编写影响应用程序运行状态的代码时，我们会产生副作用。考虑下面的代码块

```
var dayOfMonth = 21 // A CONSTANT THAT HOLDS A VALUE, IN THIS INSTANCE LET US CALL IT THE DAYOFMONTHvar logOutAndIncrementDay // A FUNCTION THAT LOGS A USER OUT AND INCREMENTS THE DATE TO BE READY TO LOG IN TOMORROW (let us assume our program allows us log in once in a day)var payStaffDailyWage // A FUNCTION THAT PAYS EACH STAFF'S STIPEND DAILYvar incrementDate = () => dayOfMonth++;//assuming logOutAndIncrementDay and payStaffDailyWage are implemented as followslogOutAndIncrementDay = function (user_id) { return getUser(user_id).
        then(logUserOut).
        then(incrementDate).
        catch(handleError);  //PSEUDOCODE};payStaffDailyWage = function (date) { console.log(date);        // THIS IS THE DAY OF MONTH}And then we go on to call our procedures as so;logOutAndIncrementDate(user_id);payStaffDailyWage(dayOfMonth)         // 22 
```

现在，我们可以清楚地看到，与我们使用相同代码库和相同变量的其他人现在面临着代码中不可预测行为的风险，因为输入现在与预期不同，这都要归功于我们在 logOutAndIncrementDate 过程中增加了 date 变量。

副作用有各种形式，我们通常把函数式程序员看作是把副作用减少到几乎为零。副作用是无法避免的，因为我们的程序运行带来的副作用是 CPU ram 被吃掉，CPU 甚至产生热量。副作用肯定会发生，因为应用程序依赖于状态变化才是有用的，否则应用程序将什么也不做，这可能不是很好。

副作用包括但不限于:

```
1\. Changes in State like our logOutAndIncrementDate procedure2\. Network Calls3\. I/O Operations (Disk Writes, File Writes, etc)4\. Database Operations5\. DOM Mutations among others
```

必须保留应用程序状态，并且只有我们知道的更改对我们的应用程序有帮助。未跟踪的变更只是一个巨大的干草堆，让 bug 可以隐藏并在其中茁壮成长。

好吧…那我为什么要在乎呢？

我喜欢你问这个问题。我们非常关心结果，这里有一些结果，当你开始使用纯函数时，你马上就能看到。

```
1\. Pure functions are easy to understand, maintain and debug2\. Pure functions can be relied upon, and we can go to sleep without worrying about edge cases that may cause our programs to behave unpredictably, because all changes are structured.3\. Pure functions are easy to test because they compute their output based on given inputs, so we can easily make assertions on them.4\. Pure functions can be **memoized**. A pure function can be replaced by its return value, due to something cool called **REFERENTIAL TRANSPARENCY**. This means the functions execution has no external footprints or effects on state, hence we can skip executing it altogether and just plug in its return value, and our app should still work fine!
```

**那么，什么时候一个函数被描述为纯的呢？**

功能纯度是一个相对的课题。在这个系列中，我们将检查代码块，这些代码块将指导我们理解在编写纯函数时要注意什么，并帮助介绍我们编写代码的函数方法。

一个函数要被描述为纯函数，它的定义和执行必须满足一些标准。

```
1\. INPUT AND OUTPUT: A pure function must receive arguments, and compute the result BASED ON the arguments received, and return an output.a. What this means is that a pure function will calculate its return value using only values that are passed in as arguments. A pure function will **NOT REACH OUT OF ITS SCOPE**, to access or reassign some **FREE VARIABLE** (variables that are floating outside the functions scope)Consider the following block of codevar c = 4;var add = function (a,b) { return a + b + c; // ACCESSES A FREE VARIABLE “c”, BAD}2\. CONSISTENT RESULTS: A pure function must return the same output every time it is fed the same inputs.a. This tells us that so long as inputs don’t change, the return value of a pure function must be the same. Remember how we touched on referential transparency? A pure function returns the same input, which is why we can comfortably replace it with its return value and not have our applications explode (like Michael Bay does it).Consider the following block of codevar generateID = () => { return Math.random(); // OUTPUT DOES NOT CORRELATE TO INPUT, NEITHER WILL OUTPUT REMAIN THE SAME ACROSS ALL EXECUTIONS OF THIS FUNCTION, ALSO TAKES NO INPUT}3\. SIDE EFFECTS: A pure function must not cause any observable side effect, as has been explained earlier. If a side effect is present, then the code is by definition, impure. TRIVIA: Consider the function below, would you describe this function as pure?var giveMeNull = () => { 
    return null; 
}
```

**功能纯度、强制性杂质和表面积**

副作用不可避免。这一点已经说得很清楚了，我们作为程序员也明白这一点。我们的代码将总是导致状态的变化是有用的，这显然总是一个副作用。数据库写入发生，DOM 变异以呈现新数据，CPU 在我们工作时消耗 RAM，等等。这是我们作为程序员必须适应的事情，因为我们在代码库中找到了绕过强制性杂质的方法。

那么，作为函数式程序员，我们如何与代码中的副作用共生共存呢？

请记住，我们可以使用一些技术来减少杂质在代码库中游荡的情况。我们今天要考虑的其中一个概念是，如果我们不能完全去除杂质，那么就去除杂质，减少杂质在我们函数中出现的表面积。抽象是面向对象编程的支柱，如果你不知道 OOP 的支柱，一个好的读物是 [**如何向一个六岁的孩子解释面向对象编程的概念**](https://www.freecodecamp.org/news/object-oriented-programming-concepts-21bb035f7260/)

抽象不一定隐藏代码。然而，它确实允许我们在代码中执行关注点分离。这意味着，当我们编写一个函数时，我们需要理解我们可以将杂质抽象到代码库中的一个临时“容器”中，这样我们就可以从一个点管理它们。

**YADDI，YADDI，YADDA……让我看看你在练习中所说的内容**

好吧，好吧！既然你不肯放手，我们来检查一些代码，看看如何重构我们的代码，抽象出杂质，给我们可怜的函数(双关语)一些喘息的空间！

```
var getUserAndRenderFullname = async (user_id) => { let user = await getNetwork.call(user_id); // PSEUDOCODE if(!user) return null;    let username = user[username]; let myH1 = document.createElement(‘h1’); myH1.innerText = username; let namespace = document.querySelector(‘#name_h1’); //ACCESSES  THE DOM namespace.append(myH1); return null;}
```

上面的代码块有问题。一些已经在代码注释中指出，还有一些。

关注点不分离。函数式编程教导我们从“函数作为组件”的角度来处理程序，其中逻辑的每一小步都由一个单独的函数来处理，赋予每个函数一个单独的职责。

我们的 getUserAndRenderFullname 函数至少可以分成两个独立的函数。我们可以有一个获取用户的函数 getUser，还有另一个将用户名附加到 DOM 的函数，我们可以更进一步，将函数去专门化，只将任何文本附加到 DOM！

```
var getUser = async (user_id) => { let user = await makeNetworkCall(user_id); if(!user) return null; return user;}var renderTextToDOM = (elem, position, text) => { let docElement = document.createElement(`${elem}`); docElement.innerText = `${text}`; position.append(docElement); return null;}
```

现在，我们可以重构代码

```
var user = getUser(`12345`);var nameH1 = document.querySelector(`#name_h1`)renderTextToDOM(`h1`, nameH1, user.name);
```

我们可以将我们的函数视为单个组件，我们可以理解、优化和重构这些组件，而不用担心它会破坏我们代码的另一部分……这是纯函数和函数式编程为我们开发人员提供的好处之一。

**延伸阅读**

1.  [减少副作用——凯尔·辛普森](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch5.md/#chapter-5-reducing-side-effects)
2.  [JavaScript 中的纯函数是什么](https://www.freecodecamp.org/news/what-is-a-pure-function-in-javascript-acb887375dfe/)