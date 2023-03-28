# 干净的编码实践

> 原文：<https://medium.com/codex/clean-coding-practices-4ab1e8a5cb0e?source=collection_archive---------2----------------------->

## 语言不可知

![](img/6586846729f3c1bd08fee3afaaa0adb7.png)

麦克斯韦·尼尔森在 T2 的照片

随着软件工程行业的增长和发展，一些常见的最佳实践也在增长。这些实践被称为“干净的编码实践”。它们不特定于任何特定的语言，因为它们适用于所有编程语言。干净的编码实践会让你成为更好的工程师，让代码库更稳定，让你的公司赚更多的钱。观察这些实践将允许你花时间构建很酷的新特性，而不是维护脆弱的代码。

> “任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”
> 
> —马丁·福勒

![](img/e36006e13df4e565e4173a0d9d3e0adf.png)

# 命名

> "计算机科学中只有两个难题:缓存失效和事物命名."
> 
> —菲尔·卡尔顿

给事物命名是相当困难的。一个好的名字应该揭示对象的意图。它应该回答这样的问题:它是做什么的，如何使用它，为什么它在这里。

不好:

```
var date1; var date2; private makeExDate() { … }
```

好:

```
var tokenExpirationDate; var tokenRefreshDate; private updateTokenDate() { … }
```

**在命名惯例上保持一致。**通过命名约定为您的技术堆栈中的每种语言定义标准。永远遵循这些标准来编写代码，让你的前一任或后一任都看不出来。这将提供整个应用程序的一致性，并使其在未来更容易支持。

术语要一致。不要因为厌倦了输入“检索”,就使用同义词词典来创造新单词。改变术语只会让后来者感到困惑，并使代码库的搜索变得更加困难。很简单，你未来的自己会感谢你的。

当选择一个名字**时，避免虚假信息**或其他混淆的名字。名字不应该留下欺骗性的暗示。例如，一个名为“UserList”的对象，当它不是一个列表而是一个数组时，它就是虚假信息。我们对“列表”类型的技术知识使得除了“列表”数据结构之外的任何东西都是虚假信息。

在选择名字时，重要的是做出有意义的区分。命名不当的一个例子是参数名为“a1”和“a2 ”,而不是创建像“user”和“account”这样有意义的名称。

使用**可发音的名称**。写下我们所说的名字。

> 作为人类，我们的大脑已经进化到处理口语，因此可发音的名字只是在美学上更好，更容易与同事谈论。
> 
> —鲍勃大叔的干净代码

不好:

```
var tltAmt;
```

好:

```
var totalAmount;
```

创建易于搜索的名字。遵循某种模式的名字给了工程师同事一些直觉，告诉他们在哪里以及如何找到代码。它还可以防止查找和替换错误。

如果你所有的 NgRx 商店都称这个减压器为“减压器”,那么挑选一个你需要的会变得很复杂。然而，如果您将其命名为“userReducer ”,那么我们可以很容易地从 intellisense 中选择正确的类。遵循*【店名】减速器*的模式，更容易:

1.  给班级命名
2.  搜索时查找类别
3.  使 intellisense 更易于使用，因为您不必梳理 8 个“reducer”选项。

这条规则有特定于语言的期望。例如，在 JavaScript 中，当编写一个小的回调函数时，丢弃变量是可以预期的。这是因为这个名字在 1-3 行内就存在和消失了。

```
// This is OK because the variable u is easily understandable as a // user in the collection of users. const thisUser = users.find(u => u.userId === selectedUser.userId);
```

当设计一个应用程序**时，术语应该在开发开始前确定并记录下来。**这应该由利益相关者、架构和工程联合完成。重要的是，每个人从一开始就说同一种语言，这样才能正确地创建数据结构。我们不想设计名为“用户”的数据库表，然后在项目进行 6 个月后，利益相关者开始称用户为“个人资料”。现在，所有 UI 代码都将用户称为配置文件，但是 API 和数据库将它们称为用户。在项目中期更改语言将导致维护代码的人在后端拥有 profile == user 的“领域”知识。这叫做**心理映射**，应该**避免**。从 UI 到数据库保持对象的相同名称，以避免混淆。

**类名应该是名词。方法和函数名应该是动词。没有例外。**

**不要可爱**，搞笑，或双关语。不要将函数命名为“SeekAndDestroy”。创建一个清晰简洁的名称，如“DeleteRecords”。

**使用解决方案域名。**我们可以假设工程师具备计算机科学知识。因此，我们可以使用计算机科学语言、算法名称、模式名称、数学术语等。

**使用问题域名。**与问题域结构有更多关系的代码应该有来自问题域的名字。最坏的情况是，工程师必须在问题域中寻找答案。

