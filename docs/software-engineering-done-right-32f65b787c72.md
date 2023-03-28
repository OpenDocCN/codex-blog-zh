# 我们会掩护你的

> 原文：<https://medium.com/codex/software-engineering-done-right-32f65b787c72?source=collection_archive---------0----------------------->

## 软件工程第一季第四集

*行为驱动开发(BDD)是正确开发软件的一个重要方面。它允许定义软件需要满足的一组明确的需求。更普遍的好处是为产品开发团队提供了一种自动化验收阶段的方法，并将软件验收转移到开发周期的最早阶段。因此，反馈会尽快传递给工程师。*

![](img/81ede9427d9ddc2f3eaf4b1f301fcfe3.png)

*BDD 与测试驱动开发(TDD)密切相关，并且遵循相同的模式。当然，有测试是很棒的，知道测试通过或失败对于了解正在开发的产品的质量水平是至关重要的。另一个对提高信心水平很重要的方面是理解开发中的产品有多少关键的成功因素被任何形式的测试所覆盖。在本系列的前几期中，我为测试自动化和代码覆盖奠定了基础。现在是时候给我们的黄瓜——小黄瓜沙拉添加一些调料了。*

# 之前发生的事…

在本系列的第二部分中，我通过 Cucumber 框架和 Gherkin 向您介绍了行为驱动开发(BDD ),后者是用于编写使用场景或定义产品行为的领域特定语言(DSL)。如果你错过了那一集或者想要复习，请点击 Medium 上的文章链接。

[](/@Arc_E_Tect/software-engineering-done-right-ad1f194a5f3b) [## 软件工程做得好吗

### 如何真正应用常识知识和原则—第一季第二集

medium.com](/@Arc_E_Tect/software-engineering-done-right-ad1f194a5f3b) 

第三集是关于代码覆盖率以及如何使用 JaCoCo 工具来度量它。我包括了一些关于哪些覆盖目标是合理的以及为什么合理的经验法则。我强调 100%的覆盖率不应该是目标，这可能是浪费时间。我还向软件工程师解释了覆盖率度量的价值，以及为什么从管理的角度来看，覆盖率通常不是软件质量的指标。如果你错过了这一集或者想要复习，请点击 Medium 上的文章链接。

[](/@Arc_E_Tect/software-engineering-done-right-2358ae0d6dd4) [## 软件工程做得好吗

### 打好基础——第一季第三集

medium.com](/@Arc_E_Tect/software-engineering-done-right-2358ae0d6dd4) 

这两集都是在我的 Github 存储库中的一个分支中完成的。所有分支都用一个剧集指示器命名，可在以下位置找到:

