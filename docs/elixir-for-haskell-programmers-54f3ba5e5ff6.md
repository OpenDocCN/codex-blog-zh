# Haskell 程序员的灵丹妙药

> 原文：<https://medium.com/codex/elixir-for-haskell-programmers-54f3ba5e5ff6?source=collection_archive---------9----------------------->

嗯，你可能在想 ***“为什么 Haskell 开发者会对 Elixir 感兴趣？”*** 或 ***“世界上没有多少 Haskell 开发人员，所以本指南不会很有用”*** ，主要原因是我只是想更多地谈论 elixir 以及与 Haskell 的差异和相似之处，而不考虑这种材料的未来用途，此外，Elixir 是一种非常好的语言，并且有一个充满活力的社区，激励社区为传播 Elixir 这个词做出贡献。以及“ ***为什么不为 Elixir 程序员设计一个 Haskell？”*** ，因为 Haskell 已经是一门非常成熟的语言，有着令人难以置信的课程，甚至还有更令人难以置信的教授，我不知道我可以在哪里插入一些新的东西，而 Elixir 仍然是一门非常年轻的语言，有许多事情要做，以传播这种语言并使社区变得更强大。

既然我已经恰当地介绍了原因，让我们从基础开始:

![](img/beb24cc3af9cc0f6df50fcb317f8c2ec.png)

[Arjun MJ](https://unsplash.com/@arjun_mj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

W 什么是仙丹？

Elixir 是一种运行在 Erlang 虚拟机上的函数式语言，由 José Valim 在前 plataformatech(已被 nubank 收购)创建，同时，Elixir 受到 Ruby(José Valim 曾经是 rails 核心团队的成员)、Clojure(Valim 决定应用宏来使语言可扩展)和 Erlang(这种风格的并发行为者模型更容易被人类理解，并且 Erlang VM 的所有功能都是容错和自愈的)的极大启发。

## 最基本的

如果不强调函数，这就不是一个合适的函数式语言介绍，现在让我们来深入了解一下 elixir 函数的语法:

```
defmodule Example do 
  def hi() do 
    IO.puts "Hello World"
  end
endExample.hi()
```

为了能够在 Elixir 中使用模块函数，我需要首先创建一个模块( ***defmodule*** )，然后定义我的函数(用 ***def*** 关键字)，为了执行，我只需运行 ***Example.hi()*** (在 [repl](https://replit.com/languages/elixir) 上键入这段代码，然后点击 run，应该会显示 hello world)。

如果你认为该语法有一些类似 c 的代码，你猜对了，这是 Ruby 语言的灵感，我们必须打开和关闭块，但稍后我们会看到一些不键入这些块的方法。

Haskell 中的等价物

```
module Examplehi = print "Hello World"
```

## 匿名函数

与 Haskell 相反，Elixir 区分了模块函数和匿名函数，而在 Haskell 中两者是平等的。

Haskell 中的示例

```
domath x = x + 3-- is equal to(\x -> x+3)example = do
  print $ map (domath)    [4] -- outputs [7]
  print $ map (\x -> x+3) [4] -- outputs [7]
```

而长生不老药的等效性是不可能的:

```
defmodule MathMod do 
  def do_math(x) do 
    x+3
  end def example() do 
    IO.inspect Enum.map([4], do_math)
  end
endMathMod.hi(4) # outputs 7
MathMod.example() # outputs an error because passing module functons as a parameter generates an error
```

为了能够将函数作为参数传递，我需要像这样指定一个匿名函数:

```
defmodule MathMod do 
  def do_math(x) do 
    x+3
  end def example() do 
    IO.inspect Enum.map([4], fn x -> x+3 end)
  end
endMathMod.example() # outputs [7]
```

此外，为了区分函数，Elixir 没有自动编译，所以我们必须手动编译我们的函数，并且只能编译匿名函数:

```
anFunc = fn x -> fn y -> x+y end endIO.puts anFunc.(3).(4) # outputs 7
```

## 缩短我们的功能

正如我之前提到的，我们可以通过不编写一些开闭父类或代码块来节省一些时间:

```
defmodule MathMod do 
  def do_math(x) do 
    x+3
  end
end# is equal todefmodule MathMod do 
  def do_math(x), do: x+3
end
```

此外:

```
defmodule MathMod do 
  def example() do 
    IO.inspect(Enum.map([4], fn x -> x+3 end))
  end
end# is equal todefmodule MathMod do 
  def example, do: IO.inspect Enum.map([4], &(&1+3)) 
#notice that &1 
# is an alias for the first parameter(the x) if our function had 
# more parameters the alias would be &2, &3...&n
end
```

咻，我认为对于第一口 Elixir，我们涵盖了一堆东西，可能这不是对初学者最友好的开始方式，但如果你熟悉 Haskell，我试图让它尽可能地熟悉，我不知道我是否会继续并将它变成一个系列，但我仍然希望以某种方式为你的学习之旅做出贡献。

感谢阅读！再见:)