有时候，一个词可以有多种意思。添加**有意义的上下文**有助于提供清晰度。想象一个脱离上下文的变量。如果您在邮政编码旁边看到州，那么您的想法与单独看到州是不同的。

**不要** **添加多余的上下文。添加前缀或使用多余的约定既讨厌又过分。如果名字清晰，越短越好。**

# 单一责任

**封装**你的代码。将一个域的所有函数和方法分组到同一个类或模块中(取决于语言)。

**吻**(保持简单愚蠢)。尽可能缩短函数。我见过 3 行长的方法和超过 100 行长的方法。显然，3 行方法更容易阅读。给出*“方法和函数永远不要超过 15 行”*的指导是不合理的。相反，我们应该努力编写具有单一职责的方法和函数。验证用户凭证，然后创建令牌，并在浏览器上设置 cookie 的方法应该将大部分工作委托给其他函数。

不好:

```
function loginUser(user) {
  // All user validation logic
  … 
 var isValidUser = (from u in ctx.users 
                    where u.userId == user.userId 
                    select u).Any();
  if(isValidUser) {
    // A lot of logic to create a token 
   … var token = new JwtSecurityToken( …);
    …
    // A lot of logic to create a cookie 
    var cookie = new Cookie(); return token;
 } return;
}
```

好:

```
public User loginUser(User user) {
   var token;
   if(validateUser(user)) {
     token = createToken(user);
     var cookie = createCookie(token);
     browser.cookies.set(cookie);
   } // 1 return statement limits the ways this function can exit
  // this reduces cyclical complexity & makes the function easier
  // to read 
  return token;
}private Boolean validateUser(User user) {
   // All user validation logic
   var thisUser = from u in ctx.users 
                  where u.userId == user.userId 
                  select u;

  return thisUser.Any();
}private Token createToken(User user) {
  // A lot of logic to create a token
  … var token = new JwtSecurityToken( …);
  … return token;
 }private Cookie createCookie(Token token) {
  // A lot of logic to create a cookie
  var cookie = new Cookie();
  … return cookie;
}
```

我们函数中的语句应该在**一个抽象层次**内。和上面的代码一样，我们不想把代码的读者送进一个永无止境的兔子洞:

这个方法调用这个函数，这个函数调用另一个函数，另一个函数调用另一个函数。我太迷茫了。我甚至不记得我从哪里开始，也不记得我在试图解决什么问题。”

我们可以很容易地让令牌函数创建一个 cookie，因为 cookie 方法无论如何都需要令牌。然而，这不是令牌函数的职责。它不能假设每次创建令牌时，也应该创建 cookie。

做**“一件事”**的任何函数或方法的目标是**没有结果**。它不会发生任何意想不到的变异。没有诡计。它接受参数并发出一个新的值或对象。它从不改变它的参数。改变参数可能会产生意想不到的后果，并导致竞争情况，因为调用函数看不到内部情况。例如:

不好:

```
public Token loginUser(User user) {
   var token;
   if(validateUser(user)) {
      token = createToken(user);
   } return token;
}function validateUser(user) {
  // All user validation logic
  var user = from u in ctx.users
             where u.userId == user.userId select u;

  return user.Any();
}function createToken(user) {
  // A lot of logic to create a token
  … var token = new JwtSecurityToken( …);
  …
  createCookie(token); return token;
}function createCookie(token) {
  // A lot of logic to create a cookie
  var cookie = new Cookie();
  …
  browser.cookies.set(cookie);
}
```

在上面的代码示例中，令牌函数调用了 cookie 函数，它创建了一个 cookie 并将其设置在文档上。我们不想用饼干。拥有一个 cookie 集可能会弄乱 UI，读取无效的内容。作为一名调试应用程序的工程师，我需要花更长的时间来阅读不同层次的抽象(可能还需要一些调试时间来遍历树)，才能发现令牌函数正在调用 cookie 函数。我不想那样。现在，我必须编写一个新函数来创建令牌，但不设置 cookie。真是一团糟！我该怎么称呼这个函数呢？“createToken”已被占用，重命名该函数将导致大量重构工作。如果我移除 cookie 调用，我可能会破坏其他依赖它来设置 cookie 的东西。另外，为什么“validateUser”函数改变了用户参数？如果我需要的用户信息不在对象的数据库版本中，该怎么办？从读取“loginUser”函数看不出用户对象已经发生了变化。

# 评论

> 有可能提出这样一个观点:程序员应该足够自律，以保持注释具有高度的可修复性、相关性和准确性。我同意，他们应该。但是我宁愿把精力放在让代码变得如此清晰和有表现力上，以至于一开始就不需要注释。
> 
> —干净的代码

