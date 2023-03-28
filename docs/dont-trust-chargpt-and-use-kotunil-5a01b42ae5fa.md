# 不要相信 ChatGPT 而使用 KotUniL

> 原文：<https://medium.com/codex/dont-trust-chargpt-and-use-kotunil-5a01b42ae5fa?source=collection_archive---------1----------------------->

最近几周，媒体和社交网络上充斥着与 ChatGPT 对话的消息。

我也试着和 ChatGPT 聊天。我们谈话的结果在标题里。

如果你想知道为什么 ChatGPT 不可信，为什么使用 KotUniL 更好——请继续阅读。

首先，我用英语和 ChatGPT(为了简单起见，我有时称它为 AI)交谈，然后用俄语交谈。

我首先问了 AI 一个简单的学校问题。

问题和解决方案如下:

> 在伊娃的案例中，她的水族箱里有一个玻璃杯破了，水流到了地板上。破损前水族箱里有 32 升水。伊娃的房间宽 4 米，长 4.3 米。假设伊娃房间里的水留在那里没有流走，那么它现在有多高？

**ChatGPT 的回应:**

> 为了确定伊娃房间里的水的高度，你需要知道从水族馆流出的水的体积和房间的体积。
> 
> 水族箱流出的水的体积简单来说就是玻璃破裂前水族箱的体积，32 升。
> 
> 要计算房间的体积，你需要知道房间的米数。您提供了房间的宽度和长度，但没有提供高度。没有房间的高度，就不可能确定房间的体积，因此也就不可能确定房间中水的高度。
> 
> 如果你知道房间的高度，你可以通过乘以房间的宽度、长度和高度来计算房间的体积。例如，如果房间的高度是 3 米，房间的体积将是 4 x 4.3 x 3 = 52.8 立方米。
> 
> 要确定房间中水的高度，您需要将从水族馆流出的水的体积(32 升)除以房间的体积(52.8 立方米)，然后将结果乘以 1000，将结果从米转换为毫米。
> 
> 例如，如果房间的高度是 3 米，房间中水的高度将是(32 / 52.8) x 1000 = 60.4 毫米。
> 
> 希望这有帮助！如果你还有其他问题，请告诉我。

我建议你找出错误，并同意或不同意标题中的论点，即迄今为止我不能相信 ChatGPT 能解决甚至最简单的问题。

接下来，我让人工智能用 Kotlin 为这个解决方案写一个程序。

立刻，不眨眼，人工智能回答道:

```
fun main() {
    // Set the volume of the aquarium before the glass broke
    val aquariumVolume = 32.0

    // Set the dimensions of the room
    val roomWidth = 4.0
    val roomLength = 4.3
    val roomHeight = 3.0 // Note: you need to provide the height of the room

    // Calculate the volume of the room
    val roomVolume = roomWidth * roomLength * roomHeight

    // Calculate the height of the water in the room
    val waterHeight = (aquariumVolume / roomVolume) * 1000

    // Print the result
    println("The height of the water in the room is $waterHeight millimeters.")
}
```

更重要的是，人工智能在一个单独的盒子里显示了程序的文本，黑色背景，适当的字体和颜色。

请注意，生成的程序只是复制了错误的解决方案。

尊重是由于人工智能对科特林语法的“了解”。

记住，人工智能是在大量的例子上训练的，我决定用初等算术来测试它，但用的是在训练中不太可能遇到的大量数字。人工智能通过了测试，这表明对于算术问题，它知道如何打开内部计算器。

然后我决定看看人工智能是否知道辛尼斯。

事实证明确实如此:

> 0 的正弦等于 0。正弦函数是一个数学函数，它以一个角度作为输入，返回直角三角形与该角度相对的边与该三角形斜边的比值。换句话说，给定直角三角形中的一个角，正弦函数返回该角的对边长度与斜边长度的比值。对于角度为 0 度的特殊情况，与角度相对的边的长度为 0，因此 0 的正弦等于 0。

意识到我有一块难啃的骨头，我要求人工智能编写一个太空飞行控制程序。我预计我将不得不回答一万个问题，详细说明系统的需求。之后，我期望得到一个有两千万行的程序。

但是这两件事都没有发生。

AI 勇敢地给了我一个小程序和一个解释。

