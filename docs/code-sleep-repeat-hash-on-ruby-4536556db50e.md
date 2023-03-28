# Ruby 上的代码、睡眠、重复—哈希

> 原文：<https://medium.com/codex/code-sleep-repeat-hash-on-ruby-4536556db50e?source=collection_archive---------11----------------------->

这篇博客文章摘自一篇最初写于 2020 年 4 月 15 日的博客作业。在这篇博客中，我解释了我是如何克服 Flatiron 的 Sinatra 阶段的一个困难实验室的。

也许你需要一些动力，我很高兴让你知道你并不孤单，我们都经常遭受一些骗子综合症的困扰。如果你真的只是需要了解 Ruby 散列，点击这个超链接直接进入文档。虽然我认为这个博客上的示例代码很有意义，但它应该附有文档。一旦你理解了这个主题，你可以继续练习面向对象编程，你会搞定的。

尽情享受吧！

是每月的那个时候，做那个过期的博客作业的日子。这个月，我会谈谈我在辛纳屈的经历。这很好。事情变得越来越令人兴奋，过去的一周很顺利，就像流畅的爵士乐或传奇本身。

![](img/607a4cc8971742dd2db9912758cd5a8a.png)

几周前，我充分利用我的商业智能经验(或者我应该说熟悉 SQL)来学习这些课程。但是，我跑得太快了，撞到了一面叫 ActiveRecord 的墙。这对我来说是件新鲜事。虽然这很简单，但我们确实需要对目前所学的内容感到满意。这就是 CRUD 创建、读取、更新、删除)操作发挥作用的地方。CRUD 实验室包括在 Ruby 上实例化和创建数据的练习。例如:

```
def can_be_created_with_a_hash_of_attributes
  # Initialize movie and then and save it
  attributes = {
      title: "The Sting",
      release_date: 1973,
      director: "George Roy Hill",
      lead: "Paul Newman",
      in_theaters: false
  }
  movie = Movie.find_by(attributes)  
end
```

为了解决故障，我们需要知道我们在以前的 Ruby 课程中学到了什么。假设我们必须开发一个 CLI 程序，这应该是一个简单的程序。对我来说不是。有一个具有挑战性和难度的练习。如果有争论的话，单独创建圣诞经典家居；如果没有争论的话，创建邪教经典房间。

有时我尝试，它返回一个错误，期待的房间。这让我沮丧地大喊:

![](img/27744809a27ff1b8f0a225501e2a1d82.png)

然后，我决定换一种方式试试。也许加上那个独自在家的论点…

![](img/350f3321170191a32be066d56593cbb0.png)

没错，我很沮丧。结果我需要做点什么，重复。在实现正确的代码之前，我需要检查带有对值的散列。这个故事的寓意是重复，再重复，再重复一些。回顾主题，再次编码，用这些代码玩一会儿。这是解决问题的正确代码:

```
def can_be_created_in_a_block(m = {title: "Home Alone", release_date: 1990})
    movie = Movie.create do |mov|
    mov.title = m[:title]  
    mov.release_date = m[:release_date]   
    mov.save 
  end	
end
```

这也将创造出《孤独的家》和令人惊叹的烂片《房间》。记住新手伙伴，学习代码并不断练习。重复是从我们所学的东西中获取价值的关键。