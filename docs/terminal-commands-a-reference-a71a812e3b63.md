# 终端命令:参考

> 原文：<https://medium.com/codex/terminal-commands-a-reference-a71a812e3b63?source=collection_archive---------11----------------------->

一个*命令列表*可以在终端中运行，像专家一样浏览目录和日志！大部分是针对 Linux 的，但也有一些可以在 Windows 上运行。

![](img/7c3fdf42dfb96c91f2c23993d0a027fd.png)

弗朗西斯科·温加罗拍摄的照片

# 激光唱片

*   `cd` —将您带到您的主目录
*   `cd ..` —向上一个目录
*   `cd ../../` —向上两个目录等等…
*   `cd /` —转到您的根目录
*   `cd -` —带你回到你上次所在的目录

# 快捷键

在终端中点击这些

*   `ctrl + l` —清除端子
*   `ctrl + shift + plus`或者只是`ctrl + plus`——放大
*   `ctrl + shift + minus`或者只是`ctrl + minus` —缩小
*   `ctrl + a` —将光标移到行首
*   `ctrl + e` —将光标移至行尾
*   `ctrl + u` —删除光标前的所有内容
*   `ctrl + y` —将删除的内容粘贴到光标所在的位置
*   `ctrl + k` —删除光标之后的所有内容
*   `alt + backspace` —删除光标所在的单词
*   `ctrl + r` —搜索您已经运行过的命令

# 限位开关（Limit Switch）

*   `ls` —列出工作目录中的内容
*   `ls -l` —以自上而下的格式列出工作目录中的内容
*   `ll` —可以代替`ls -l`使用的 linux alias 命令；可能不适用于所有操作系统
*   `ls -al` —以自上而下的格式列出工作目录中的内容，还包括机密文件！…哦，太棒了
*   `la` —可以代替`ls -al`使用的 linux alias 命令；可能不适用于所有操作系统

# 检查日志文件

`less path/of/the/log/file.log` —比使用`cat`查看日志文件要好，但是您可能不得不使用`sudo`

> **专业提示:**如果您忘记在刚刚运行的命令前键入`sudo`，那么只需键入:
> 
> `sudo !!` —双感叹号是调用您刚刚运行的最后一个命令的快捷方式，而无需重新键入

`tail path/of/the/log/file.log` —类似于`less`命令，但它只会打印日志文件的最后 10 行，你可能也需要对这个命令使用`sudo`

按`q`退出日志页面

`tail -f path/of/the/log/file.log`—该命令将指定实时打印日志文件，用于实时监控，您可能也需要将`sudo`与该命令一起使用；使用 linux 操作系统中的日志文件路径`/var/log/auth.log`进行测试