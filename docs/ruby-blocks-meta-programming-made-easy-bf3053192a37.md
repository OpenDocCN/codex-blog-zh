# Ruby Blocks:初学者元编程教程

> 原文：<https://medium.com/codex/ruby-blocks-meta-programming-made-easy-bf3053192a37?source=collection_archive---------12----------------------->

![](img/c1fd0e337d4662866e642189d2cce15f.png)

照片由[内森·达克](https://unsplash.com/@nvte?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/blocks-red?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

## 什么是积木？

在 [Ruby](https://rubyapi.org/) 中，块是一个匿名函数，作为方法/函数的参数传递。通过使用块，函数和类方法可以有更多的用例。当我们调用一个块时，我们实际上是在编写一段代码，这段代码正在编写(重新调整用途)一个函数。

有些人可能认为这是有争议的，但是块是元编程的一个例子。它们允许我们编写一个重新定义函数源代码的程序。块防止编程冗余，而不是写一百万个不同的方法，我们可以写一个方法并传递给它无限个块。那么我们如何使用积木呢？

如果你喜欢通过视频学习，看看我在 blocks 上制作的视频 [**这里**](https://www.youtube.com/watch?v=DuTaOGvfMdA)**&**[**这里**](https://www.youtube.com/watch?v=XPSKvHMTRIE) **！**

## 使用块

编写代码块有两种方法:使用花括号语法或 do/end 语句。花括号语法最适用于只有一行代码的程序块，而 do/end 结构最适用于较长的多行程序。就像函数一样，它们也可以接受参数。参数用竖线(||)字符括起来，并用逗号分隔。

**each** 方法允许我们迭代列表对象中的每个元素。each 方法接受一个带有参数的块，在本例中，它被命名为 word。这个参数将代表当前被迭代的元素，在每次迭代中我们的块将被调用。

```
# One Liner == Braces syntax %w[coreys corner podcast].each {|word| puts word}# Multiline == do/end %w[the best podcast around].each do |word|
   puts word 
   puts word.capitalize
   puts "#{word} includes hog!!" if word.include? "hog"
end 
```

## **基于书写块的功能**

如果编写代码块是将代码传递给函数的一种方式，为什么我们不能将代码作为参数传递给函数呢？我们可以传递一行代码作为函数的参数。让我们这样做，看看会发生什么。

```
def does_not_work(code)
   puts "Inside of function"
   code # does not print in proper order 
   puts "Inside of function
end
does_not_work(puts "hello")>> hello
>> Inside of function
>> Inside of function
```

正如你所看到的，程序并没有像我们预期的那样运行。将代码行作为参数传递是行不通的，这也是我们需要使用块的原因之一。即使我们可以把一行代码作为参数传入，并且成功了，那也意味着我们需要为每一行代码创建一个参数，我们想用它作为参数。这种假设场景显然非常麻烦、不动态，并且可能导致错误。块使得在一个函数中动态执行多行代码成为可能。

让我们写一个函数，它将一个块作为参数，在这个例子中，我们将隐式地这样做，我们的块在函数参数中不是一个命名的参数。在下面的例子中，我们使用 **yield** 关键字来运行/调用/执行程序块。

```
# Implicit block with yield as call method 
def takes_a_block
   puts "Inside of function"
   yield
   puts "Inside of function
"endtakes_a_block {puts "Inside of block"}takes_a_block do 
   puts "Hey I'm inside of a block'
   puts "Wow this is so cool" 
end 
```

如果我们想要明确地指定一个块作为一个命名的函数参数，我们可以简单地用&符号来定义它，如下所示。

```
def my_func(&my_block) #Explicitly defined block
   my_block.call # Equivalent to yield 
end
```

我们不使用 yield 来运行这个块，而是使用 call 方法来执行它。除了我们只能在显式定义的块上使用 call 方法之外，使用 yield 或 call 没有区别。

yield 关键字可以在函数中使用任意多次。让一个块接受一个参数，我们要做的就是把它作为一个参数传递给 yield。传递给 yield 的任何参数都将被视为我们在块中指定的参数。省略参数或包含多个参数不会导致错误，但是多余的参数将被视为 nil 对象。

```
def some_data_types(&block)
   yield :symbol
   yield "string"
   yield(1)
   block.call([])
   block.call {}
end # n represents :symbol, "string" etc as our block is calledsome_data_types { |n| puts "#{n} is of type #{n.class}" }some_data_types do |n, x| #extra arg does not cause error
   puts "#{*n*} responds to .each_byte" if *n*.respond_to? :each_byte
   puts "#{*n*}.object_id == #{*n*.object_id}"
   p x # extra arg treated as nil
end
```

## 动态块

block_given？方法有助于防止程序崩溃，尤其是在处理隐式块时。如果一个块被传递给我们的函数 block_given 返回 true，否则返回 false，这允许我们动态地调用 yield。

```
def foo
   if block_given? then yield else puts "No block given" end
endfoo 
>> "No Block Given"foo "The Corey's Corner Podcast is crazy" 
>> "The Corey's Corner Podcast is crazy"
```

总而言之，我们已经知道，块是一种动态编写匿名函数并将它们作为参数传递给函数/方法的方式。它们使得 Ruby 语言更加灵活和动态，这使得我们更容易用高抽象级别编程。

外星人存在吗？看看[科里的角落](https://anchor.fm/coreys-corner)就知道了…

[Ruby on Rails 电子商务教程](https://www.youtube.com/watch?v=gAXs3xhfHVg)

[用 Ruby 对 SQL 命令进行元编程](https://www.youtube.com/watch?v=CiWkUQt4YEM)🔥

被束缚🍳->[https://thoughtsandfitness.com](https://thoughtsandfitness.com)
加德纳 App 开发->[https://gardnerappdev.com](https://gardnerappdev.com)