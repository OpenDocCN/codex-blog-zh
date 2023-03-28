# 特质，我如何使用它们来改进我的代码(PHP)

> 原文：<https://medium.com/codex/trait-how-i-use-them-to-improve-my-code-php-41e676c2c509?source=collection_archive---------10----------------------->

## **用这 5 个技巧变得令人敬畏**

![](img/e7406954baf526da2f961fe5b218c53e.png)

由[朱迪·库克](https://unsplash.com/@jodiecook?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

嗨，我是尼科，

我是伦敦(英国)一家票务经纪公司的高级工程师。

你在办公室看书！

这是我每天(几乎)例行发布的日志，记录我在办公室的日常生活中使用的技术。

这个帖子是给谁的？

在这篇文章中，你将学习特征、面向对象编程和继承。

如果你还没到那一步，不用担心，你可以这样开始:

[](https://anastasionico.uk/blog/the-complete-guide-to-object-oriented-programming) [## 面向对象编程完全指南

### 学习面向对象编程是 web 开发人员可以学习的最有价值的技能之一。例如…

anastasionico.uk](https://anastasionico.uk/blog/the-complete-guide-to-object-oriented-programming) 

如果你想挑战你的技能，点击这里

[](https://anastasionico.uk/blog/domain-driven-design-quickest-the-basics) [## 领域驱动的设计最快{基础}

### 让我对你做一个小小的猜测，你浏览这个网站并正在阅读这句话的原因是…

anastasionico.uk](https://anastasionico.uk/blog/domain-driven-design-quickest-the-basics) 

# 问题是

如果你对面向对象编程有所了解，你肯定会遇到继承和接口这样的世界。

在 PHP 类不支持多重继承的情况下，接口可以帮助你解决这个问题。

假设一个类只能扩展一个父类，那么你必须使用和实现尽可能多的接口来创建复杂的系统。

但是，

如果您需要跨继承层次结构共享一个实现，该怎么办？

这就是特征出现的原因，现在我将向你解释如何使用它们。

考虑一个类似抽象类的特征，它有一个真正相似的结构，并且不能被自己实例化。但是你可以把它合并到一个具体的类中。

**trait 定义的任何方法在使用它的类中都自动可用。**

事实上，

你应该把一个特征看作是类的包含但是。

**一种特质能解决什么问题？**

作为一个高效的人，我喜欢记录我每天能写多少字。

让我们想象一下，我已经开发了一个小应用程序，它为我计算它们，并告诉我是否已经做得很好，或者我是否需要更加努力。

让我们也说一下，我目前正在写这篇关于编程的文章和一本关于兽人、巫师等等的奇幻书。

我的项目有两节课:一节是关于书的:

```
class BookWriting 
{...public function addWordsCount(int $todaysWords)
    {
        return $this->wordsCount += $todaysWords;} 

    public function getHeroName(){...}...}
```

一张写在博客上。

```
class ArticleWriting 
{...public function addWordsCount(int $todaysWords)
    {
        return $this->wordsCount += $todaysWords;} 

    public function PostCategory(){...}...}
```

你可以看到我项目的代码库有一个很大的问题，

两个看起来一样，做同样事情的方法在两个类中，尽管很相似，但彼此没有任何关系。

> "复制可能是软件中所有罪恶的根源."
> ―罗伯特·c·马丁，干净代码收集

# 为什么是特质？

嗯，特征为这个问题提供了一个快速可靠的(注意我没有说完美或优雅)解决方案。

特征的目的是**封装特定的行为，并在多个类**需要时使其可用。

```
trait CountWordsUtilityTrait
{ 
    public function addWordsCount(int $todaysWords)
    {
        return $this->wordsCount += $todaysWords;
    }
}
```

这个方法现在可以使用了。

```
class BookWriting 
{
    use CountWordsUtilityTrait;
}class ArticleWriting 
{
    use CountWordsUtilityTrait;
}
```

两个类都可以像以前一样使用 *addWordsCount()* 方法，但是现在它只出现在一个地方。

如果我们需要在一个月后更新代码，这将是一个巨大的优势。

# 提示和技巧

![](img/4140b9ae0d0ee81306e72af4f9b851c3.png)

照片由[艾哈迈德·谢拉兹](https://unsplash.com/@sheraz_official__?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**使用一个以上的特征**

正如我们所说的，我们拥有特征的原因之一是为了解决语言的单继承限制。

这意味着如果我们需要的话，我们可以使用更多的特征。

```
trait PaginatorTrait
{ 
    public function getPageNumber(): int
    {
        return $this->pageNumber;
    }
}class BookWriting 
{
    use CountWordsUtilityTrait, PaginatorTrait;
}
```

现在， *BookWriting* 类可以访问 *addWordsCount()* 方法和 *getPageNumber()* 方法。

**特性和接口**

没有人说当一个类也实现一个接口时我们不能使用特征。

PHP 的限制只是针对我们要继承的类。

这意味着你实际上可以这样做:

```
class ArticleWriting implements Commentable
{
    use CountWordsUtilityTrait;
}
```

**关键字**的代替

我已经在上面展示了我们可以在一个类中结合两个或更多的特征，这很好，在主域之外有更多的功能。

当这些特征有一个称为相同方式的方法时，就会出现一个问题。

我们的类或 PHP 引擎将如何理解在每种情况下使用哪种方法？

对我来说，这可能是个错误。

嗯，

不完全是。

作为一名 PHP 开发人员，你可能遇到过的函数*instance，该语言还提供了另一个名为*的关键字来代替*。*

这个关键字让我们指定类应该如何在特征的方法之间进行选择。

它是这样工作的:

```
trait PaginatorTrait
{ 
    public function getPageNumber(): int
    {
        return $this->pageNumber;
    }
}trait PublishedDataTrait
{ 
    public function getPageNumber(): int
    {
        return $this->publisher->getNumberOfPage();
    }
}class BookWriting 
{
    use PaginatorTrait, PublishedDataTrait;
}
```

这将导致 PHP 致命错误，因为解释器不知道使用哪个 *getPageNumber()* 。

```
class BookWriting 
{
    use PaginatorTrait, PublishedDataTrait {
        PublishedDataTrait::getPageNumber instreadof PaginatorTrait;
    }
}
```

用那一行，你基本上是在说使用*publisheddatatatrait*的 *getPageNumber()* 方法，而不是 PaginatorTrait 的同名方法。

**走样方法**

如果你仔细阅读了它，你可能现在就想出了一个问题。

如果我们也想使用 PaginatorTrait::getPageNumber 呢？

记住， *instreadof* 的目标不是使用一种方法而不是另一种方法，

我们的目标是避免代码中的冲突和致命错误。

解决办法很简单。

我们所做的只是为剩下的函数创建一个别名。

```
class BookWriting 
{
    use PaginatorTrait, PublishedDataTrait {
        PublishedDataTrait::getPageNumber instreadof PaginatorTrait;
        PaginatorTrait::getPageNumber as getPaginatorPage
    }
}$book = new BookWriting();
echo $book->getPageNumber();    // this refers to PublishedDataTrait
echo $book->getPaginatorPage(); // this refers to PaginatorTrait
```

注意，仅仅给一个或另一个特征的方法起别名是不够的。

你需要先用*代替 of 来表示优先级。*

准备好今天的最后一个小把戏了吗？

**用别名**更改访问修饰符

您已经看到了 alias 是如何工作的，现在应该很清楚了。

别名也可以有另一个特殊的功能。

它可以动态地改变特征的访问修饰符。

让我们看看这一点是如何以及为什么重要。

```
trait PublishedDataTrait
{ 
    public function getPublisherId(): string
    {
        return $this->publisher->getId();
    }
}class BookService 
{
    use PublishedDataTrait {
        PublishedDataTrait::getPublisherId as private
    }public function getPublisherData()
    {
        return $this->getPublisherId()
    }
}$book = new BookService();
echo $book->getPublisherData();  // this will return the ID 
echo $book->getPublisherId();    // this is an Error: Call to private method
```

特质是做给很多职业用的，不只是同一职业在同一水平。

它们中的一些可能直接与控制器通信，并提供诸如 id 之类的敏感细节。

通过指定访问修饰符(public、protected 和 private 中的一个),使用该特征的方法的类可以修改其行为。

为什么这很重要？

> *面向对象程序设计的开闭原则说，对象应该对扩展开放，但对修改关闭。换句话说，您应该能够在不更改模块的情况下更改模块的功能。在 OO 中，当您保护您要扩展的代码不被修改时，可扩展性可以得到最好的实现。鲍勃叔叔*

不必担心类在哪里被调用，因为它是私有的，这使我们的生活更容易，并让我们晚上睡得更好

# 你的观点是什么？

你是如何发现特质的？

这是你经常使用的功能吗？

在下面的评论中写下你的想法。

![](img/b416d3a2fd7b97fc207e1c01ae3969b2.png)

# 结论

我已经做了将近十年的网页开发员了。

与位于伦敦市中心的世界级企业和屡获殊荣的营销机构合作。

此外，我在我的博客和在线社区上写文章和教程。

[点击这里阅读我 100 多篇博文](https://anastasionico.uk/blog)