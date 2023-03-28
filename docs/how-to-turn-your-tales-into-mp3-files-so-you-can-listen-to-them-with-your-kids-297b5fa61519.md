# 如何将你的故事转换成 mp3 文件，这样你就可以和你的孩子一起听了！

> 原文：<https://medium.com/codex/how-to-turn-your-tales-into-mp3-files-so-you-can-listen-to-them-with-your-kids-297b5fa61519?source=collection_archive---------4----------------------->

![](img/28c8c78a9ebfdcec6536b88941af9978.png)

照片由[农 V](https://unsplash.com/@californong?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

嗨，读者，我有另一个简单的提议给你，完全和网络安全无关！

[这里](https://levelup.gitconnected.com/how-to-build-an-ai-tale-generator-for-your-kids-34b8db153054)我们制作了一个为我们写故事的 AI，但现在我们想进一步让我们的孩子感到惊讶，所以让我们扩展这个项目，让我们的脚本用生成的故事生成一个 mp3。

我提倡边做边学，这对于初学者来说是一个很好的实践项目，你可以从中学习:

*   如何导入、使用和安装外部模块
*   如何读取文件
*   如何从命令行获取参数

所以，让我们扩展我们的故事生成器吧！

## 步骤 1:安装依赖项

第一步很明显，我们需要安装一个外部 TTS 库:

*   [**gTTS**](https://github.com/pndurette/gTTS) **:** 我们将使用它将文本转换为语音(TTS)并将结果存储在 mp3 文件中。

我假设您已经安装并配置了 python 和 pip，那么转到您的操作系统命令行并编写:

```
pip install gTTS
```

它将安装谷歌 TTS 库。

正在讨论的这个库是免费的，但是有每日请求的限制，所以你不能用它来阅读整本书，而只能阅读一些短文(我将在以后的文章中向你展示另一个解决方案)！

## 第二步:编写代码

![](img/03323f67001cd4d858d71b9fff5da65e.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

第一步是导入我们需要的库。

我们已经看到了 [gTTS](https://github.com/pndurette/gTTS) ，但是现在我只想描述我们将如何使用另外两个核心库(它们不需要安装)。

*   **pathlib:** 我们要用它来获取不带扩展名的文件名。
*   sys:在我们的项目中，这个库将负责管理 CLI 参数。

因此，让我们将它们全部导入:

```
from gtts import gTTS
from pathlib import Path
import sys
```

现在该写我们的主要方法了:

```
if __name__ == "__main__":
    filename = sys.argv[1]
    with open(filename, "r") as f:
        text = f.read()
        tts = gTTS(text)
        name = Path(filename).stem
        tts.save(f"name.mp3")
```

让我们分析一下代码在做什么:

*   将第二个参数保存在变量中(第一个参数的索引为 0，是脚本名称)
*   以阅读模式打开文件
*   读取一个文件并将内容放入一个名为 *"text"* 的变量中
*   创建一个 gTTS 对象，你可以在这里查看[文档](https://gtts.readthedocs.io/en/latest/module.html#module-gtts.tts)
*   获取不带扩展名的文件名
*   通过使用与“mp3”扩展名相同的名称，将文件保存在脚本的同一目录中。

## 第三步:看看结果

![](img/11e0e674cbfc47e4a692f1da1e77e7f4.png)

由[瑞安·克劳斯](https://unsplash.com/@ryanklausphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

现在是时候享受我们的工作并测试它了。

让我们想象一下，在脚本(名为 main.py)的同一个目录中有一个名为*“My story . txt”*的文本文件！

我们想要运行它，我们的方法是在命令行中键入:

```
python main.py "My Story.txt"
```

结果将是一个名为“My Story.mp3”的 mp3 文件，其中包含整个演讲！

## 结论

这是一个非常基础的项目，但我发现它在许多情况下非常有用(当有人阅读一篇文章时，我可以更好地理解它)。我希望你也一样！

如果你喜欢这个内容，你可以在 Medium 上关注我，或者看看我的博客[这里](https://www.stackzero.net/)

非常感谢您的关注！

[](/@stackzero/membership) [## 用我的推荐链接加入媒体- StackZero

### 阅读 StackZero(以及 Medium 上成千上万的其他作者)的每一个故事。成为会员后，您将可以完全访问…

medium.com](/@stackzero/membership)