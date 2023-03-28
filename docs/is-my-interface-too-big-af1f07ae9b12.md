# 是我界面太大了吗？

> 原文：<https://medium.com/codex/is-my-interface-too-big-af1f07ae9b12?source=collection_archive---------0----------------------->

![](img/e3d3f303c86088d1dd8bdc5907e1c154.png)

在这篇文章中，我解释了如何检测你正在使用的接口是否变得太大，需要拆分成更小的接口。较小的接口有助于提高代码的可维护性和可读性。更重要的是，它有助于理解代码。

Go 中的接口不同于 Java、c#、PHP 等中已知的接口。在那些语言中，你预先定义了接口。换句话说，在创建一个类的时候，你需要知道这个类将如何被使用。在围棋中，情况有所不同。你可以创建一个包含任意数量函数的结构，if 的用户可以只定义他需要的方法的子列表。这是非常强大的工具。但是有时我们仍然可以创建太大的接口。上面的列表应该有助于您发现代码中的接口分离问题。

# 嘲笑中的恐慌

如果您正在生成/编写模拟，并且其中一些方法有空的实现或者有一个`panic`在里面，那么您可能没有正确地隔离接口。这段代码是一个很好的建议，说明你在代码中有不止一个责任，并且你正试图测试其中的一个。

我的建议是试着将这些职责分解成独立的功能/结构，并分别测试它们。这里有一个例子。

```
type MethodRepoMock struct {
  mock.Mock
}

func (m *MethodRepoMock) Create(ctx context.Context, method dto.ShippingMethodDTO, tx *sql.Tx) error {
  panic("implement me")
}

func (m *MethodRepoMock) GetAll(ctx context.Context, sellerID string, templateID string, countryCode *string) ([]*models.ShippingMethodAggregate, error) {
  panic("implement me")
}

func (m *MethodRepoMock) Get(ctx context.Context, sellerID string, methodID string) (*models.ShippingMethodAggregate, error) {
  args := m.Called(sellerID, methodID)
  if args.Get(0) == nil {
    return nil, args.Error(1)
  }
  x, ok := args.Get(0).(*models.ShippingMethodAggregate)
  if !ok {
    panic(fmt.Sprintf("assert: arguments failed because object wasn't correct type: %v", args.Get(0)))
  }
  return x, args.Error(1)
}

func (m *MethodRepoMock) Update(ctx context.Context, method dto.ShippingMethodDTO, sellerID string, methodID string, tx *sql.Tx) error {
  panic("implement me")
}
```

在上面的代码中，您可以清楚地看到测试用例中只使用了一个函数。可以对使用 mock 的服务进行更新，以便为存储库使用一个简单的小型接口。

```
type Getter interface {
  Get(ctx context.Context, sellerID string, methodID string) (*models.ShippingMethodAggregate, error)
}
```

另一个说你的接口太大的前提是当你向它添加一个新方法时，它需要在代码的其他区域做一些改变——与你正在做的改变无关。

假设您有两个服务(`Service1`和`Service2`)和一个存储库。存储库用于两种不同的服务中(这不是一件坏事)。一个新的需求来了，你必须在`Service1`中添加一个新的功能，并添加到存储库中。如果变更需要变更，例如，在`Service2`的测试中，这是存在不良界面分离的迹象。

这个问题可能发生在两种场景中:两个服务都使用一个具体的存储库或者共享同一个(更大的)接口。

```
type myRepository interface {
  // used only in Service1
  GetSomething(ctx context.Context, id string) (Something, error)

  // used only in Service2
  CalculateSomething(ctx context.Context, param1 int) (int, error)

  // used only in Service2
  SendSomething(ctx context.Context, param1 int) (int, error)

  // used in both services
  SaveSomething(ctx context.Context, id int) (int, error)
}

type Service1 struct {
  repo myRepository
}

type Service2 struct {
  repo myRepository
}
```

不要害怕创建小接口，即使接口中的一些方法可以重复。当我们重构上面的代码时，我们将得到两个具有相同功能的接口。

```
type serice1Repo interface {
  // used only in Service1
  GetSomething(ctx context.Context, id string) (Something, error)

  // used in both services
  SaveSomething(ctx context.Context, id int) (int, error)
}

type serice2Repo interface {
  // used only in Service2
  CalculateSomething(ctx context.Context, param1 int) (int, error)

  // used only in Service2
  SendSomething(ctx context.Context, param1 int) (int, error)

  // used in both services
  SaveSomething(ctx context.Context, id int) (int, error)
}
```

这种重复没问题。我们倾向于过于激进地遵循 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 原则，仅仅为了节省几行代码而创造一个虚拟的存在。

```
type servicesRepo interface {
  // used in both services
  SaveSomething(ctx context.Context, id int) (int, error)
}

type serice1Repo interface {
  servicesRepo
  // used only in Service1
  GetSomething(ctx context.Context, id string) (Something, error)
}

type serice2Repo interface {
  servicesRepo

  // used only in Service2
  CalculateSomething(ctx context.Context, param1 int) (int, error)

  // used only in Service2
  SendSomething(ctx context.Context, param1 int) (int, error)
}
```

如您所见，您甚至没有保存代码行。你甚至拥有更多！在我看来，它也没有提高可读性。

# 公共接口

然而，创建一个公共接口并不是一件坏事。你这么做的原因可能是。让我们回到我们的两个服务和存储库。如果您必须将存储库公开，因为这些服务必须能够访问它们(并且它们都在不同的包中)，这是一种代码味道，是需要重构的信号。

我见过很多次一个叫`repositories`的包，里面所有接口都有自己的位置。每个想要使用其中一个接口的服务都必须使用整个(更大的)接口，正如我们在“模拟中的混乱”一节中看到的，忽略其他未使用的方法。怎么解决问题？

Go 的界面非常棒，因为你可以不费吹灰之力就修复它。你唯一要做的就是把只包含你需要的方法的小接口放在使用它的代码旁边，然后用它来代替。当你一步一步地重构它的时候，你将会达到能够完全移除`repositories`包的程度。

# 界面应该有多大？

这个问题问得好。理想情况下，它应该有尽可能少的方法。这是什么意思？你可以像定义依赖关系一样考虑接口。依赖性越小，代码就越灵活。你可以制作一个大的依赖注入树，并且只使用它的一个子集。一切都会好的。然而，当涉及到代码的可维护性时，它会变得很痛苦。在极端情况下——无法维护。

可以看看标准库。`io.Reader`或`fmt.Stringer`是只有一个方法的接口。他们只专注于一件事，并且做得很好。由于这种方法，接受`io.Reader`的函数不关心数据是来自 HTTP 请求、硬盘还是其他来源。接口的力量也用在了错误中。你可以使任何类型成为错误。它可以是字符串、整数或任何自定义结构。你能看到它的灵活性吗？当然，也有像 [ReadWriteCloser](https://golang.org/src/io/io.go?s=5240:5298#L128) 这样的接口对其他接口进行分组，但是这个规则仍然有效。让你的界面尽可能的小。

# 摘要

这就是我这次给你的全部。我希望你觉得这篇文章很有趣，并且我帮助你构建了更好的软件。这些建议并不是特定于 Go 的，但我将它们放在上下文中以帮助理解这些原则。我相信有更多不良界面分离的信号。如果你有自己的想法，请在下面的评论区告诉我们。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

标签:[# golang](https://developer20.com/tags/golang/)[# code-review](https://developer20.com/tags/code-review/)

【https://developer20.com】最初发表于[](https://developer20.com/is-my-interface-too-big/)**。**