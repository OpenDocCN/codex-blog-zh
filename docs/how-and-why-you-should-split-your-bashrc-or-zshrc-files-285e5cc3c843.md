# 你应该如何(以及为什么)分割你的。巴沙尔或者。zshrc 文件

> 原文：<https://medium.com/codex/how-and-why-you-should-split-your-bashrc-or-zshrc-files-285e5cc3c843?source=collection_archive---------2----------------------->

只有几行代码的可伸缩 Bash/Zsh 启动脚本

![](img/6e80d5881bf4a1161cf8f326d03d0f98.png)

[杰瑞米·托马斯](https://unsplash.com/@jeremythomasphoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 目标

*   要将我们的`.bashrc` / `.zshrc`拆分成多个文件，放在`zshrc`文件夹中，然后逐个加载:

```
for FILE in ~/**zshrc**/*; do
    **source** $FILE
done
```

# 背景

`.bashrc` / `.zshrc`主要在我们启动终端 app 的时候执行。通常，它包含我们的自定义别名/快捷方式、函数/命令、外壳变量和环境变量，以帮助我们的工作流。

然而，随着时间的推移，当我们修补各种框架、实用程序或项目时，这很容易变得混乱。甚至在工作中，我们可能需要自动化重复的任务/命令来提高生产力/效率。因此，我们会不时更新这些文件。起初的小花园可能会演变成更难维持的茂密森林。

## 拆分 Vim 设置

我之前有一个长度`.vimrc` / `init.vim`的设置，所以我把它拆分成更模块化的，这样主加载程序甚至可以自动检测/加载新文件。使[我的设置](https://github.com/ranelpadon/configs/tree/master/nvim)更具可扩展性:

```
runtime settings.vim                             
runtime plugins.vim
runtime mappings.vim " `!` is needed to load all files in the folder.                             runtime! themes/*.vim                             
runtime! plugins-config/*.vim
```

## 拆分 Zsh 设置

同样，我之前的`.zshrc`中有超过**1500**行，现在的[只有几行](https://github.com/ranelpadon/configs/blob/master/.zshrc)。下面将解释它的简化版本。大多数人也会有更简单的用例。

```
CONFIGS=$HOME/dev/configs                                                           **source** $CONFIGS/zshrc/init.sh FILES_STR=$(fd --glob '*.sh' --exclude 'init.sh' $CONFIGS/zshrc)
FILES=($(echo $FILES_STR | tr '\n' ' ')) for FILE in **$FILES**; do                                 
    **source** $FILE                             
done
```

## 利益

模块化设置是一种很好的做法，原因如下:

*   相关内容将被分组在一起，这意味着更好地分离关注点。
*   系统将更具可扩展性，因为未来的分割文件也将自动加载，无需更新`.zshrc`。
*   首先将内容分开会迫使你一个接一个地检查你所有的设置，并发现过时的、未使用的或重复的设置。这也将迫使你学习更多关于 shell 脚本的知识，因为你会注意到有重复的模式，它们可以被提取出来以便更好地重用代码。最终结果是一个更精简的系统。请注意，根据最新的 StackOverflow 调查，shell 脚本是[收入最高的技术之一。](https://insights.stackoverflow.com/survey/2021#section-top-paying-technologies-top-paying-technologies)

# 分布式源

这里的命令主要假设使用`zsh` ( `.zshrc`)，但是即使使用`bash` ( `.bashrc` / `.bash_profile`或者其他 shells，思路也是一样的。

## 1.创建脚本文件夹

我们需要创建目标文件夹来包含分割的文件。为了简单起见，我们可以创建`~/zshrc`文件夹(即没有一个点来区分它和主`~/.zshrc`文件)。

## 2.创建脚本文件

我们可以开始查看`.zshrc`文件的内容。然后，像下面的文件一样将每个主题的相关内容分组。注意，文件扩展名无关紧要，`source` shell 命令只关心文件内容。我只是使用`.sh`作为`zsh`和`bash`外壳的通用/总括术语:

```
~/zshrc
  ├── git.sh
  ├── js.sh 
  ├── python.sh
  ├── django.sh
  ├── docker.sh
  ├── general.sh
  ├── init.sh
```

这样我们就有了两个特殊的文件:

*   `init.sh`包含外壳(`zsh`)需要的东西，比如初始化器、插件、主题等等。
*   `general.sh`包含所有不属于当前分割文件的内容，太少以至于不能保证一个专用文件。这将是不容易分类的东西的默认文件。

## 3.更新主加载程序

然后，我们的`~/.zshrc`可以简单地加载/获取文件夹中所有的`.sh`分割文件。更具体地说，你可以使用`*.sh`而不是仅仅`*`:

```
for FILE in ~/**zshrc**/*; do
    **source** $FILE
done
```

因此，我们会有这样的逻辑结构:

```
~/.zshrc
~/zshrc
  ├── git.sh
  ├── js.sh 
  ├── python.sh
  ├── django.sh
  ├── docker.sh
  ├── general.sh
  ├── init.sh
```

## 未来更新

我们现在有了一个可扩展的系统。对于未来的变化:

*   检查新的别名/命令/envvar 是否可以放入公共分割文件中(例如在`git.sh`、`python.sh`等)并放在那里
*   如果它不属于任何地方，就把它放在`general.sh`作为默认目的地
*   一旦`general.sh`中已经有了大量的相关内容，你可以把它们放在一个新的专用/分割文件中。

它的美妙之处在于`~/.zshrc`甚至会自动加载新添加的文件。因此，不需要更新它的内容。

# 关键来源

其他人可能会在这一点上停止，如果他们对`~/.zshrc`中的上述命令没有意见的话。但是有时有些脚本需要在其他脚本之前先加载，您可以将它们放在`init.sh`中。

有各种策略可以解决这个问题，但最简单的一个是:

*   先装上`init.sh`
*   然后，加载其他脚本

## 使用“查找”实用程序

我们可以像这样:

```
# Load the 'init.sh'.
**source** ~/zshrc/init.sh# Find all '.sh' files in ~/zshrc, exclude 'init.sh'.
FILES_STR=$(find ~/zshrc -name '*.sh' -not -name 'init.sh')# `tr` is a find-and-replace utility.
# Outer () will convert the output of $() to array.
FILES=($(echo $FILES_STR | tr '\n' ' '))for FILE in **$FILES**; do
    **source** $FILE
done
```

## 使用“fd”实用程序

如果你像我一样是`[fd](https://github.com/sharkdp/fd)`的粉丝，它是`find`的现代/更快版本，你只需要改变`FILES_STR`的值:

```
# Load the 'init.sh'.
**source** ~/zshrc/init.sh# Find all .sh files in ~/zshrc, exclude 'init.sh'.
FILES_STR=$(fd --glob '*.sh' --exclude 'init.sh' ~/zshrc)# 'tr' is a find-and-replace utility.
# Outer () will convert the output of $() to array.
FILES=($(echo $FILES_STR | tr '\n' ' '))for FILE in **$FILES**; do
    **source** $FILE
done
```

# 关键要点

*   Shell 启动脚本对于提高我们的效率非常有用。
*   从单个`.zshrc` / `.bashrc`或者从其分割的文件中获取资源在性能上没有明显的区别。
*   分割文件将意味着更好的模块化、关注点分离和可伸缩性。未来的更新将更加容易和易于管理。
*   每个文件的范围越小，意味着越容易发现过时和未使用的内容，或者会迫使您重构它们的重叠使用。这个过程可以增强您的 shell 脚本技能。
*   我们可以先加载关键文件，然后加载其他非关键文件。我们可以使用`find` / `fd` CLI 实用程序来查找/排除文件。

感谢您的阅读。如果你发现了一些价值，请关注我，或者为文章鼓掌/评论，或者[请我喝杯咖啡](https://ko-fi.com/ranelpadon)。这对我意义重大，鼓励我创作更多高质量的内容。