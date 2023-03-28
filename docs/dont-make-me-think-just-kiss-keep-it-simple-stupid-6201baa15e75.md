# 不要让我思考。亲亲就好！(保持简单，笨蛋)

> 原文：<https://medium.com/codex/dont-make-me-think-just-kiss-keep-it-simple-stupid-6201baa15e75?source=collection_archive---------8----------------------->

![](img/0d7e79312eb464af0341ff5209f78b96.png)

巴勃罗·阿罗约在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

许多开发人员，尤其是在他们职业生涯的初期，喜欢创建复杂的解决方案来证明他们的技能。发明如此“古怪”的东西，以至于你不得不向别人解释，这是一件好事——你会觉得自己更聪明、更有智慧、更有创造力。此外，当团队的其他成员不得不向你寻求一些技巧来理解你的解决方案时，你可能会认为这显示了你的专业技能…但是你知道吗？这在 It 世界里是行不通的(即使有时候我们很想这么做)。伟大的程序员不是艺术家，他们是工匠。团队工作人员不仅为他们自己做事情，也为以后会使用他们代码的人做事情。那些明白仅仅为了证明你的技能而过度复杂化你的代码会产生相反效果的人。这就是为什么最好的程序员的代码不同于优秀的程序员。正如马丁·福勒所说:

> “任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”

我非常同意这句话。问题是——如何为人们而不仅仅是为编译器创建代码？幸运的是，答案很简单——使用编程中最强大的最佳实践之一 KISS——保持简单，笨蛋。

# 吻

这条规则是美国海军于 1960 年创立的。他们的目标是让一切尽可能简单(但不要更多)。它可以应用于几乎所有的生活领域——工程、商业、软技能——也可以应用于十二步计划(帮助戒毒的互助组织)。里面很多人总结为**不要过度工程**。甚至，从理论上来说，这听起来几乎很傻，但却非常重要，影响深远。编写简单代码的原因有很多:

*   简单、可读的代码更容易维护和扩展。
*   简单的解决方案更容易调试。
*   出现 bug 的可能性较低。
*   基于 KISS 的代码更容易测试(也是自动测试)。
*   项目的入门级更低(你需要更少的时间把你的代码交给一个新的开发人员)。

对我来说最重要的是——我不必花很多时间去理解代码。不管我是因为警觉而在半夜醒来，使用新的代码库，还是在几个月后回到这个项目——几秒钟就足以了解正在发生的事情。我们的大脑——一个令人敬畏的内置处理器——容量有限，不需要的时候不要浪费它的能量！

![](img/dd88b9f245a6b544b1a0190b04a31883.png)