如果你上大学是为了获得任何一种计算机科学学位，你可能会被告知使用注释来记录你的代码。用像 JSDoc 这样的文档工具记录函数和方法可以让函数的使用者知道:

1.  参数及其用途
2.  预期返回值及其类型
3.  描述方法目的和使用方法的任何其他相关注释。这些应该简明扼要。我们这里不需要历史课。

然而，我们应该始终努力编写自文档化的代码。评论很快就会过时。过时的评论可能会让你之后的人感到困惑。理想状态是代码不言自明。含义:

1.  它的参数有明确有意义的名称。
2.  已经指定了返回类型，因此调用方知道会发生什么。
3.  函数或方法的名称清晰、有意义且可搜索。
4.  它只执行一个任务，从不改变它的参数。

因为这些已经是干净的编码实践，简单地遵循这些习惯将创建自文档化的代码。

不要评论糟糕的代码。添加注释解释代码意味着是时候重构和创建新方法了。

不好:

```
// check if they entered more than 40 hours
if(mondayMinutes + tuesdayMinutes + wednesdayMinutes + thursdayMinutes + fridayMinutes / 60 > 40 || mondayMinutes + tuesdayMinutes + wednesdayMinutes + thursdayMinutes + fridayMinutes * 60 < 0)
```

好:

```
if(getTotalHours() > 40 || getTotalHours() < 0)
```

更好:

```
if(validateWorkWeekHours())
```

**不要在注释掉代码**中留下。这里的逻辑通常是:

*“我们将来可能需要这个街区。”*

或者

“我们离开这里是为了历史的目的。”

使用源代码管理，这样您就可以看到文件在某个时间点的状态。没有理由让你的代码库中堆满旧代码。这将增加应用程序的规模，降低性能。更重要的是，它使得代码更难阅读，这将降低你的速度。

# 错误处理

对于真正干净的代码，错误处理不能掩盖逻辑。我们怎样才能写出干净、清晰的代码，同时还能处理错误呢？

首先写下你的 try-catch-finally 语句。这将使所有快乐路径逻辑远离错误处理，并且错误处理逻辑将全部在一个地方完成。

**给出有例外的上下文。**抛出的异常使您能够确定错误的来源和位置，以便定义失败操作的意图。

**根据调用者的需求定义异常类。虽然我们可以用无数种方式对异常进行分类，比如来源或类型，但关键的区别在于如何捕捉错误。这将为调试错误提供最多的上下文。**

**不返回 null。如果你从不返回 null，你就永远不必检查 null。简单。*(注意:JavaScript 总是需要进行空值检查，因为 JavaScript…)***

# 测试

测试是重要的，并且不能从干净的编码实践中免除。在测试中遵循与代码相同的命名标准和其他干净的编码实践。这将保持测试快速运行，并使它们易于维护。在测试中遵循第一条原则可以保持测试的干净:

*   ast:测试应该快速运行，否则人们不太可能运行它们，代码会开始腐烂。
*   不依赖:测试不相互依赖。
*   可测试的:它们可以在任何环境下运行。
*   自我验证:他们要么通过，要么失败。你不需要解释结果。这要求每个测试有一个断言。
*   我认为测试应该写在产品代码之前。之前写是关键。如果您在之后编写，产品代码可能很难测试。

# 设计规则

> 软件开发最重要的一个方面就是要清楚你想要构建什么。
> 
> 比约恩·斯特罗斯普

在你开始一个项目之前，正确的设计是关键。与利益相关者就您正在构建的东西达成共识将防止您构建错误的东西。对建筑的相互理解将:

*   让队伍继续前进
*   缓解工程和建筑之间的矛盾
*   在公关时防止重构和挫折

如果你正处于冲刺阶段，正在进行团队讨论，讨论如何编写代码，那你就太晚了。这将通过占用宝贵的 sprint 开发时间来减缓团队的速度，因为你应该在设计阶段就进行长达一小时的会议和对话。

> 没有需求或设计，编程就是给一个空文本文件添加 bug 的艺术。
> 
> —路易·斯利格雷

创建一个合适的设计是这个过程中最重要的步骤之一。创建设计的工程师应该在实施设计之前咨询架构并获得批准。应该给建筑师适当的时间来设计整个系统。工程师可以帮助设计项目中较小的功能。

架构师挑选团队将使用的设计模式。如果有必要，工程师学习模式，并开发符合所选设计模式的代码。记住，建筑师在规划森林，工程师在种树。

其他一些需要记住的事情:

