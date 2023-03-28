# Java 8+中的 FizzBuzz

> 原文：<https://medium.com/codex/fizzbuzz-in-java-8-aa70b3038736?source=collection_archive---------12----------------------->

![](img/f48ec7e401a800a2c36f91a3b10c20ca.png)

布莱恩·麦高恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

FizzBuzz 是编码面试的主流任务之一。自从 Java 8 以来，不用任何循环语句就可以解决这个问题。任务看起来是这样的:

> 您必须打印从`1`到`N`的整数。但是如果整数被 3 整除，就用`Fizz`代替。并且如果除以 5 就用`Buzz`代替。如果它能被 3 和 5 整除，就用`FizzBuzz`代替。

所以你必须打印出`Fizz`，而不是像 3、6、9 这样的数字。而不是 5，10，20 — `Buzz`。而不是 15，30，45 — `FizzBuzz`。

## 输出示例

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
```

# 经典 FizzBuzz Java 解决方案

在继续之前，我想先看看 Java 上 FizzBuzz 问题的“经典”解决方案。这可能会有点混乱，因为许多`if`语句都有重复的模运算符。但是谁在乎呢？注意`if`的顺序。打乱顺序可能会导致不正确的行为。

# 使用 Java 流 API 的 FizzBuzz

![](img/a08b27dd6990702ea065d2048ac72eb8.png)

照片由[卡里姆·萨基布加列夫](https://unsplash.com/@karimsan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我用构建在 stream API 上的循环替换了`for`循环。此外，我将`if`语句移到了一个新方法中，以免在`mapToObj`中使用巨大的 lambda 造成混乱。

因此，`IntStream.rangeClosed(1, n)`创建了一个从 1 到 n 的整数流。然后`mapToObj(FizzBuzz::mapIntToFizzBuzz)`对每个整数`mapIntToFizzBuzz`执行，并用第二个方法的输出替换该整数。这个方法的输出是一个带有整数本身或者一些嘶嘶声的字符串。最后`forEach(System.out::println)`对流中的每个字符串执行标准输出的打印。一般来说，自经典解决方案以来没有任何变化。

别忘了 Java 中的流是懒惰的。在执行任何像`forEach`这样的终端方法之前，什么都不会发生。

# 编后记

FizzBuzz 与 Java 流看起来像过度工程。但这是自 Java 8 以来解决 FizzBuzz 任务的一种全新方法。顺便说一下，Java 流 API 本身是一个很大的主题。

不要忘记关注我，以免错过接下来的文章，感谢您的宝贵时间！