照片由[詹姆斯·哈里森](https://unsplash.com/@jstrippa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 实用技巧

当你知道为什么它如此重要时，你可能会开始考虑如何在你的代码中应用 KISS 规则。下面，你可以找到一些有用的提示:

## 使用有意义的、可读的变量

为什么我们需要标签？因为有了它，我们可以用一个词来表达整个意思。当我说`elephant`的时候，不用我说，它是一种长着长鼻子和大耳朵的大动物。那么，为什么你仍然使用像`tmp`、`MyClass`或`helper`这样的变量呢？使用正确的、有意义的、可读的变量名可以避免很多理解代码的麻烦。看看下面的代码就知道了:

```
class MyClass {
  helper = (i: number): string => {
    if (i % 3 === 0 && i % 5 === 0) return 'FizzBuzz';
    if (i % 3 === 0) return 'Fizz';
    if (i % 5 === 0) return 'Buzz';
    return i.toString();
  };

  main = (j: number): void => {
    for (let i = 1; i <= j; i++ ) {
      let tmp = this.helper(i);
      console.log(tmp);
    }
  };
}const tmp2 = new MyClass();
tmp2.main(20);
```

你能在几秒钟内说出这段代码是做什么的吗？当然，你可以理解功能，但这不是最好的体验。让我们稍微改变一下:

```
class FizzBuzz {
  getWordForNumber = (number: number): string => {
    if (number % 3 === 0 && number % 5 === 0) return 'FizzBuzz';
    if (number % 3 === 0) return 'Fizz';
    if (number % 5 === 0) return 'Buzz';
    return number.toString();
  };

  playGame = (numberLimit: number): void => {
    for (let current = 1; current <= numberLimit; current++ ) {
      let word = this.getWordForNumber(current);
      console.log(word);
    }
  };
}const fizzBuzz = new FizzBuzz();
fizzBuzz.playGame(20);
```

我只改了名字，但影响真的很大——即使是在这么简单的情况下。想想它在现实生活中会有多有用吧！

## 将常量值提取到变量中

或许，你至少能回忆起一个时刻，当你在代码中看到一些数字，却不知道它们是什么意思。是一些业务需求吗？还是数学规律？也许是一些制度/法律的限制？即使你在实现过程中记住了它们，过一段时间后它们的意义可能就不那么明显了。我们都记得 Phi 数大约是 3.14 或者一天是 24 小时。但是你能说 84600 是什么吗？还是你代码中的第 9 号？将常量值保存到具有描述性名称的变量中(如下所示)可以避免这个问题。

```
const SECONDS_PER_DAY = 84600;
const DAILY_HOURS_DRIVER_LIMIT = 9;
```

![](img/9390c7e2bc1ce665b91cfa066aaf5c21.png)

一个好的标签帮助我们理解一个给定的事物是什么(或者我们在哪里)。Tim RüMann 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 提取逻辑以分离命名良好的函数

你知道怎么吃大象吗？一次一口。它也适用于代码。只有当代码太长时，许多人才会将代码分割成更小的部分。但是有时候拆分一个短代码可能是有用的。看看下面的代码就知道了:

```
const isFormValid = {
  email: string,
  phone: string,
  address: string,
  validator: Validator
} => {
  return (email !== null && validator.isEmail(email)) || (phone !== null && validator.isPhone(phone)) || ( address !== null && validator.isAddress(address));
};
```

以及它的改进版本:

```
const isFormValid = {
  email: string,
  phone: string,
  address: string,
  validator: Validator
} => {
  const isMailValid = email !== null && validator.isEmail(email);
  const isPhoneValid = phone !== null && validator.isPhone(phone);
  const isAddressValid = address !== null && validator.isAddress(address);
  return isMailValid || isPhoneValid || isAddressValid;
};
```

你认为哪个更好？

## 避免多重嵌套

看看下面的代码:

```
class FizzBuzz { getWordForNumber = (number: number): string => {
    if (number % 3 === 0 || number % 5 === 0) {
      if (number % 3 === 0 ) {
        if (number % 5 === 0 ) {
          return 'FizzBuzz';
        }
        return 'Fizz';
      } else {
        return 'Buzz';
      }
    } else {
      return number.toString();
    }
  }
};
```

有点吓人——你不觉得吗？分析这种结构并不容易——你必须专注于理解所有的条件。但是如果稍微改变一下逻辑，用快速返回法呢？

```
class FizzBuzz { getWordForNumber = (number: number): string => {
    if (number % 3 === 0 && number % 5 === 0) return 'Fizzbuzz';
    if (number % 3 === 0) return 'Fizz';
    if (number % 5 === 0) return 'Buzz';
    return number.toString();
  };
}
```

好多了，你不觉得吗？代码更短，更容易理解。此外，创建测试场景要容易得多。这个简单的方法在您的日常编码中非常有用——当您看到多个嵌套结构时，花几分钟想想是否可以使用快速返回方法来简化它。

![](img/f05a4c6b7047b6b03749168d80e24d99.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 拍摄

## 使用内置的方法和结构

你不必从头开始构建一切。如果你使用的编程语言提供了更复杂的数据结构/模式的实现，就使用它们。它们被所有使用给定语言的人很好地测试过，并被所有开发人员所熟知。不要重新发明轮子——只使用给你的东西。

## 移除“黑客”

下面的代码片段是我见过的最有创意的代码之一:

```
const isEven = (number: number): boolean => {
  const regex = /^-?\d*[02468]$/;
  return regex.test(number.toString());
};
```

是的，它有效。但是——这真的是检查一个数字是否是偶数的最好方法吗？我不这么认为。正则表达式对错误非常敏感——人类理解起来并不自然。不要过度渲染——最简单的解决方案是最好的！

```
const isEven = (number: number): boolan => {
  return number % 2 === 0;
};
```

当然，这个例子是简化的，但是这个技巧在实际代码中也是有效的。想想你的代码——你的代码中有没有任何 T4 式的或*技巧*？是时候和他们说再见了。

![](img/862fe1e6c719928909a674bf263ffee0.png)

照片由 [Gary Chan](https://unsplash.com/@gary_at_unsplash?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 删除不需要的代码

没有比空卡更干净的了，不是吗？没有代码意味着没有 bug，没有遗留问题，没有安全漏洞。那么，为什么您仍然将未使用的代码保存在库中呢？即使它被注释了，它也使得阅读和理解代码变得更加困难。下一个开发者可能害怕移除它，因为他们不知道它有多重要。只需删除它——这些条目仍将存在于您的 git 文件中，因此您可以在需要时轻松地恢复它们。

最后一个提示——可能最值得记住的是:

> “编写代码时，总是把最终维护你代码的人想象成一个知道你住在哪里的暴力精神病患者”~约翰·伍兹

# 最后上诉

总而言之，我对你有些吸引力。一位亲爱的程序员，他写的遗留代码我现在还在纠结。亲爱的同事或开源代码贡献者。亲爱的每一个参与软件工程过程的人。我对你有一个巨大的请求——请尊重我和你的时间。让我们专注于重要的任务，而不是试图理解你过度设计的代码。别让我思考——简单点，笨蛋！