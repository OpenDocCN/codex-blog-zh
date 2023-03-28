# 理解 JavaScript 中的“this”——完整指南

> 原文：<https://medium.com/codex/understanding-this-in-javascript-the-complete-guide-c4c21fe15ff8?source=collection_archive---------10----------------------->

![](img/491f92600e282238fea79b0739b73ffb.png)

扎卡里·凯米格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Namaste Devs，我敢肯定你在学习甚至面试的时候已经把自己和 JS 里的“这个”搞混了。有一天，当你坐在电脑前阅读这篇文章时，你会有一个灵光乍现的时刻。我将尝试解释“this”的传统工作方式和 ES6 中的新实现(箭头函数)。所以坐稳了。

简单地说，“this”是对包含当前函数的对象的**引用。就这么简单，但是什么是当前对象，以及在代码运行时它是如何动态变化的，这就有点令人困惑了。**

它的值将取决于运行时函数被调用的位置，用专业术语来说，我们称之为**“调用点”**。调用一个函数有多种方法，为了简单起见，我将在这里展示其中的两种

```
function doSomething(a){
   console.log(a);
}var obj = {
 key: "value",
 doSomething: function(){ console.log(" namaste"); }
}// you can call the function doSomething like thisdoSomething("jainish"); // jainish
obj.doSomething(); // namaste
```

嗯，这个你已经知道了，那么你认为我想从这点说明什么呢？看看我们是如何调用这些函数的。他们有什么不同吗？如果有，区别有多重要？

这个的答案是**“执行上下文”**。但是 WTF 是上下文，怎么有用。为了解释这一点，让我深入研究一下 JS 在幕后是如何工作的。

让我们来理解当一个简单的 JS 程序被执行时会发生什么。

1.  创建**全局执行上下文**并将其推入执行堆栈。
2.  然后，引擎执行代码。
3.  如果遇到函数调用，引擎将首先编译代码，在此期间创建新的**执行上下文**并将其推送到执行堆栈上，最后执行代码
4.  执行完成后，弹出 fn 执行上下文

**“执行上下文”**是 JS 中的一个抽象概念。它是一个对象，包含函数执行所需的特定信息。该信息包含多个属性。仅举几个例子。

