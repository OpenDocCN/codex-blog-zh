# 纪念品设计模式介绍

> 原文：<https://medium.com/codex/an-introduction-to-memento-design-pattern-861ad511bf5a?source=collection_archive---------4----------------------->

![](img/0cc9ff12c1a3787509217e4f6a23b906.png)

纪念品设计模式介绍

memento 设计模式允许撤销或恢复对象的先前版本。这被归类在行为模式下。根据四人组(g of)写的书，纪念品模式有三个支柱，称为发起者、纪念品和看守者。

1.  发起者:这创建了 memento 对象，并且这是状态需要被保存的原始对象。
2.  Memento:它负责存储发起者的内部状态。
3.  看守人:这是记录纪念品的。

让我们用一个真实的场景来理解这一点。

*假设，某公司根据员工的资历和绩效指数向其提供奖励。有 3 个资历级别，分别命名为 1、2 和 3。1 是最高级别，3 是最低资历级别。性能指数也遵循同样的顺序。该奖励提供给资历级别和绩效指数都小于或等于 2 的员工。在这里，人力资源可以给员工增加奖励，如果相应的员工表现较差，他们可以恢复增加的奖励。*

为了实现这个场景，

首先，我创建了一个名为 *EmployeeReward* 的类来保存关于雇员报酬记录的信息。除此之外，我还创建了另一个名为*常量*的类来保存将在实现过程中使用的常量值。请参考以下代码。

接下来，我创建了另一个名为 *RewardHandler* 的类，并添加了一个名为*add reward(EmployeeReward EmployeeReward)*的方法，将员工的奖励添加到一个数组列表中。当员工的年资级别和绩效指标小于或等于 2 时，增加奖励。这里我实现了一个名为 *getRewards()* 的方法来返回一个克隆的*employeeRewards*ArrayList 对象。在 *RewardHandler* 内部，我已经声明了一个名为*rewardhandlermento 的静态类。再次在 *RewardHandler* 中，我创建了一个名为 *saveReward()* 的方法，通过传递克隆的 ArrayList 的参数(使用 *getRewards()* 方法)来返回*rewardhandlermento*的一个实例。在这里，我实现了一个名为*revert redward(rewardhandlermento rewardhandlermento)*的方法来设置*rewardhandlermento*实例的 ArrayList 对 *RewardHandler* 类的 ArrayList 的引用。除此之外，我还覆盖了 *toString()* 方法来显示 *RewardHandler* 类中的详细信息。参考下面的代码。*

这里， *EmployeeReward* 类被认为是发起者，*rewardhandlermento*类被认为是纪念品。

接下来，我实现了一个名为 *RewardHistory* 的类来跟踪*rewardhandlermento 的实例。*在这里，我创建了一个堆栈来跟踪从*rewardhandlermento*类创建的实例，这是由*save reward(reward handler reward handler)*方法完成的。在那之后，我实现了另一个名为*revertrevard(reward handler reward handler)*的方法来返回堆栈的顶部元素，并将返回的*rewardhandlermento*实例的奖励分配给在 *RewardHandler* 类中声明的 ArrayList。参考下面的代码。

这被认为是看管人。

最后，我在 *MainApplication* 类中实现了 main 方法。参考下面的代码。

在这里，首先，我给在 *RewardHandler* 类中声明的 ArrayList 添加了 3 个奖励，并将它们的纪念品保存到堆栈中。之后，我通过调用 *RewardHistory* 类的*revertReward(reward handler reward handler)*方法，恢复了最后的动作。下面定义了接收到的输出。

```
Sorry, Employee with name: Hasini and ID: 003 cannot get rewards
Employee ID: 001
Employee Name: Hasini
Employee's Performance Index: 2
Employee's Level: 2
Employee's Reward Type: Dinner
Place: Marina BayEmployee ID: 002
Employee Name: Sandunika
Employee's Performance Index: 1
Employee's Level: 2
Employee's Reward Type: Tour
Place: India_____________________________Employee ID: 001
Employee Name: Hasini
Employee's Performance Index: 2
Employee's Level: 2
Employee's Reward Type: Dinner
Place: Marina BayEmployee ID: 002
Employee Name: Sandunika
Employee's Performance Index: 1
Employee's Level: 2
Employee's Reward Type: Tour
Place: India_____________________________
```

据此，当调用 *RewardHistory* 类的*revertReward(reward handler reward handler)*方法时，由 *RewardHandler* 中的*rewardhandlermento*类的返回实例(在 *RewardHistory* 内栈顶元素)被*revertReward(rewardhandlermento rewardhandlermento)*方法捕获。之后*奖励处理员*的奖励参考分配给*奖励处理员*内的*员工奖励*。因此，这由 *RewardHandler 类中的 *toString()* 方法使用，*即使在 main 方法中调用了*revertrevard(reward handler reward handler)*方法之后，也会产生相同的输出。

为了避免这种情况，我们想要撤销的实体不应该保存到堆栈中。因此，没有新的*rewardhandlermento*实例进入堆栈，也没有恢复。参考下面的代码。

下面定义了接收到的输出。

```
Sorry, Employee with name: Hasini and ID: 003 cannot get rewards
Employee ID: 001
Employee Name: Hasini
Employee's Performance Index: 2
Employee's Level: 2
Employee's Reward Type: Dinner
Place: Marina BayEmployee ID: 002
Employee Name: Sandunika
Employee's Performance Index: 1
Employee's Level: 2
Employee's Reward Type: Tour
Place: IndiaEmployee ID: 004
Employee Name: Hasini
Employee's Performance Index: 1
Employee's Level: 1
Employee's Reward Type: Tour
Place: UK_____________________________Employee ID: 001
Employee Name: Hasini
Employee's Performance Index: 2
Employee's Level: 2
Employee's Reward Type: Dinner
Place: Marina BayEmployee ID: 002
Employee Name: Sandunika
Employee's Performance Index: 1
Employee's Level: 2
Employee's Reward Type: Tour
Place: India_____________________________
```

这是文章的结尾。我希望你已经对纪念品的式样有所了解。让我们在另一篇文章中相遇。在那之前，注意安全！！😍😍

[](https://github.com/HasiniSandunika/Design-Patterns-Java.git) [## GitHub-HasiniSandunika/Design-Patterns-Java:用 Java 实现设计模式。

### 用 Java 实现设计模式。为 HasiniSandunika/Design-Patterns-Java 开发做出贡献，创建…

github.com](https://github.com/HasiniSandunika/Design-Patterns-Java.git) 

**参考文献**

*   [https://www.youtube.com/watch?v=Pwm-jrG2ZVA&list = PLD-myte BG 3 x 86 i3 uyaxwzkfvtuy 2g MDO&index = 6](https://www.youtube.com/watch?v=Pwm-jrG2ZVA&list=PLD-mYtebG3X86i3uyAXwZKfVtUy2gMDdo&index=6)
*   https://www.baeldung.com/java-memento-design-pattern
*   [https://www . tutorialspoint . com/design _ pattern/memento _ pattern . htm](https://www.tutorialspoint.com/design_pattern/memento_pattern.htm)
*   [https://www.javatpoint.com/memento-pattern](https://www.javatpoint.com/memento-pattern)