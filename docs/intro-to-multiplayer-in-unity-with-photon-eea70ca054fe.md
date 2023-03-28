# 多人游戏中的光子入门

> 原文：<https://medium.com/codex/intro-to-multiplayer-in-unity-with-photon-eea70ca054fe?source=collection_archive---------4----------------------->

## [法典](http://medium.com/codex)

## 多人游戏编程是棘手的，所以让我们从使用网络游戏对象的基础开始。

![](img/1455fdead4cc42e24905d7fd4ce60c0d.png)

由[弗洛里安·奥利佛](https://unsplash.com/@florianolv?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/gaming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我最珍贵的记忆是和朋友一起玩多人游戏，无论是在网上还是在沙发上。通宵玩光晕，在地下室喝汽水是我们对美好周末的定义。现在我已经踏上了自己的游戏开发之旅，我迫不及待地希望能够构建自己的多人游戏，这样其他人就可以创造自己的回忆了。

不幸的是，即使像 Unity 和 Photon 这样的工具在易用性方面取得了巨大的进步，也很难直接进入网络化多人编程。在线多人游戏要求你以一种完全不同的方式来看待你的游戏，这种观念上的转变对我来说是一个巨大的绊脚石。在这篇文章中，我将解释如何转变你的思维，用 Unity 和 Photon 创建多人游戏。

# 引入光子

光子是一个伟大的软件包，它为你提供了我们进行多人游戏编程所需的各种代码。为了将重点放在概念上，我们将轻松完成设置步骤。使用 Photon 非常容易，他们网站上的说明应该足以帮助他们进行设置([https://doc . photonengine . com/en-us/pun/current/getting-started/pun-intro](https://doc.photonengine.com/en-us/pun/current/getting-started/pun-intro))。基本上，您需要:

*   创建一个光子帐户。
*   通过 Photon 站点创建一个应用程序来获得一个应用程序 ID。
*   通过包管理器将 Photon 包添加到 Unity 项目中。
*   在 Unity 中，运行 Photon 设置向导来输入您的应用程序 ID。

由于 Photon 为您提供了一些基本的**免费**用法，您将能够处理多达 20 个并发用户，而无需进行额外的配置！

# 多人多元宇宙

我最初认为，当我玩在线多人游戏时，就好像每个玩家在某个地方都有一个进入单个游戏实例的窗口。我以为我的命令被发送到那个游戏实例，而我只是看到了游戏的结果，有点像每个人都在看同一个电视节目。从技术上来说，你可以像那样制作你的多人游戏，但是我们将使用一个不同的模型。取而代之的是，每个玩家(也被称为**客户端**)在他们自己的电脑上运行一个**独立的游戏实例**。

![](img/5a91841a8c339862d82fa0219488042f.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/identical?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

让我们暂时忽略玩家的输入，想象我们有一个游戏，它从创建一个环境开始。如果你和我加入同一个游戏，那么我们的每台电脑都将独立运行游戏的代码。例如，代码可能放置一些钥匙，锁一些门，初始化一些炸药桶。结果是你和我将会看到完全相同的游戏关卡，考虑到运行的**完全相同的**代码，这是有意义的。

在很大程度上，我们不需要改变游戏逻辑的编码方式。无论连接了多少个客户端，都应该应用相同的规则。即使有 128 个人在玩，也应该有代码说一个桶如果被击中应该会爆炸。当我们不得不考虑如何保持所有客户端**同步**时，棘手的事情就开始了。例如，如果我在游戏中射击一个桶，我们需要确保你的游戏显示我的角色射击和桶爆炸。

# 壮丽的风景

光子包给了我们一个叫做`PhotonView`的单行为组件，好家伙，它要做一些工作了！当你给一个游戏对象附加一个`PhotonView`时，你就说这个游戏对象在所有客户端上都是相同的**。这意味着，如果一个给定的桶有`photonViewID = 1`，我们在我的游戏实例中对那个桶做的任何事情都可以与你的游戏实例中的相同桶同步。如果我的桶爆炸，它可以告诉其他客户端中的所有其他版本也爆炸。**

![](img/73428bb63454b7c19cdea813cbc0b516.png)

斯蒂芬·拉德福德在 [Unsplash](https://unsplash.com/s/photos/explosion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

照片视图使用名为 **RPC 的**函数进行通信。如果一个函数被指定为 RPC，那么它可以被其他客户端中的其他版本调用。RPC 看起来是这样的:

```
public class LaserShot: MonoBehaviourPun
{
  public GameObject laserBeam; private void Update()
  {
    if (Input.GetKeyDown(KeyCode.A))
    {
      ShootLaser();
    }
  } public void ShootLaser()
  {
    laserBeam.SetActive(true);
    this.photonView.RPC("ShootLaser_RPC", RpcTarget.Others);
  } [PunRPC]
  private void ShootLaser_RPC()
  {
    laserBeam.SetActive(true);
  }
}
```

*(快速旁白:* `*MonoBehaviourPun*` *与* `*MonoBehaviour*` *相同，它只是允许你自动访问同一个游戏对象上的一个* `*PhotonView*` *组件，而不必使用一个* `*GetComponent*` *调用。)*

在这个例子中，每个客户端都有一个角色游戏对象，它有一个`LaserShot`组件和一个`PhotonView`组件。

*   我的游戏实例中的角色调用它的`ShootLaser`函数，这将激活它在我的游戏中的`laserBeam`。
*   然后，`this.photonView.RPC`向这个角色游戏对象的所有其他版本发送一个消息(通过`RpcTarget.Others`参数)告诉它们调用它们的`ShootLaser_RPC`函数。

这里发生的是，我的游戏对象版本激活了它的激光束，然后同一个游戏对象的所有其他版本也收到一个调用来激活它们的激光束。`ShootLaser_RPC`，可以被称为任何东西，重要的是函数前面有`[PunRPC]`。

# 我的就是我的

上面的例子有一个很大的问题。请记住，每个客户端运行的都是同一个游戏的副本。这意味着如果你和我在一场死亡竞赛中，我们每个人都会有一个有两个角色的游戏。由于我的游戏版本有两个角色，每个角色都有一个`LaserShot`组件，那么点击“A”键将调用每个角色的`ShootLaser`功能。没有代码来决定我能控制哪个角色！

![](img/8761f5044ee4e924ecb4cc8b13df33a2.png)

照片由[最大焦点](https://unsplash.com/@maximalfocus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[不飞溅](https://unsplash.com/s/photos/laser?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

不要担心，因为`photonView`有一个所有权的概念，你可以用这个所有权来决定你的键盘命令是否应该作用于一个特定的游戏对象。

光子网络游戏中的每个客户端都被称为一个`Player`，`photonView`的主人可以被设定为一个给定的`Player`。在游戏过程中，您可以通过`isMine`查询一个`photonView`是否属于本地客户端:

```
private void Update() {
  if (this.photonView.isMine && Input.GetKeyDown(KeyCode.A))
  {
    ShootLaser();
  }
}
```

或者更好的模式:

```
private void Update() {
  if (!this.photonView.isMine)
  {
    return;
  } if (Input.GetKeyDown(KeyCode.A)
  {
    ShootLaser();
  }
}
```

现在，即使两个玩家都在运行他们的`Update`功能，只有拥有属于你的`photonView`的玩家会真正响应输入！

# 非常好

保持同步需要大量的工作，因此 Photon 为您提供了更多的组件，让您的生活变得更加轻松。

如果你还添加了`PhotonTransformView`组件，那么游戏对象的位置、旋转和比例可以在客户端之间自动**保持同步**。这意味着你可以专注于让每个玩家控制他们自己角色的移动，这些属性将在游戏的其他版本中更新！

类似地，`PhotonAnimatorView`组件将保持动画参数同步。只要每个玩家控制他们自己的动画师参数，他们将会在游戏的每个版本中自动更新！

# 冷静

咻，要涵盖的内容太多了！对于网络编程，我们需要学习很多新的模式，但是这些基本概念是你处理问题的方式。这很复杂，但非常可行。我希望这有助于澄清围绕光子和 RPC 的一些困惑，请随时询问您可能有的任何问题！