*   遵循 Demeter 的[法则:一个类应该只知道它的直接依赖。](https://en.wikipedia.org/wiki/Law_of_Demeter)
*   使用依赖注入(DIP)来避免紧密耦合。
*   如果你使用第三方库，包装它们，这样如果它们改变了，只有你的包装器实现需要改变。

# 其他的

## 组织代码

尽可能接近变量的用法来声明变量。实例变量应该在类的顶部声明。所有的变量、方法和函数都应该按照可访问性的顺序排列，公共成员在顶部，私有成员在底部。常量应该在类的顶部或在它们自己的文件中声明。

**干:**不要重复自己。在创建新的方法/函数之前，搜索代码库，确保它不存在于其他地方。

[**规则 3**](https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming)) :不要创建一段代码的“共享”版本，直到它被使用过两次。规则 3 意味着在第三次编写代码时，很明显代码是共享的，可以重构到共享位置。如果您在编写代码之前知道代码将被共享，那么您可以在第一时间将它放在一个共享位置。

**不要硬编码。**定义一个常量或使用变量来代替硬编码的值。使用该变量不仅使其可读，而且如果在多个地方使用，也使其易于更改。

不好:

```
public getUsers() {
  try { ... }
  catch {
    this.notifyUserOfError("There was an error. Please try again.")
  }
}
public getOrders() {
  try { ... }
  catch {
    this.notifyUserOfError("There was an error. Please try again.")
  }
}public getProducts() {
  try { ... }
  catch {
    this.notifyUserOfError("There was an error. Please try again.")
  }
}private notifyUserOfError(message) {
 this.messageService(message);
}
```

考虑上面的代码。我们在 3 种方法中重复了相同的错误信息。如果利益相关者回来告诉我们将信息修改为:

*“遇到错误。如果您继续收到此消息，请联系 support @ myapp . com。"*

现在我们必须在 3 个地方修改消息。这叫一个**“魔弦”，要避免**。让我们看看一个常量是如何使这段代码更容易维护的。

```
export const genericErrorMessage = "There was an error. Please try again.";public getUsers() {
 try { … }
 catch {
   this.notifyUserOfError(genericErrorMessage)
   }
}public getOrders() {
   try { … }
   catch {
     this.notifyUserOfError(genericErrorMessage)
   }
}public getProducts() {
   try { … }
   catch {
      this.notifyUserOfError(genericErrorMessage)
   }
}private notifyUserOfError(message) {
 this.messageService(message)
}
```

现在我们有 1 个地方来修改消息的文本。我们可以将这个神奇的字符串移动到“notifyUserOfError”方法中，但是这样我们将失去定义自定义错误消息的能力。

**永远做个好童子军。**让代码库比你发现时更整洁。这些实践是在数十年的代码编写之后逐渐形成的。重要的是要记住，相对而言，我们的行业并没有那么古老。虽然律师和会计师已经做了几个世纪的工作，但计算机编程作为一种职业才出现了几十年。最佳实践会随着时间的推移而发展，我们需要与时俱进。今天存在的实践在 20 年前还处于萌芽状态。您将会遇到不符合这些标准的代码，因为这些代码是在它们存在之前编写的。

作为工程师，我们在职业生涯中也不断成长。你一年前写的代码可能不像你昨天写的代码。你可能会看着代码对自己说*“我在想什么？“amtToRcvTl”到底是什么意思？”*。出于这些和许多其他原因，我们应该一直寻找方法来提高可读性、可维护性和可测试性，以使我们的代码库比我们发现的更干净、更稳定、更好。

![](img/d3d24976b9882bd9ae794475b4b1cc18.png)

## 闻

知道如何编写干净的代码在你成为一名出色的工程师的道路上很重要。同样重要的是能够审查其他人的代码，发现代码的味道，并提供建设性的反馈。以下是一些常见代码气味的列表:

*   **过时评论**:旧评论。
*   **多余的评论**:描述不言而喻的事情的评论。
*   **写得差的评论**:最差的文笔过度解释了没人关心的细节。
*   **注释掉的代码**:这只会随着时间而腐烂。你有版本控制。把它拿掉。
*   **测试需要不止一个步骤**:所有的单元测试应该用一个命令运行。
*   **函数有太多参数**:尽量减少到零个、一个或两个。超过三个就值得怀疑了。
*   **死函数**:函数如果从来没有被调用过，就应该被删除。
*   重复:代码中的重复错过了抽象的机会。
*   **不一致**:每次都用同样的方式做同样的事情。
*   理解算法:确保你完全理解你的函数是如何工作的。可以理解的是，实现这个目标的过程来自于这里或那里的小调整，但是在你完成之前，要真正了解全局。
*   **用命名常数替换幻数和字符串**:幻数是那些目的不证自明，但我们需要它们进行具体计算的奇怪数字。让这些数字保持原样，没有标签，可能会使未来的读者感到困惑，并模糊操作正在做什么。