1.  **Activation/Variable object**=>存储传递给函数的参数、变量以及该函数中声明的函数的信息。
2.  **范围链**的创建。([你可以在这里了解更多](https://jainishshah12.medium.com/yeh-lexical-scope-hai-kya-da08a1601f2f))
3.  此的**值。**

全局执行上下文类似于函数上下文，只是默认情况下它是加载到堆栈中的。

这并没有准确回答上面提到的两个 doSomething 函数之间的区别，但它表明“ **this** ”本质上是动态的，并且根据调用位置而变化(其**值基于执行上下文堆栈**顶部的上下文)。

有 4 个简单的规则来描述“这”是如何工作的

1.  默认绑定
2.  隐式结合
3.  显式绑定
4.  “新”运算符

当然，我们将讨论某些例外(注意:ES6 的箭头函数使用一些不同的机制，首先理解这些规则将有助于您理解箭头函数是如何工作的)

```
function doSomething(){
  console.log(this.a);
}var a = 6;doSomething(); //6
```

可以看到我们已经在全局空间声明了函数(not is a function)。当我们执行这个函数时，它输出 6。但是为什么呢？

答:我们谈到了执行堆栈，默认情况下，全局执行上下文位于堆栈顶部。当我们调用某个东西时，它的执行上下文将被推送到栈顶。因为这个函数属于全局对象，所以它的值将是全局对象(Node.js 中的 global 和 browser env 中的 window)。全局对象具有属性“a ”,因此输出将是 6。这是一个**【默认绑定】**的例子

```
function doSomething(){
  console.log(this.a);
}var a = 6;
var obj = {
  a : 12,
  doSomething: doSomething
};obj.doSomething(); //12
```

这里发生了什么变化？在这种情况下，我们用前缀(“obj”)调用 doSomething。).以这种方式调用函数会将函数执行上下文中“this”的值设置为“obj”(对对象 obj 的引用)。obj 具有属性“a ”,因此打印的值将是 12。用对象引用作为函数调用的前缀是**“隐式绑定”**的一个例子。您正在隐式调用一个对象的函数。

```
var a = 6;
var obj = {
  a : 12,
  doSomething: function() { console.log(this.a) }
};var foo = obj.doSomething;foo(); //6
```

该死的。！！我们刚刚了解到，用 obj 引用调用一个函数会打印 12，但 wtf 做到了打印 6。这里有一个重要的概念。一个函数定义存储在堆中，当你用 obj 的 doSomething 赋值“foo”时，你实际上是在内存中传递那个函数的引用。在对象引用和函数声明的地方没有相互联系，但这取决于调用函数的地方。将执行上下文考虑在内。全局执行上下文在栈顶。你调用函数 foo，它是全局上下文的一个属性，所以当执行 foo 时，它的值将被设置为全局上下文。所以答案是 6。

我希望到目前为止，你能够理解我们带入画面的“对象”。如果没有，请重新阅读到这一点。

隐式和默认绑定是 **"JS 引擎对这个"**"的值做出决定的一种方式。但是如果你是一个开发者，你想设置这个值。您希望在什么情况下指定该值？语言中为此提供了什么机制？

我们举个例子。

```
var input = 1;function square() { 
 let cbFn = function(){ console.log(this.input*this.input) };
 setTimeout( cbFn , 1000);
};var obj = {
 input : 3,
 square: square
};obj.square(); // 1
```

我敢肯定你会猜到答案是 9，但为什么它打印 1！！这一次我们甚至调用了带有对象引用的函数。男孩，我累了…..好吧，再忍耐一分钟。很简单…答案就是执行上下文堆栈。

当您调用函数 obj.square()时，square 函数被推送到执行上下文堆栈上，其中“this”指向“obj”。如果不是 setTimeout，而是打印平方逻辑，它会打印“9”。但是哪里出了问题？看看你什么时候设置的，引擎会把它放在一边，等以后执行。这样一来，square 函数的执行就结束了，这导致它的执行上下文从堆栈中弹出。当 setTimeout 解析后，它被推送到堆栈中，全局对象位于堆栈的顶部。正如你现在看到的，他的函数将作为全局对象的一部分被调用，因此它将选择输入值“1”并打印“1”。

你将如何修理它？这就是你应该有一些机制的地方，你可以在 setTimeout 的函数中显式地设置这个值为 obj。再比如**“事件处理程序”**。当您在事件处理程序上添加回调时，您必须确保“this”的值只是对该 DOM 元素的引用。

## **调用、绑定、应用**

这三个函数有助于将“this”的值显式设置为我们想要的对象。

**调用并应用使用给定的“上下文”和参数立即调用函数**。而 **bind 返回一个函数**，其上下文(this)设置不可更改。

```
function foo() { 
  console.log(this.key);
}var objA = { key : "A" };
var objB = { key : "B" };foo.call(objA) // A
foo.call(objB) // B// Apply works the same way , there is a small difference between  // call and apply on format of arguments passed to itvar bindedFoo = foo.bind(objA);bindedFoo(); // A;var doubleBindedFoo = bindedFoo.bind(objB);doubleBindedFoo() // A (Once binded the value of this can't be changed)
```

上面的例子展示了动态绑定工作的基本结构。让我们再次以 setTimeout 为例，修改它来解决这个问题

```
function doSomething(){
 let cbFn = function () { console.log(this.input*this.input )};
 let bindedcbFn = cbFn.bind(this);
 setTimeout( bindedcbFn , 1000 );
}var obj = { 
     input : 3, 
     doSomething: doSomething 
}; obj.doSomething(); // 9// One great example can be of debounce method ("primarily used in // search, suggestion => while the user is typing don't make API    // calls , instead delay the execution till user finishes typing")function debounce(fn,timeout) {
 let timer;

 return  function (...args) { 
     let context = this;
     clearTimeout(timer);
     setTimeout(  function() { fn.apply(context,args) } , timeout );
    };
}
```

所以这些都是显式绑定“这个”的方式。您可能已经使用了构造函数。但是 JS 真的有类吗，构造函数有意义吗？？原来构造 fn 只是一个常规函数。在任何函数前使用**“new”**关键字，使其成为构造函数 fn。但是“新”关键字的作用是什么。

如果构造函数没有返回一个对象,“new”关键字将创建一个全新的对象并返回它。但是“这个”是从哪里来的呢？事实证明当你用" new "关键字调用一个函数时。 **JS 在后台新建一个对象，并将新建对象的引用传递给函数，从而使这个新对象成为“this”。**

```
function Name(fname, lName) {  
   this.fname = fname; 
   this.lName = lName;  
}var newObj = new Name("jainish", "shah");
console.log( newObj.fname + "  " + newObj.lname); // "jainish shah"
```

最后…我们已经在这里讨论了所有 4 个规则。但是，如果一个以上的规则同时出现，我们需要一些优先规则呢

1.  新绑定
2.  显式绑定
3.  隐式结合
4.  默认绑定

```
function foo() {
  console.log(this.name || "name not defined");
}var obj = { name: 'jainish' };
var bindedFoo = foo.bind(obj);
bindedFoo() // jainishvar newFoo = new bindedFoo(); // name not defined
```

您可以清楚地看到，即使 bindedFoo fn 将“this”设置为 obj，它也会被“new”关键字覆盖。这与上面所说的“一旦绑定”fn 就“不能改变值”的说法相矛盾，并不完全正确。它高度依赖于“bind”方法的实现。(为 polyfill 签出 mdn 文档)

这就把我们带到了文章的结尾。我知道它很长，但是大小很重要😂😂。我将在下一篇文章中讨论“箭头函数”。我希望你学到新的东西，直到下一个。

如有任何疑问，请联系我@ jainishshah12@gmail.com