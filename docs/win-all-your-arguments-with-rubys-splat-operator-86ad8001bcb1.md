# 用 Ruby 的 Splat 操作符赢得所有的争论

> 原文：<https://medium.com/codex/win-all-your-arguments-with-rubys-splat-operator-86ad8001bcb1?source=collection_archive---------16----------------------->

![](img/cd688a52059e6f21761bedd17d4d2f75.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

任何学习 Ruby(和许多其他语言)的人都明白的第一件事就是参数和自变量是程序的生命线。它们允许我们的方法是动态的，能够用抽象的数据集执行任务，而不是硬编码成特定的数据。随着程序范围的扩大，您可能会发现您的方法需要越来越多的参数才能发挥作用。在下面的简单例子中，如果我们试图将下面的数组传递到我们的方法中，我们必须以某种方式访问字符串，这有很多选择。

```
my_fav_colors = ["green", "purple", "blue"]def three_fav_colors(color1, color2, color3) puts "My three favorite colors are #{color1}, #{color2}, and
  #.{color3}."end
```

我们可能会创建三个新变量，然后插入这些变量(可能效率最低):

```
fav_color1 = my_fav_colors[0]
fav_color2 = my_fav_colors[1]
fav_color3 = my_fav_colors[2]three_fav_colors(fav_color1, fav_color2, fav_color3)
=> "My three favorite colors are green, purple, and blue."
```

我们可以直接插入数组索引，这是一种稍微简洁的方法:

```
three_fav_colors(my_fav_colors[0], my_fav_colors[1], my_fav_colors[2])
=> "My three favorite colors are green, purple, and blue."
```

通常在 ruby 中，当我们操作数组时，我们可以对它们进行枚举。然而，这里我们只想输出一个句子，因此任何枚举都需要对原始方法进行笨拙的重构。

输入 splat 运算符:

```
three_fav_colors(*my_fav_colors)
=> "My three favorite colors are green, purple, and blue."
```

通过将*放在数组前面，我们告诉该方法将每个索引元素变成一个参数。它很干净，没有添加任何额外的代码行，读起来也很好。但是最精彩的部分来了:使用*不仅仅是将数组转换成参数。

比如说我们的程序收集了人们最喜欢的颜色，有人输入了五种颜色。通常情况下，我们必须想办法丢弃多余的数据。但是有了*之后，我们可以让我们的方法变得更加智能，忽略任何不需要的数据:

```
my_fav_colors = ["green", "purple", "blue", "yellow", "red"]def three_fav_colors_test(color1, color2, color3)puts "My three favorite colors are #{color1}, #{color2}, and #{color3}."end three_fav_colors(*my_fav_colors)=> "My three favorite colors are green, purple, and blue."
```

Ruby 通常会抱怨传递了太多的参数，但是通过将*作为第四个参数，我们的方法将接受数组的前三个元素，并忽略后面的所有内容。现在，我们不需要编写任何代码来缩减数组，只需要一个字符(如果算上可信的逗号，就是两个)。

然而，兔子洞并没有就此结束；事实上，这仅仅是个开始。

Splat 参数是可选的，因此如果使用 splat 参数的方法没有收到任何参数，它不会引发错误:

```
def meetings(*times)
    times.each do |time|
        puts "I have a meeting at #{time}."
    end
endmeetings
=>nil
```

我们可以创建接受任意数量参数的方法:

```
def meetings(*times, day)
    times.each do |time|
        puts "I have a meeting at #{time} on #{day}."
    end
endmeetings("3:00", "4:15", "8:00", "Wednesday")
=>"I have a meeting at 3:00 on Wednesday.
"I have a meeting at 4:15 on Wednesday.
"I have a meeting at 8:00 on Wednesday.
```

上面还显示了 splat 可以放在参数列表中的任何地方。我们可以用它一次动态设置多个变量(感谢 [freeCodeCamp](https://www.freecodecamp.org/news/rubys-splat-and-double-splat-operators-ceb753329a78/) ):

```
first, *middle, last = [1,2,3,4,5]first = 1
middle = [2,3,4]
last = 5
```

它甚至可以用来将给定的对象转换成一个数组( [source](https://www.honeybadger.io/blog/ruby-splat-array-manipulation-destructuring/) )。它也适用于哈希:

```
array = *"pizza"array
=>["pizza"]array = *{tango: "Argentina", ballet: "France"}
array
=>[[:tango, "Argentina"], [:ballet, "France"]]
```

我们得到的散列数组很奇怪，但是有另一个工具使用总是好的。

使用 splat 操作符是另一种 ruby 技术，虽然不是必需的，但它打开了一个全新的快捷方式世界。它可以让你的方法更聪明，更抽象，更不容易出错。我将在这里进入 doublesplat (**)操作符(它的行为与*非常相似，只是它专门处理散列),但是[这篇文章](/@sologoubalex/parameter-with-double-splat-operator-in-ruby-d944d234de34)确实公正，我已经让你在这里呆了一段时间了。

现在前进，赢得这些争论。编码快乐！

![](img/382af0c9d757ee08667c194a55aee5cf.png)