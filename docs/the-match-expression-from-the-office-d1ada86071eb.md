# 在我发现这个之前，我一直停留在过去

> 原文：<https://medium.com/codex/the-match-expression-from-the-office-d1ada86071eb?source=collection_archive---------19----------------------->

## 2022 年写条件的新方法

![](img/a2f0756388149dd97719d5d2fd541fb5.png)

肖恩·西姆在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 为什么是 PHP 8？

2020 年 11 月 26 日，核心 PHP 团队发布了该语言最新、最快、更好的版本。

在所有令人惊叹的新特性和关键字中，比如**构造函数属性提升、Nullsafe 操作符**等等，有一个对大多数开发人员来说非常突出。

我说的是比赛表情

# 匹配表达式

可以把匹配表达式看作是 switch 语句的一种发展。

它所做的是**评估一个主题表达式，并根据情况，分支到不同的块或代码或语句**。

与 switch 不同，这种比较是一种身份检查(===)，而不是弱相等检查(==)。

```
$food = 'cake';

$returnValue = match ($food) {
    'apple' => 'This food is an apple',
    'bar' => 'This food is a bar',
    'cake' => 'This food is a cake',
};

var_dump($returnValue);
// string(19) "This food is a cake"
```

# 为什么要用？

使用这种语言的新特性有许多积极的方面。

例如，使用 switch 语句最烦人的事情之一就是我们不能忘记使用来中断代码块。

不再休息！

如果我们不这样做，代码将在下面的情况下继续运行。

这当然会导致错误和意想不到的结果。

```
$i = 0;switch ($i) {
    case 0:
        echo "i equals 0";       
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
}"i equals 0"
"i equals 1"
```

匹配表达式返回值:

在上面的例子中你已经看到了，match 块的内部看起来像一个经典数组，**匹配条件的值是实际返回到变量**中的值。

只要条件表达式为真，匹配表达式也可以处理非同一性条件情况。

这里有一个例子。

```
$result = match (true) {
    $age >= 65 => 'senior',
    $age >= 25 => 'adult',
    $age >= 18 => 'young adult',
    default => 'kid',
};
```

像开关一样，任何臂都可以包含由逗号分隔的多个表达式。

```
$result = match ($x) {
    // This match arm:
    $a, $b, $c => 5,
    // Is equivalent to these three match arms:
    $a => 5,
    $b => 5,
    $c => 5,
};
```

从开关继承的另一个特性是默认情况。

```
$expressionResult = match ($condition) {
    1, 3 => getFromEvens(),
    2, 4 => getFromOdds(),
    default => GetAll(),
};
```

如果条件与之前的任何情况(1、2、3 或 4)都不相同，那么只有在那时才会调用函数`GetAll()`。

不要放入多个默认条件，因为它会抛出一个错误！

处理这个问题的一个更好的方法是将表达式包装在一个 try-catch 块中

```
$condition = 5;

try {
    match ($condition) {
        1, 2 => foo(),
        3, 4 => bar(),
    };
} catch (UnhandledMatchError $e) {
    var_dump($e);
}
```

# 骗局

使用它并没有什么坏处。

在我看来，该语法比 switch 语句中的语法更干净，并且它以更优雅的方式处理结果。

我不使用它的唯一原因是万一我只需要两三箱。

在这种情况下，我可能会选择几个单一的 if 语句。

# 你的观点是什么？

你已经使用匹配表达式了吗？

它的使用案例是什么？

在下面的评论中展示你的例子。

![](img/b416d3a2fd7b97fc207e1c01ae3969b2.png)

# 结论

我已经做了将近十年的网页开发员了。

与位于伦敦市中心的世界级企业和屡获殊荣的营销机构合作。

此外，我在我的博客和在线社区上写文章和教程，并帮助企业在网上建立他们的存在。

[点击这里阅读超过 100+的我的博文](https://anastasionico.uk/blog)