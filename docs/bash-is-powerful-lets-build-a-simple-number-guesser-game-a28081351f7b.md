# Bash 很厉害！让我们建立一个简单的数字猜谜游戏。

> 原文：<https://medium.com/codex/bash-is-powerful-lets-build-a-simple-number-guesser-game-a28081351f7b?source=collection_archive---------9----------------------->

![](img/8e27b9a6705d5eba160268fd163f7fd4.png)

# 为什么是巴什？为什么要猜数字？

number guesser 是一个构建起来不太复杂的项目，您将学习如何使用 if、else if、else 语句、函数、用户输入和 random 模块(当然这些编程概念将以 Bash 方式实现！).**等等，什么是痛击？Bash 是大多数 linux 系统的默认登录 shell。这意味着要精通 linux，精通 Bash 是有帮助的。** Bash 可以直接从 linux 终端运行，也可以添加到一个文本文件中，并在现场使用 linux 命令行执行！此外，大多数(如果不是全部的话)linux 发行版支持 bash，所以不需要下载任何新的东西。只是运行它的飞行。

# 好了，让我们开始游戏吧！计划是这样的！

首先，我们需要一种方法来生成一个随机数(我们将使用内置的随机函数)并将其分配给一个变量，这样我们就可以引用该变量并将其与用户输入进行比较。接下来，我们需要获取用户输入。最后，我们需要将用户输入与随机数进行比较。如果用户猜对了，那么我们就结束游戏，让用户知道他们赢了！否则，我们需要继续游戏，直到用户获胜。

# 很简单，让我们开始写代码吧！

首先，我们添加 she bang(sharp 和 bang 的缩写),并在其后添加/bin/bash 路径。我们这样做是为了确保我们的 linux 操作系统正确执行 bash 文件。应该是这样的。

```
#!/bin/bash
```

接下来，我们需要一种通过命令行读取用户输入的方法。对于该井，使用内置的 read 命令。我们用-p 将它附加到一个变量上(这样我们以后可以引用它)。这个应该是这样的。

```
read -p "What number would you like to pick?" user_guess
```

所以 read -p (read 命令)，“你想挑选什么数字”(打印的字符串)，然后 user_guess(带有赋值的变量名)。

接下来，我们需要将用户输入与随机数进行比较，以确定游戏玩家是否正确。为此，请使用 if/else 语句。If/else 语句检查条件是否为真，并将根据条件执行代码块。在 bash 中，这个逻辑语句看起来像这样。

`if [conditional] then (do something here) else (do something) fi`

我们还想在终端上打印一些东西，让用户知道他是否正确。这口井使用回声。Echo(实际上是 echo 小写)是另一个内置命令，用于将字符串打印到终端。Echo 长这样。

`echo "this string will be printed to the terminal screen"`

所以结合 echo 和 if 语句，我们实际的代码应该是这样的。

```
if [ ${random_number} == ${user_guess} ]
then
    echo "You guessed right!"
else 
    echo "You guessed wrong"
fi
```

# 厉害！让我们打造最终产品！

现在我们已经有了基本的逻辑，从这里开始，我将稍微清理一下代码，使它更具可读性。我还将向一个函数添加一些逻辑并调用该函数，因为(在我看来)它使一切更具可读性，但更重要的是，如果当前用户猜错了，我将使用递归调用该函数。所以这里是成品！

```
echo "Random number guesser!"                                                                       

read -p "What is the random number you would like to guess? Please    
         pick a number between 0 and 9" user_guess

function number_guess() {                                                                            
   if [ ${random_number} == ${user_guess} ]                                                        
   then                                                                                            
       echo "You guessed right! Congratulations ${random_number} is 
             equal to ${user_guess}"        
   else                                                                                            
        echo "You guessed wrong! Try again."                                                        
        read -p "What is the random number you would like to guess? 
                please pick a number between 0 and 9" user_guess
        number_guess                                                                                
    fi                                                                                              
}                                                                                                   

number_guess
```

正如你所看到的，构建一个简单的数字猜谜游戏并不难。它只需要几行代码，因为我们使用了 bash，所以这个脚本非常容易从命令行运行。

# 谢谢你看我的文章！

想知道我在做什么吗？你可以在 http://davidesquerra.com/的或 https://github.com/Davidfree2 的找到我

如果你真的喜欢这篇文章，请在下面鼓掌，甚至关注！让我知道你的想法，如果你想看到更多这样的内容。如果有不清楚的地方，也不要提问题。感谢您的阅读。