```
import java.util.Scanner;

public class SpaceFlightControl {
  public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);

    // initialize variables for the space flight
    double fuel = 100.0;
    double altitude = 0.0;
    double velocity = 0.0;
    double acceleration = 0.0;
    double thrust = 0.0;

    // control loop for the space flight
    while (fuel > 0) {
      // display the current status of the space flight
      System.out.println("Fuel: " + fuel);
      System.out.println("Altitude: " + altitude);
      System.out.println("Velocity: " + velocity);
      System.out.println("Acceleration: " + acceleration);
      System.out.println("Thrust: " + thrust);

      // prompt the user for the desired thrust
      System.out.print("Enter desired thrust (0-100): ");
      thrust = scanner.nextDouble();

      // update the acceleration and velocity based on the thrust
      acceleration = thrust / 10.0;
      velocity += acceleration;

      // update the altitude based on the velocity
      altitude += velocity;

      // decrease the fuel based on the thrust
      fuel -= thrust / 10.0;
    }

    // display a message when the fuel runs out
    System.out.println("Fuel exhausted. Space flight ended.");
  }
}
```

> 这个程序通过允许用户在控制循环的每次迭代中输入一个推力值(从 0 到 100)来模拟太空飞行。然后，程序根据推力计算加速度、速度和高度，并相应地更新燃油。控制循环一直持续到燃料耗尽，此时程序显示一条信息，表明太空飞行已经结束。
> 
> *我希望这有所帮助！如果你有任何问题让我知道。*

结束了我们的英语对话后，我问人工智能生命的意义是什么。

答案很快就来了:

> *生命、宇宙和一切事物的意义是一个困扰了哲学家、神学家和科学家几个世纪的问题。这是一个经常以幽默或修辞方式提出的问题，参考了道格拉斯·亚当斯的科幻小说《银河系漫游指南》。在这部小说中，一台名为 Deep think 的巨型超级计算机被要求寻找这个问题的答案，经过深思熟虑后，它的回答是“42”。小说中的人物对这个答案感到失望，因为他们原本希望得到更深刻的东西。*
> 
> 然而，在现实中，生命、宇宙和一切事物的意义是一个非常个人化和主观的问题，不同的人可能有不同的答案。有些人认为生活的意义是寻找幸福和满足，而另一些人认为是帮助他人或对世界产生积极影响。一些人认为宇宙有特定的目的或意义，而另一些人认为它只是物质和能量的随机集合。最终，这个问题的答案可能是每个人必须根据他们自己的信仰、价值观和经历来决定的。

然后换成俄语，要求给我讲个故事。人工智能对著名的三只小猪的故事进行了新的、创新的解释，这让我很高兴。如果你有兴趣，在这里阅读它[。](https://proza.ru/2022/12/20/852)

现在我将尝试回答这个可能长期困扰读者的问题，KotUniL 是什么，它与此有什么关系。

首先，我从[kotu il 文档中获取了第一个任务。](https://github.com/vsirotin/si-units)

其次，根据 ChatGPT 的创建者所说，他们在专业培训师的帮助下训练了这个系统。因此，经过一定的简化，我们可以说 ChatGPT 反映了人类训练者在解决物理问题方面的“平均”水平。

让我们再读一遍解答的最后几行。立方分米除以立方米。你得到一个无量纲单位，乘以 1000，另一个无量纲单位。据说这种乘法的结果是毫米！

人工智能对维度感到困惑，然后将这种困惑具体化为一个科特林程序。

唉，通过创造科学、技术、经济和其他程序和系统，维度混乱是非常普遍的。这种程序错误已经导致宇宙飞船坠毁、船只沉没和人员死亡。

为了避免这种情况，应该使用一个用于处理物理和其他单元的库。

实际上，我认为单元库应该和语言一起“开箱即用”。在 Kotlin 的例子中，一个像 KotUniL 这样的库应该有很多像 Ktor 或 Dokka 这样的库。

这是我给语言开发者的一个建议。如果你也有同样的看法，请[投票支持](https://youtrack.jetbrains.com/issue/KT-55556)。

我想如果开发者接受了，这个世界会变得更可靠一点。当他们决定的时候，那些想要的人可以使用 KotUniL。