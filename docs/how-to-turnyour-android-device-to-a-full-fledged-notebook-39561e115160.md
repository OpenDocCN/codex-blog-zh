# 如何将你的安卓设备变成一台功能齐全的笔记本电脑

> 原文：<https://medium.com/codex/how-to-turnyour-android-device-to-a-full-fledged-notebook-39561e115160?source=collection_archive---------1----------------------->

## 是的，你可以在你的 Android 设备上运行一个成熟的 Linux 操作系统！

![](img/26283a26b5014026b525e91170edab94.png)

劳伦·曼克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

想象你在一个孤岛上。和你在一起:只有你的安卓平板和一个蓝牙键盘。(支持多线程的平板电脑或手机，例如 Samsung SM-T580 或更高版本)。

如何用这个设置编码？

在 Google Playstore 中，你既找不到任何有用的解释器，也找不到适合你的编程语言的编译器(我试过一些 Python、Lisp 和 R——它们不适合开发人员),也找不到适合你的编程语言的成熟的文本或代码编辑器(如果我错了，请在评论部分告诉我我错了！).如果你想编辑或测试你的代码，同时阅读一个 pdf，并在互联网上浏览代码，似乎不可能用你的 Android 设备来完成这一切。

一旦你尝试使用智能手机或平板电脑编码，你就会意识到一个成熟的操作系统是严肃工作的必需品。

但是，在你的 Android 智能手机或平板电脑中有一个解决方案可以做到这一切:`Termux`！

`Termux`是一款适用于 Android 手机和平板电脑的 linux shell 仿真器。它在谷歌 Playstore 中免费提供。

在`Termux`上，可以安装并运行一个完全成熟的 Linux 操作系统:Arch Linux for Termux ( `TermuxArch`)。

选择 Arch Linux 是经过深思熟虑的:在所有成熟的 Linux 发行版中，Arch Linux 在内存占用方面效率最高。与此同时，它是互联网上最好的有文档记录的 Linux 发行版，同时在 Linux 软件方面总是最新的(`pacman`中的软件包通常在软件升级后立即出现，而 Debian 软件包——例如 Ubuntu 中的软件包— `apt` —必须等待下一个发行版可用。(Arch Linux 社区是适应新软件版本最快的)。`TermuxArch`在`Termux`内运行时没有图形桌面(X11)，但可以通过在您的 Android 设备中并行运行一个 XServer 程序并将其与`TermuxArch`连接来获得一个。通过这一点，你可以在手机或平板电脑中运行一个成熟的 Linux 桌面，可以通过触摸屏或通过连接到手机/平板电脑的蓝牙鼠标和蓝牙键盘进行控制。

这个过程很复杂，但是可行而且有效！

# 安装 Termux

```
# go to google playstore > Termux > install
```

# 安装`XServer XSDL`

```
# go to google playstore > XServer XSDL > install
```

如果您启动 XServer XSDL，您将看到如下消息:

```
export DISPLAY=192.170.1.100:0
export PULSE_SERVER=192.170.1.100:4713
x-window-manager & firefoxexport DISPLAY=:0 
export PULSE_SERVER=tcp:127.0.0.1:4713
```

记下这些信息或截图。我们稍后需要这些信息，以便将 XServer 显示与 TermuxArch 实例连接起来。

# 安装 Git，然后将 TermuxArch 安装到 Termux 中

需要安装`git`才能安装 TermuxArch。

Termux 包的管理者是`pkg`。要将任何可用的包安装到 Termux shell 中，您可以在 shell 中键入:`$ pkg install <your-package-name>`。

要安装`git`，你输入:

```
$ pkg install git
```

Git 将 TermuxArch 存储库克隆到您的 Termux 中:

```
$ git clone https://github.com/sdrausty/TermuxArch
```

将 TermuxArch 的先决条件安装到 Termux 中:

```
$ pkg install bsdtar
$ pkg install proot
```

然后，`cd`进入下载的 TermuxArch 文件夹，安装 TermuxArch:

```
$ cd TermuxArch$ bash setupTermuxArch.sh
```

成功安装后，安装程序将在 TermuxArch shell 中保持活动状态！

在 TermuxArch shell 内部，配置`pacman`，它是 Arch Linux/TermuxArch 的包管理器(对应 Ubuntu 中的`apt`):

```
# add mirror for pacman in arch
# https://bbs.archlinux.de/viewtopic.php?id=14528
$ nano /etc/pacman.d/mirrorlist # (or instead of nano - use your text editor of choice)# uncomment a server in your country there - for me in Germany
# and save the changes. In nano: `C-x y (or j depending on language)`.
```

在此之后，立即更新`pacman`(这些是 Arch Linux 对 Ubuntu Linux 中的`$ sudo apt update`和`$ sudo apt upgrade`的对应命令):

```
$ pacman -Syy
$ pacman -Syu
```

# 安装 X 所需的程序(用于 TermuxArch)

安装并启动`lxde`，一个用于 Arch Linux / TermuxArch 的轻量级 GUI:

```
# install necessities for X:
pacman -S xorg-server 
pacman -S lxde # install all!
pacman -S ttf-dejavu
```

# 将您正在运行的 TermuxArch 与 XSERVER 连接，以获得桌面 GUI

将`lxde`与 XSERVER 连接:

```
# inside your running TermuxArch shell inside termux, 
# give what is desired for connection of X into the TermuxArch terminal:
# (remember, this was the info, which XSERVER displayed before!) $ export DISPLAY=192.170.1.100:0
$ export PULSE_SERVER=192.170.1.100:4713
# and then start your graphical program
$ startlxde# after this, change to the XSERVER screen
# you will see the running ArchLinux Desktop there!
```

瞧啊。您的 LXDE 应该在 XSERVER 屏幕中启动，并使用蓝牙鼠标和蓝牙键盘，这应该感觉像是在您的 Android 设备中使用 GUI 访问成熟的操作系统！

Android 和 Termux 最重要的区别是:现在你可以在你的 TermuxArch 中安装任何可安装的 Linux 软件——既不是 Android 应用也不是 Termux 包——而是普通的桌面(Arch) Linux 软件。—在 TermuxArch 中，您可以安装编程所需的任何 Linux 软件，不再依赖现有的 Android 应用程序！—这难道不是一个巧妙的破解吗？

```
# e.g. to program lisp using emacs and sbcl, I installed:
pacman -S sbcl
pacman -S emacs
```

然而，唯一的限制是，这些程序必须为您的平板电脑的架构而构建，并且同时在`pacman`中可用。此外，程序的内存占用不应超过设备中的 RAM。

有了这个设置(带有 XSERVER 的 Termux 中的 TermuxArch)，我可以:

1.  打开一本 PDF 书籍进行编程
2.  并排运行 Emacs 来做笔记
3.  甚至运行一个连接到 Emacs 的语言(sbcl)解释器，这样我就可以测试我写下的代码。
4.  甚至在互联网上浏览，搜索所选编程语言的功能——因为 Firefox 或 Chrome 会占用太多内存，我`pacman`安装了一些内存占用较少的浏览器:[苍白的月亮](https://en.wikipedia.org/wiki/Pale_Moon_(web_browser))。

(如果您是 Mac iPad 或 iPhone 用户，您可能会在 iSH 中找到与 Mac 版 Termux 最接近的等效功能。如果你尝试过，请写在评论里——或者留下一个链接，链接到你的关于这个的媒体文章:)

编码快乐！—享受具有笔记本功能的变形 Android 设备！