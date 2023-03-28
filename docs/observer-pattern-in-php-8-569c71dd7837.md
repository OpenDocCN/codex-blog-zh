# PHP 8+中的观察者模式

> 原文：<https://medium.com/codex/observer-pattern-in-php-8-569c71dd7837?source=collection_archive---------3----------------------->

![](img/5494673a982148dc7234a6807f21746e.png)

图片来自 [Pixabay](https://pixabay.com/pt/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1209011) 的[免费照片](https://pixabay.com/photos/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1209011)

在 PHP 8+中实现观察者模式的最简单指南

PHP 世界中最常用的设计模式之一是**观察器**，当我们有几个依赖于另一个对象的对象，并且需要在那个对象的状态改变时执行一个动作，或者一个对象需要通知其他人而不知道他们是谁或者有多少人时，这非常有用。

# 🧐观察者设计模式

**观察者设计模式**是一种行为设计模式，它让一个对象在事件发生时通知其他对象，比如状态改变或方法执行。

这个模式由一个名为**可观察**的对象(或某些来源中的**主体** / **发布者**)和许多其他名为**观察者**(或**订阅者**)的对象组成。**可观察对象**拥有一个**观察对象**的列表作为属性，并自动通知它们任何状态变化，通常调用一个观察对象的方法。

观察者模式通常用于在应用程序**中实现事件处理程序，而不会在主类和事件监听器的对象**之间创建依赖关系。

# 👷‍♂️实施

在我们的例子中，我们将创建一个简单的系统，它可以创建用户并将他们的数据存储在内存中。然后我们将在每次创建用户时实现一个通知

首先，让我们创建一个只有名称和电子邮件属性的用户类。

```
class **User**
{
    public function __construct(
        private string $name,
        private string $email
    ) {}
}
```

然后，让我们创建一个*用户存储库*来存储和处理用户数据:

> UsersRepository 类将实现**单例模式**,以保证数据被正确持久化

```
class **UsersRepository**
{
    private array $users = []; private static $instance; private function __construct() {}
    private function __clone() {} public function **createUser**(*User* $user)
    {
        *$this*->users[] = $user;
    } public static function **getInstance**()
    {
        if(*self*::$instance === null) {
            self::$instance = new self;
        }
        return self::$instance;
    }
}
```

PHP 有内置的接口来帮助我们实现观察者设计模式。为了实现**可观察的**对象，我们可以使用[**spl subject**](https://php.net/manual/en/class.splsubject.php)**接口，对于**观察者**，我们可以使用[**spl observer**](https://www.php.net/manual/en/class.splobserver.php)**接口。****

****因此，我们将在 *UsersRepository* 类上实现 **SplSubject** ，这样，当您的状态改变时，该类将能够通知其他类。****

> ******SplSubject** 接口指定我们必须实现 [**attach**](https://www.php.net/manual/en/splsubject.attach.php) 方法来订阅一个新的观察者，实现 [**detach**](https://www.php.net/manual/en/splsubject.detach.php) 方法来取消订阅一个观察者，实现 [**notify**](https://www.php.net/manual/en/splsubject.notify.php) 方法通知所有订阅的观察者。****

****首先，我会展示最终的结果，然后我们一起一步一步地检查代码。因此，最后一个类将是这样的:****

```
**class **UsersRepository** implements SplSubject
{
    private **\SplObjectStorage** $observers;

    private array $users = []; private static $instance; private function __construct()
    {
        $this->observers = **new \SplObjectStorage**;
    }
    private function __clone() {} public function createUser(*User* $user): void
    {
        *$this*->users[] = $user;
 **$this->notify();**
    } public function **attach**(\SplObserver $observer): void
    {
        $this->observers->attach($observer);
    } public function **detach**(\SplObserver $observer): void
    {
        $this->observers->detach($observer);
    } public function **notify**(): void
    {
        foreach($this->observers as $observer) {
            $observer->update($this);
        }
    } public static function getInstance(): self
    {
        if(*self*::$instance === null) {
            self::$instance = new self;
        }
        return self::$instance;
    }
}**
```

****第一步是实现属性，该属性将保存所有附加到**主题**的观察器，并在构造函数中初始化它:****

> ****PHP 有一个处理对象集的内置类叫做[**SplObjectStorage**](https://www.php.net/manual/en/class.splobjectstorage.php)。它在需要唯一标识对象的情况下很有用，并将在我们的**观察者模式**实现中用来保存我们的**观察者**集合。****

```
**private **\SplObjectStorage** $observers;private function __construct()
{
    $this->observers = **new \SplObjectStorage**;
}**
```

****然后，剩下的就是实现方法来处理订阅(**附加**)、取消订阅(**分离**)和通知(**通知**)方法。对于第一个和第二个方法，我们将使用 [**attach**](https://www.php.net/manual/en/splobjectstorage.attach.php) 和[**detach**](https://www.php.net/manual/en/splobjectstorage.detach.php)SplObjectStorage 的方法，将一个观察者作为参数传递。对于最后一个，我们将遍历我们的观察者池，调用每个观察者的 **update** 方法，将 Subject 类作为参数传递。****

> ****在 **createUser** 方法中创建用户后，我们还需要添加一个对 **notify** 方法的调用。****

```
**public function createUser(*User* $user): void
{
    *$this*->users[] = $user;
 **$this->notify();**
}public function **attach**(\SplObserver $observer): void
{
    $this->observers->**attach**($observer);
}public function **detach**(\SplObserver $observer): void
{
    $this->observers->**detach**($observer);
}public function **notify**(): void
{
    foreach($this->observers as $observer) {
        $observer->**update**($this);
    }
}**
```

****好了，我们的*用户资料库*完成了。现在，我们必须创建一个类，每次创建用户时都会得到通知。对于这个例子，我们将创建一个虚拟的电子邮件发送者类，它将实现 **SplObserver** 接口:****

> ******SplObserver** 接口指定我们必须实现[**update**](https://www.php.net/manual/en/splobserver.update.php)**方法，该方法将接收实现 **SplSubject** 的对象作为唯一参数。******

```
**class **Email** implements **\SplObserver**
{
    public function **update**(\SplSubject $subject): void
    {
        echo "Email class has been notified!\n";
    }
}**
```

****现在，我们只需创建一个脚本来测试我们的**观察者模式**实现:****

```
**<?php# First, we get an instance of UsersRepository and Email classes
$usersRepository = UsersRepository::**getInstance**();
$emailSender = **new Email()**;# Then, we attach the Email class to UsersRepository observers' pool
$usersRepository->**attach**($emailSender);# Now we may create a new user
$user = **new User**('Rafael', 'rafaelmelo.programmer@gmail.com');
$usersRepository->**createUser**($user);**
```

****执行该脚本将导致:****

```
**$ Email class has been notified!**
```

# ****📉优势****

****观察者设计模式允许我们在对象之间创建一对多的关系，并且，最好的部分是:主对象不知道它的依赖对象的任何事情。****

****那是什么意思？嗯，这允许我们在不改变主对象的情况下实现其他依赖对象，换句话说，这给我们带来了期望的低耦合和高内聚。而且，它符合 [**开合原理**](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) 中所述**[**稳固**](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design#:~:text=SOLID%20is%20an%20acronym%20for,OOD)%20principles%20by%20Robert%20C.&text=O%20%2D%20Open%2Dclosed%20Principle,I%20%2D%20Interface%20Segregation%20Principle) 原理。******

****实际上，回到我们的例子，除了已经实现的电子邮件通知之外，我们还可以实现其他通知类型，比如 SMS，而无需更改 *UsersRepository* 类。****

# ****📈不足之处****

****尽管有这些好处，但是滥用观察者模式实现会给系统带来不必要的复杂性。****

****此外，我们无法控制观察者的通知顺序，如果系统没有良好的结构，这可能会导致错误或意外的行为。****

# ****✅结论****

****Observer 模式是 PHP 世界中最常用的设计模式之一，它可以为应用程序带来灵活性，当我们有几个依赖于另一个对象的对象，并且需要在该对象的状态改变时执行某个操作，或者一个对象需要通知其他对象时，它非常有用。****

****这很容易实现，主要是在 PHP 中，我们有内置的接口和类来帮助我们加快开发过程。****

****观察者模式给我们带来了系统的低耦合和高内聚。另一方面，它会给代码带来不必要的复杂性和意想不到的副作用。****

****如果你想更深入地了解这个主题，我建议访问我在下面留下的所有参考资料！****

# ****📚参考****

> ****[官方 SplObserver 文档](https://www.php.net/manual/pt_BR/class.splobserver.php)****
> 
> ****[官方 SplSubject 文档](https://www.php.net/manual/pt_BR/class.splsubject.php)****
> 
> ****[观察者(refactoring.guru)](https://refactoring.guru/design-patterns/observer)****
> 
> ****[事件(计算)—维基百科](https://en.wikipedia.org/wiki/Event_(computing))****
> 
> ****[通过](https://www.sitepoint.com/understanding-the-observer-pattern/) [Ignatius Teo](https://www.sitepoint.com/author/iteo) 理解观察者模式****