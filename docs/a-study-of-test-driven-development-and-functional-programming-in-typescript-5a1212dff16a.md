# TypeScript 中测试驱动开发和函数式编程的研究

> 原文：<https://medium.com/codex/a-study-of-test-driven-development-and-functional-programming-in-typescript-5a1212dff16a?source=collection_archive---------4----------------------->

## 从基于类的 IoC 到功能性的 TDD？—一次旅行

![](img/982d4d15c03b7810c8faa90ed614c4cb.png)

照片由 [Unsplash](https://unsplash.com/?utm_source=Hashnode&utm_medium=referral) 上的 [Dmitry Ratushny](https://unsplash.com/es/@ratushny?utm_source=Hashnode&utm_medium=referral) 拍摄

# 介绍

我来自面向对象编程(OOP)的背景——这是我学习编程的形成时期的热门话题——由 C++和 Java 驱动。拥抱单元测试意味着拥抱控制反转(IoC)和依赖注入(DI)。这就是方法。

随着我选择的语言切换到 TypeScript，以及通过 HTTP APIs 传递接口定义的对象来传递数据，真正的 OOP 类消失了，但我继续使用 DI 类。业务逻辑在服务类中，服务类实际上只是构造函数中共享 DI 的函数集合。单元测试模拟出了所有的依赖关系，我的覆盖率经常达到 100%，这让我很自豪。我首先用单元测试*测试了我的代码，所以在第一次部署时一切正常。我基本上忘记了如何使用 IDE 的调试器。*

一切都很好。

我曾听到测试驱动开发(TDD)的粉丝们惊叹它的神奇之处，但我认为我的*部署前测试*方法基本上做到了这一点。(事实证明，并不尽然。)

我曾听到函数式编程(FP)的粉丝们惊呼它们优于面向对象编程。但是，嘿，我只是用类来为 DI 组织我的函数——所以我基本上是函数式的。(是，但不是。)

我看不出这些之间有什么联系。这两个我都没有完全理解，我知道。偶尔，我会阅读一篇关于 TDD 或 FP 的博客文章，试图理解这种兴奋感。我想发现这些技术令人兴奋！但是我读的所有东西都很基础，我看不到如何将其应用到实际问题中，它们也没有显示如何将概念联系起来。或者更糟的是，TDD 被完全曲解了，看起来很可怕，因为做错了是可怕的。

我认为:功能性对于纯粹的功能来说是很好的，但是你如何测试不纯粹的编排呢？

# 什么是真正的测试驱动开发？

Robert c . Martin(Bob 叔叔)在他关于[期望专业化](https://www.youtube.com/watch?v=BSaAMQVq01E)的演讲中涉及了很多关于软件开发实践的内容，特别是这里的 TDD。(看这些。)

与复式记账会计实践的类比尤其引起了我的共鸣。复式簿记是为每个账户保留一个交易分类账的纪律。当你从一个账户中扣除时，它必须立即被加到另一个账户中。通常这个“账户”不是传统意义上的账户，它只是一种跟踪和*检查你工作的手段。这是校验和。这里减去；加在那里；比较总数。如果你犯了一个错误，簿记员马上就知道了。*

我们经常编写所有的代码，然后*编写所有的测试来检查它。记账员*不要*。他们在这里减去一个条目，然后在那里加上它。这是原子操作。为什么？因为如果他们都做了一个，然后又做了另一个，并且犯了一个错误，那么最后的校验和就是“不匹配”。错在哪里？它是他们添加的*多个*事务之一中的*某处*。这很痛苦，而自动输入所有事务就没那么痛苦了。*

TDD 就像复式记账一样。规则被放在两个地方，原子地。写测试代码，然后写一些生产代码。校验和。写一些测试代码，写一些生产代码。校验和。这听起来可能很痛苦，但是如果你引入了一个 bug，你会立刻发现它。这是终极林挺工具。与生产中的一个 bug 相比，这有多痛苦？一旦你习惯了，就不会了。这是#1 TDD 承诺:无 Bug 代码！(警告:当然，你必须理解想要的行为。否则你会写两次错误的行为。)

这是第二个 TDD 承诺:你的代码将会更干净，更容易维护，并且是自文档化的。观看视频，找出这个承诺的论据。

如果 Bob 叔叔和我最终让你相信测试驱动开发(TDD)是令人惊奇的，那么请继续关注 Ian Cooper 关于这个主题的演讲: [TDD，哪里出错了](https://www.youtube.com/watch?v=EZ05e7EMOLM)(是的，这实际上是专业的 TDD！他指出了人们误解的地方。)

接下来在我的研究中，我发现了这本指南。不再是一个简单化的例子。TDD 和 FP 在一个大到足以应对真正挑战的例子中出现，并逐步解释而不仅仅是一个最终结果。但是…嗯……这些工厂功能是怎么回事？现在一切都是工厂。😬

最后一篇文章链接到这个[无模拟测试的原始指南](https://www.jamesshore.com/v2/blog/2018/testing-without-mocks)。这是另一个与 TDD 紧密联系的概念，但是这个概念使用了类而不是函数。尽管这些课程仍然涉及工厂。此外，示例显示了 React 代码——提醒我 React 组件本身使用工厂模式，无论是基于函数的还是基于类的。我不知道他为什么想完全不模仿(到了在进程中运行一个假服务器的地步——已经模仿过了！)，但要点是嘲笑(和假装)可能是最后的选择，而不是第一选择。

# 什么是函数式编程？

实现全功能，还是使用类？在某种程度上，它们都使用 JavaScript，但这是这些例子中突出的一点:它们都是 JavaScript 而不是 TypeScript。在某种程度上，我可以看到，当进行适当的 TDD 时，强类型的有力论据被削弱了:失败是默认发生的。这让我想到也许我可以允许隐式`any`(关闭 eslint 规则)且不为所有的*定义类型，但是强类型仍然有好处。FP 的类型更难吗？在 FP 和完全动态类型化之间确实存在关联。🧐*

*什么时候使用类 vs 函数？[这篇关于将](https://labs42io.github.io/clean-code-typescript/) [CleanCode](http://cleancoder.com/) 应用于 TypeScript 的文章帮助我认识到这不是一个非此即彼的选择。看着这些模式，我意识到我可以*学习*功能，但是在有意义的地方享受类和真正的 OOP 的好处。简而言之:类用于面向对象的逻辑，模块用于相关功能的分组。*

**但是等一下*，你个 FP 修炼者吼道！函数式编程不是这样的！是的，鲍勃叔叔在他的关于函数式编程的演讲中给了我很好的指导。真正的 FP 是关于无国籍的。一个纯函数是没有副作用的——给定相同的输入，它将总是有相同的输出。纯粹。可测试。无国籍。经常递归。事实上，FP 纯粹主义者告诉我们，泛函没有状态，甚至没有局部变量，也没有循环；递归取代了这两者，为了让开发者体验不那么差，你需要一种为此设计的语言。(Lisp，Haskell，Clojure，…)简单地只使用函数而不使用类并不自动意味着 FP。真的，应用不能完全是 FP；只有函数才能是纯粹的。没有状态的应用程序只是一个很大的功能，用途非常有限。此外，我们在这里讨论的是 JavaScript，它不是真正的 FP 语言，而是一种混合语言。函数不一定要纯。我们甚至有类(函数加状态)。*

*JavaScript 可以做 FP 吗？是的，像`map`和`filter`这样的`Array`功能是有效的。流行的 [RxJS](https://rxjs.dev/) 库也是函数式的，如果你使用过它，你就会知道函数式有多复杂。TypeScript functional 中的大狗是 [fp-ts](https://gcanti.github.io/fp-ts/) 。与 RxJS 相比，它更需要整个应用程序团队的大量投入来学习和使用这种方法。*

*那么我是在看真正的 FP，还是只是没有使用`class`关键字？*

# *所以… TDD？*

*我买的是 TDD 卖的。这听起来是可行的，个人甚至可以在现有的项目中做到这一点，而不需要强迫一个应用程序的其他开发人员，无论是现在还是将来，去理解和使用它。(的确，我已经开始这么做了！)*

*不知道如何真正开始？*

*下面是 TDD 的三个规则:*

1.  *编写产品代码只是为了通过失败的单元测试。*
2.  *编写的单元测试不要超过足以失败的程度(编译失败就是失败)。*
3.  *除了通过一个失败的单元测试所必需的，不要写更多的产品代码。*

*这里有一些用 JavaScript 进行 TDD 的[实时流例子。](https://www.jamesshore.com/v2/projects/lunch-and-learn)*

# *所以……FP？*

*对我来说，用 TypeScript，不行。*

*肯特·c·多德关于功能教学法的观点值得一读。我不接受“`this`太复杂”的论点，我认为他只是对`class`关键字过敏，但是用纯函数代替 OOP 的无状态编程有一些优点。它不仅使测试变得容易，而且使用定义为接口而不是类的对象非常好。对于一些逻辑转换，可以用一个类来实现构建器模式。*

*我们应该在模块(文件)级别而不是类级别定义函数吗？下面是我们如何在没有任何花哨的依赖注入(DI)魔法的情况下实现控制反转(IoC ):*

```
***function a() {
}

function fakeA() {
}

function b() {
}

function fakeB() {
}

export function moduleFactory(
        otherModuleDependency = otherModuleFactory(),
) {
   return {a, b};
}

export function fakeModuleFactory() {
   return {a: fakeA, b: fakeB};
}***
```

*许多 FP 代码实际上不仅对`class`关键字过敏，甚至对`function`也过敏！因此，我们可以像这样将工厂函数导出为模块默认值:*

```
***import UserRepository from "user.repository";

export default (repo = UserRepository()) => {
   return {
      a: () => {
      },
      b: () => {
      },
   };
}***
```

*对我来说，这比带有构造函数 DI 的漂亮的`export class UserLogic`可读性更差。*

*[Reddit 上的 dvlsg](https://www.reddit.com/r/typescript/comments/ufucle/comment/i72c5di/?utm_source=share&utm_medium=web2x&context=3)也许最好地总结了函数(闭包)与类的争论:*

> **有些人只是更喜欢在类上使用闭包而不是使用状态，不喜欢使用‘this’。真的是这样。这是一个有效的观点，但它只是一个观点。**

*然后是推特上的 Dax Raad:*

> *没有人谈论函数式编程的一个基本权衡是，你用得越多，你就变得越烦人*

*FP 是一个大转变。一路走下去会使你的代码不被大多数编程社区所维护，因此需要完全的认同。选择用一种非母语的语言来做 FP 比试图说服人们转向一种合适的 FP 语言更糟糕。可以提出这样做的理由，但这是一个巨大的飞跃。*

# *所以… OOP？*

*函数式和面向对象并不是非黑即白的对立选项。我们来稍微探索一下。*

*我们当然可以从 FP 那里借鉴很多，就像 JavaScript ES2015 在引入新的`Array`函数如`map`和`reduce`时所做的那样。将业务逻辑塞入纯函数和不可变类中采用了一些 FP 概念，但没有太深入。*

*用于 DI 和测试的只有功能和“没有模仿”(结果证明只是手工制作的赝品)的方法看起来并不能买到任何东西。类构造函数被替换为工厂。这里有一个很好的例子。高度相关的函数是按一个工厂返回的方式组合在一起，还是按一个类组合在一起，没有任何区别。我当然也遇到过类变大的问题，因为某件事情的所有逻辑和编排都被塞在一起，但是函数和模块也会发生这种情况。*

*解决方案不是按照它们操作的东西对功能进行分组，而是按照它们共享的依赖关系([高内聚](https://en.wikipedia.org/wiki/Cohesion_(computer_science)))和它们做什么([单一责任](https://en.wikipedia.org/wiki/Single-responsibility_principle))。不要让一个`UserService`类变得越来越大，而是让每个用例都有一个操作用户的类。这使得班级人数很少。*

*下面我使用来自`"domain/models"`的接口和一个(纯)保护函数，来自`"domain/logic"`和`"utils/assertions"`的纯函数，并把它们作为一个*用例*协调在一个干净的内聚类中。*

```
***import { isUserSignUpRequest, UserSignUpRequest, UserSignUpResponse } from "domain/models";
import { newUserFactory } from "domain/logic/user";
import { assertValidInput } from "utils/assertions";

// Optionally use DI magic to gain performance of singletons:
// @Injectable()
export class UserSignUpUseCase {
   // Dependencies defaulted - tests can provide mocks or fakes
   constructor(
           auth = new AuthService(),
           userRepo = new UserRepository(),
   ) {
   }

   async process(request: UserSignUpRequest): Promise<UserSignUpResponse> {
      this.validateRequest(request);
      const user = newUserFactory(request);
      await this.userRepo.put(user);
      return {user};
   }

   private validateRequest(request: UserSignUpRequest): void {
      this.auth.assertIsNotAuthenticated();
      assertValidInput(request, isUserSignUpRequest);
   }
}***
```

*这些用例类也可以被称为传统意义上的“控制器”。这个名字现在经常用于 RESTful 路径处理，所以我发现`UseCase`更清晰。*

*这可以通过模块作用域导出工厂函数来实现吗？绝对的。它的可读性会一样强吗？我说不。*

# *要遵守的规则？*

*这是我个人的看法，也是我打算如何继续下去。你的研究和背景可能会让你得出不同的结论。*

1.  *使用 TDD，根据具体情况灵活使用。(不要执着。)*
2.  *为 API 使用类型和接口。*
3.  *支持业务转换逻辑的纯函数——任何带有一个或两个参数，并返回一个或两个结果而没有突变或外部状态的函数。*
4.  *在有价值的地方使用 OOP 但是对象必须是自包含的，这样状态变化*只*影响对象实例。*
5.  *对具有[单一职责](https://en.wikipedia.org/wiki/Single-responsibility_principle)和[高内聚](https://en.wikipedia.org/wiki/Cohesion_(computer_science))的*小*类使用依赖注入(DI)。这些类不是面向对象的，只是函数和依赖的集合。这可以用模块和工厂函数来完成，但是对于可读性较差的语法来说，这比一个干净的类要花费更多的精力；这种语法上的好处是有充分理由的。此外，将函数分组名(类)与文件名分开感觉更灵活。*
6.  *如果需要一个测试用例，将它与真实代码一起导出，这样它就可以被依赖于它的任何测试重用。*

# *文件结构*

*综上所述，我该如何组织源代码呢？*

```
 *.
 └── src
     ├── domain
     │   ├── logic # domain logic functions & OO classes
     │   │   └── user.logic.ts
     │   └── models
     │       ├── index.ts
     │       └── user.ts
     ├── handlers
     │   └── user-sign-up.lambda.ts
     ├── external
     │   ├── dynamodb.service.ts
     │   └── repositories
     │       └── user.repository.ts
     └── use-cases
         └── user
             ├── user-sign-up.test.ts
             └── user-sign-up.ts*
```

*整个`domain`，或者仅仅是`domain/models`，可能会进入一个单独的包，与客户端代码共享——这些模型就是你的 API 契约。*

*`handlers`是代码的入口点。对于不同的环境，您可能有不同的处理程序，比如一个用于 AWS Lambda，另一个用于容器化的云。这让你踏上了[六边形建筑](https://adam.fanello.net/hexagonal-architecture-by-example-meetup)之路。在这里，`user-sign-up.lambda.ts`只是处理来自 API Gateway 和 Lambda 的解包请求，调用用例，并将正确的响应格式化回 Lambda 和 API Gateway。*

*`external`目录是你的六边形架构的右边——与外部资源和状态目标的接口。*

*最后，`use-cases`协调各个请求和命令的处理。*

# *结论*

*这些技术的探索很有趣，但是肯定会让你的博客文章更长！TDD 和 FP 之间的关系并不像我开始想的那样紧密，但是弄清楚如何用 FP 进行单元测试是非常重要的。*

*让我们总结一下:*

1.  *TDD 给你超能力；拥抱它！*
2.  *真正的函数式编程只在为它而设计的语言中实用，但是我们可以从 FP 中吸取经验并应用到其他语言中。*
3.  *`class`关键字不是毒药，是 OOP 之外有用的语法糖。*

**最初发表于 https://adam.fanello.net/tdd-and-fp-study*(此处代码格式更好)。*