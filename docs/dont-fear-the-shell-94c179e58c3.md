# 不要害怕外壳

> 原文：<https://medium.com/codex/dont-fear-the-shell-94c179e58c3?source=collection_archive---------14----------------------->

![](img/d9d2aa346eeae9b0f848fd54d4b9cee1.png)

由在英国布里斯托尔工作的 [doma 团队](https://twitter.com/doma_dev)于*2021 年 4 月 9 日*在 *Fri 发表。*

# TL；速度三角形定位法(dead reckoning)

何时求助于 shell 脚本:

*   便携性很重要
*   手头的问题很紧迫
*   文件系统交互
*   命令行程序自动化

何时寻找替代方案

*   需要可扩展性
*   编写关键任务的代码

Shell 脚本是危险的，使用 shellcheck 并限制自己使用习惯用法

电脑控制台是计算领域第二重要的 UX 改进，仅次于窗口管理器。从允许计算机用户输入由单进程操作系统执行的程序到将它们转换成工具箱，控制台走过了漫长的道路。令人高兴的是，大部分工作都是在势不可挡的创新者[道格拉斯·麦克洛伊](https://en.wikipedia.org/wiki/Douglas_McIlroy)的监督下在贝尔实验室进行的。

Multics“shell”就像那个时代其他支持终端的计算机上的输入一样，是一种接受程序并在 Multics OS(UNIX 的前身)上执行程序的工具。Multics shell 的最早版本已经有了输入/输出重定向。直到道格拉斯·麦克洛伊[发现(不是发明)](https://corecursive.com/021-gods-programming-language-with-philip-wadler/)命令管道，它才成为所有操作系统的黄金标准。

流水线和 UNIX 哲学允许编写小的特定问题的程序，这些程序可以在以后编写。但是 2021 年使用 UNIX shell 脚本的最大原因是可移植性。事实上，每个现代系统都有现成的 UNIX shell。此外，通常编写一个写得相当好的 shell 脚本是“足够好的工作”。但是怎么做呢？让我们探索一下这个问题的答案，假设读者已经知道了 shell 脚本的基础知识。

# bash 中最少的 shell 脚本

![](img/4e72704d44b3e51240b15f85e7266769.png)

让我们抛开最重要的考虑。编写安全可靠的 shell 脚本几乎是不可能的。至少可以使用`[shellcheck](https://github.com/koalaman/shellcheck)`，它与 VSCode 集成了[。此外，对用户输入要非常严格，尽最大努力引用每一个包含变量的参数。](https://marketplace.visualstudio.com/items?itemName=timonwong.shellcheck)

好了，说完了，让我们来讨论一步一步地创建一个合理的 shell 脚本。

# 设置您的 shell 环境

在开始编程 shell 之前，我们需要首先确定我们将使用哪种 shell 脚本语言。关于这一点有三个学派:

1.  默认使用`bash`。Bash 是在拥有大量特性和可移植性之间的一个合理的中间地带，因为它随每个流行的操作系统一起提供。
2.  默认使用`sh`，需要高级功能时使用`bash`。这是一种纯粹的方法。它提供了最大的可移植性，但是需要区分基本特性和 bash 专有特性。
3.  使用`zsh`、`fish`或其他“潮人”外壳做任何事情。我提到这个学派是为了完整。因为它破坏了可移植性，选择这个选项的人可能也用 Python 编码。

正如人们所猜测的，我们建议简单地对所有事情使用`bash`。当然，苹果似乎不同意将`bash`作为默认外壳，但它不会离开 mac os 系统。相反，Windows 10 支持与 WSL 程序的合理整合。它需要一些设置，但是现在 WSL2 似乎成为了 Windows 开发的默认设置。

此外，`bash`脚本有细粒度的内置支持来降低不可避免的错误的影响。在设置您的 shell 时，我们建议您使用以下[选项](https://www.gnu.org/software/bash/manual/bash.html#The-Set-Builtin):

`#!/usr/bin/env bash
set -euo pipefail`

可选地添加`-x`以便于调试。

# 命令行参数处理

如果出于某种原因，你想用 shell 写一些大的东西，比如[一个带有 git 后端的成熟的问题跟踪器](https://github.com/manpages/issues-legacy)，你需要使用或者[制作你自己的调度系统](https://github.com/manpages/issues-legacy/blob/master/issues#L30)。然而，在本帖中，我们将考虑简单的参数处理。毕竟，我们大力提倡短小精悍的脚本，只做一件事。

首先，让我们看看如何打印帮助:

```
if [["$1" == "--help" || "$1" == "-h"]]; then
  cat <<EOH
frgtmv: for each file read from STDIN, forget its filename entirely or amend part of it.
...
''frgtmv'' will then ''mv'' each of these files to ''\$(date +'%Y%m%d%H%M%S%N')'', preserving the file extension.
...
EOH
  exit
fi
```

关键点:

1.  我们使用了`<<EOH / EOH`“heredoc”语法，并确保我们没有缩进它下面的行。
2.  我们用反斜杠来转义特殊字符，如`$`。如果我们不这样做，bash 会在 subshell 中评估内部。
3.  印刷后别忘了给`exit`帮忙！

现在让我们使用`-n`，一个检查变量是否被设置的谓词，来准备所需的变量。我们通常希望在文件的开头设置默认值:

```
_mode="forget"
_pattern_from=""
_replace_with=""

if [-n "$1"]; then
  _mode="amend"
  _pattern_from="$1"
fi

if [-n "$2"]; then
  _replace_with="$2"
fi
```

有时，如果没有提供参数，您需要退出。我们使用`-z`来检查字符串是否为空:

```
# Exit if file or directory is not submitted or not a valid file or directory
if [-z "$1"]; then
  echo "We really need the first argument"
  exit 228
fi
```

# 标准输入、管道和 GNU 并行

有时，您需要通过管道或用户输入从 STDIN 接收输入。使用`read -r`完成:

```
while read -r _x; do
  mv -v "$_x" "$(date +'%Y%m%d%H%M%S%N').${_x#*.}"
done
```

如果您更关心性能而不是可移植性，请使用`cat -`将您的 STDIN 传递给 GNU parallel，遵循以下模式:

```
function forget() {
  mv -v "$2" "$(date +'%Y%m%d%H%M%S%N').$1.${2#*.}"
}
export -f forget # (A)

if [[$_mode == "forget"]]; then
  cat - | parallel forget {%} {} # (B)
fi
```

在`(A)`中，并行负载被实现为 bash 函数。注意`export`语句！我们建议编写接收两个变量的有效负载:并行作业 ID ( `{%}`)和当前从 STDIN 流中读出的项目(`{}`)。有效载荷从`(B)`调用。

以下是一些有趣的`parallel`技巧:

*   `--keep-order`保证输入的顺序不变。每个输入需要几个文件句柄，这可能会成为一个瓶颈。
*   `find . -print0 | parallel -0 f {}`以空终止模式工作。
*   `parallel 'echo "{%}:{1}:{2}";' ::: 1 2 ::: a b c`将并行化输入集{1，2} × {a，b，c}的笛卡尔积。

# Bash 参数扩展

让许多 shell 新用户困惑的是，“[参数扩展](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)”笼罩着一层神秘的面纱。在我们看来，造成这种结果有几个原因:

1.  参数扩展是一个统一的名称，它将访问值和替换值结合在一起。
2.  在这些用例中，有无数的条件行为，它们是根据参数的种类决定的。
3.  没有太多的扩张。“扩展”这个词只是“减少到一个值”的一种晦涩的说法。

让我们从头开始。bash 中的参数要么是变量(如`$HOME`)，要么是位置“自变量”参数(如`$1`)，要么是特殊参数(如`$@`)。

“扩展”是参数的[还原](https://en.wikipedia.org/wiki/Reduction_strategy_(lambda_calculus))为数值的过程。变量以人们期望的方式扩展。[然而，特殊参数](https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html)可以有上下文相关的扩展。扩展有特殊的语法来附加额外的计算，比如字符串替换，长度计算等等。

我们以下面这个变量为例:`x="a.b.c"`。根据我们的说法，下面是最常用的参数展开列表:

1.脱衣。用例:获取文件扩展名或删除文件扩展名

```
${x%.*} ≡ a.b
${x%%.*} ≡ a
${x#*.} ≡ b.c
${x##*.} ≡ c
```

2.字符串替换。

```
${x/./\!} ≡ a!b.c
${x//./\!} ≡ a!b!c
```

3.用`IFS`和`[@]`进行数组枚举。

`IFS="."; for v in ${x[@]}; do echo -n "($v)"; done ≡ (a)(b)(c)`

您也可以使用“复合赋值”来构造数组:

```
x=(a b c)
for v in ${x[@]}; do 
    echo -n "($v)"
done
```

通常不需要 IFS 操作。如果你要改变拆分上下文，你应该看看有没有别的方法。你可能会成为 XY 问题的受害者。

本教程应该提供足够好的技术来快速有效地实现您想要的 shell 脚本。引用你的变量，快速失败，备份从破坏性的改变中恢复，不要使用太多的“高级功能”，因为它们容易出错，祝你好运！

我们留给您一些我们编写的 shell 脚本，这些脚本拓展了 shell 的使用范围:

*   [巴什制造的棋钟](https://github.com/manpages/usr-local-bin/blob/master/chess)
*   [我每天使用的一个脚本:生成或重新附加到给定项目的](https://git.sr.ht/%7Ejonn/shmux/tree/master/item/shmux) `[tmux](https://git.sr.ht/%7Ejonn/shmux/tree/master/item/shmux)` [会话](https://git.sr.ht/%7Ejonn/shmux/tree/master/item/shmux)
*   [自动检测位于](https://github.com/manpages/issues-legacy/blob/master/issues#L5-L15) `[include/](https://github.com/manpages/issues-legacy/blob/master/issues#L5-L15)` [目录](https://github.com/manpages/issues-legacy/blob/master/issues#L5-L15)中的脚本和评估模块的规范路径
*   [终止](https://github.com/manpages/issues-legacy/blob/master/include/bye.sh) [工艺树](https://github.com/manpages/issues-legacy/blob/master/issues#L3)，[打印调试输出](https://github.com/manpages/issues-legacy/blob/master/include/debug.sh)

如果社区感兴趣的话，我们将花些时间用 Turtle 介绍 Haskell 中的可扩展 shell 脚本。像往常一样，在 [Twitter](https://twitter.com/doma_dev) 或 [Dev.to](https://dev.to/doma) 或 [Medium](https://doma-dev.medium.com/) mirrors 的评论中联系我们。

*原载于*[*https://DOMA . dev*](https://doma.dev/blog/get-things-done-with-bash/)*。*