[](https://bit.ly/github-arc-e-tect-branches) [## 三轴/弧形-电子检测 _ 电话簿

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

bit.ly](https://bit.ly/github-arc-e-tect-branches) 

# 跟上速度

一旦你克隆了资料库，你就可以访问这一集的来源。继续运行一个`gradlew build`。这将给我们带来错误，因为我们已经定义了我们的场景，但是我们仍然需要告诉 Cucumber 当它执行这个场景时要做什么。我们从格雷尔那里得到的错误在这方面是有帮助的。

当您克隆存储库并使用与某一集相关的分支时，您将获得该集之前的所有代码。您可以使用资源库中包含的 Gradle 来构建项目。对于 Windows，您可以从命令行运行:`gradlew build`

当您的操作系统是 MacOS 或 Linux 时，您可以从命令行运行:`./gradlew build`

如果您正在使用分支 s01e03，您的输出将显示您缺少一些代码。这是故意的，在这一集里，我们将添加缺失的源代码。当你在第一季第三集从命令行运行 Gradle 时，结果如下所示。

```
io.cucumber.junit.platform.engine.UndefinedStepException: There were 3 undefined steps
The step "some initial state" is undefined. You can implement it using the snippet(s) below:@Given("some initial state")
public void some_initial_state() {
    // Write code here that turns the phrase above into concrete
       actions
    throw new io.cucumber.java.PendingException();
}The step "this is a trigger for certain behavior" is undefined. You can implement it using the snippet(s) below:@When("this is a trigger for certain behavior")
public void this_is_a_trigger_for_certain_behavior() {
    // Write code here that turns the phrase above into concrete
       actions
    throw new io.cucumber.java.PendingException();
}The step "certain behavior is exposed" is undefined. You can implement it using the snippet(s) below:@Then("certain behavior is exposed")
public void certain_behavior_is_exposed() {
    // Write code here that turns the phrase above into concrete
       actions
    throw new io.cucumber.java.PendingException();
}
```

如你所见，Gradle 和 Cucumber 一起帮助我们做正确的事情。他们发出一些样板代码，让 Cucumber 在读取场景时执行。我们只需将这些方法复制到我们的`RunCucumberTest`类中，就万事大吉了。

# 填空

我们要实现的第一个行为是由文件`phonebook.feature`中定义的特性定义的。

我们需要实现所请求行为的三个方面。

1.  给出:在这个场景中，该场景对于在电话簿中定义联系人 Arc-E-Tect 的那些情况是有效的。
2.  何时:第二个方面告诉我们联系人有一个可检索的电话号码。
3.  然后:第三个方面告诉我们，当我们做‘when’子句中定义的事情时。期望的行为是我们得到一个特定的电话号码。

从上面的场景中，我们知道有一种叫做 Phonebook 的东西，有一种叫做 Contact 的东西，它们存储在 phone book 中。我们还知道联系人有电话号码。我们知道，名为 Arc-E-Tect 的联系人的电话号码为“ *+1 555 3247843* ”。这对于我们开发应用程序来说已经足够了，它将显示给定场景中描述的行为。

当您浏览如何实现所请求的行为的描述时，您会注意到代码根本没有做任何假设。该实现非常严格，将实现场景中描述的确切行为。

# 考虑到

大部分代码将作为给定的子句的*实现的一部分来编写。我将应用程序从`App`重命名为`PhonebookApplication`，并删除了方法`getGreeting()`，因为这与电话簿应用程序无关，并且基于我们描述的特性，不需要该方法。*

我将创建一个新的类 Contact，它有一个字段，联系人的电话号码。请注意，这种情况非常明显，在业务词汇中有一种叫做联系人的东西。这是我创建这个类的唯一原因。这是 BDD 威力的一个例子，它向工程师传达了业务词汇。考虑这样一种情况，给定的子句被定义为`Given “Arc-E-Tect” is in our Phonebook with phone number “+1 555 3247843”`，那么在我们的业务应用程序中就不会有联系人的概念。

您还应该注意，联系人只有电话号码，没有姓名。这可能会让你觉得奇怪，但是这个场景并没有提到名字，所以我们没有对名字做任何假设。通常情况下，在用户、产品所有者和软件工程师之间的讨论中，联系人也需要有一个名字，这一点变得很明显。但那会带来一大堆麻烦。名字并不总是像它们看起来的那样，也不是微不足道的。这一集不讨论名字。

因为我们需要能够确保有一个联系人可以通过带有特定电话号码的搜索字符串`“Arc-E-Tect”`检索到，所以我在我们的应用程序中添加了一个方法，允许我们确定有这样一个联系人。

我通过 TDD 做到了这一点，首先定义一个测试，然后对方法进行编码，这样测试就会成功。

第一次运行这个测试时，我得到了意料之中的编译错误，因为我的应用程序没有实现这个方法。在确保我的`addContactToPhonebookTest()`方法被正确实现后，我继续实现我们的*给定的*子句。

# 当...的时候

*When* 子句是所请求行为的触发器。在很多情况下， *when* 子句的实现是三个子句中最简单的。在我们的例子中，它告诉我们应用程序需要一种基于搜索字符串检索联系人的方法。这仅仅是在我们的应用程序中实现相关方法的问题。

我使用 TDD 来开发新方法。我会把源代码留给你，让你从 Github 库获取，确保你得到了 s01e04 分支。

这没什么大不了的，但是我们必须确保我们记得检索电话号码，这样我们就可以验证它的正确性。

您是否注意到我们不是从电话簿中检索联系人，而是从电话号码中检索？这个场景要求我们检索电话号码，而不是联系人。这一点值得注意。检索联系人可能更符合逻辑，因为我们也存储了联系人。考虑这样一种情况，应用程序处于第 6 版，现在还包含这个人的性别、宗教和信用卡号。检索完整的联系人将意味着所有其他敏感数据也被检索，这可能是非法的。因此，我们确保我们只检索我们应该检索的数据，这使我们的应用程序成为一个行为更合法的应用程序。

# 然后

在 *Then* 子句中，我们验证应用程序行为的正确性，并根据我们的场景验证检索到的电话号码是否与我们预期的相同。

# 质量有保证

因为我们所有的测试都被执行了，并且我们的代码覆盖率在每次执行`gradlew build`时都被测量了，所以我们可以立即看到我们的质量做得如何。

![](img/ea08bf1484e01f0e55079338563cfc9c.png)

鼓掌多达 50 次，表示你喜欢这个故事。

应该很好，因为我们所有的测试都通过了。事实上，当查看代码覆盖率指标时，我们看到这是 97%。尽管我们只在非平凡的方法上应用了 TDD，但是我们看到，通过应用 BDD，并且只实现了所请求的行为，我们的测试几乎 100%覆盖了我们的代码。我们的测试唯一没有触及的部分是应用程序的空`main()`方法。这就是花时间确保我们的构建环境可靠且一致的好处。

一如既往，我的软件工程做对系列这一集的完整源代码可以在 Github: [库](https://github.com/Arc-E-Tect/arc-e-tect_phonebook/tree/s01e04)找到。

接下来…

[](https://arc-e-tect.medium.com/being-prepared-for-the-future-701a6d5b83fb) [## 为未来做准备

### 软件工程第一季第五集

arc-e-tect.medium.com](https://arc-e-tect.medium.com/being-prepared-for-the-future-701a6d5b83fb) 

@电弧检测

[](https://arc-e-tect.medium.com/membership) [## 通过我的推荐链接——伊旺·艾森加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

arc-e-tect.medium.com](https://arc-e-tect.medium.com/membership) 

# 放弃

文本非常明确地传达了我个人的观点、经验和实践。与我以前或现在的客户、顾客或雇主的观点、经历和实践的任何相似之处纯属巧合。因此，这篇文章是我自己的，我是它的唯一作者，也是它的唯一版权持有者。

# 信用

特别感谢我可爱的妻子 Olcay，以及我的朋友 Sytse，他们花时间和精力审阅了我的文章。我相信他们的反馈极大地提高了文章的质量。

图片鸣谢:[https://bit . ly/Old _ Oak _ Common _ Locomotive _ Depot _ King _ Henry _ VIII](https://bit.ly/Old_Oak_Common_Locomotive_Depot_King_Henry_VIII)