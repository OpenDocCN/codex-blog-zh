# 带有 Backblaze B2 和电子邮件通知的 Linux 备份

> 原文：<https://medium.com/codex/linux-backup-with-backblaze-b2-and-email-notifications-3acdb5282fa1?source=collection_archive---------5----------------------->

![](img/d958a8d54585c5e63e0326e45af444b3.png)

我从备份配置中收到的定期电子邮件通知之一。

我和妻子长期使用 [Backblaze](https://www.backblaze.com/) 来备份我们 MacBooks 上所有的个人文件。

因此，当我最近第一次为自己构建了一个 Linux 桌面时，我想要一个同样简单的备份解决方案。不幸的是，简单自动备份应用的市场似乎仅限于 Windows 和 macOS。所以我不得不取消我的 Backblaze 个人备份帐户，去寻找一个 Linux 友好的选项。

针对 Linux 有一千零一种备份解决方案，但是它们都是针对系统管理员的——不像我过去习惯的那样“一劳永逸”。好的一面是，一旦我着手建立这些“**某些组装需要的**”解决方案之一，我每月的云存储账单就比以前便宜了很多，所以就有了这些。

我选定了一个需要最少护理和喂养的解决方案，它基于四个部分:

*   [**Backblaze B2**](https://www.backblaze.com/b2/cloud-storage.html)**作为云存储服务，并且**
*   **`**restic**`要管理备份快照，**
*   ****一个** `**systemd**` **定时器**来触发每日更新，并且**
*   **`**mailx**`每天给我发送备份成功的消息。**

**我并不期望继续使用 Backblaze 品牌(我认为我没有理由这样做)，但 B2 近年来因提供类似于 AWS s3 的云存储服务而引起了轰动。**

# **创建 Backblaze 存储桶**

**首先，创建一个 B2 帐户，并为您的备份设置一个存储桶(云存储术语“文件夹”)。**

**通过 B2 网络图形用户界面很容易做到这一点。如果您愿意，您也可以使用`b2` CLI 客户端。**

**![](img/c8cedd9526dfc1159416120415a3a2aa.png)**

**在 B2 创建一个存储桶。**

**你还需要设置一个**应用程序键**。记下密钥 ID 和密钥以备后用。**

# **设置 Restic 快照**

**现在我们需要一个知道如何**

*   ****扫描您的系统**以查找已更改的文件**
*   **使用写入时复制创建这些更改的快照**
*   ****管理您的快照历史记录****

**[Restic](https://restic.net/) 是目前这一领域流行的开源解决方案。它有一个漂亮的、统一的 CLI 界面，并且以**支持多种多样的存储后端**(包括 B2)而闻名。因为它很受欢迎并且得到了很好的支持，所以很容易从发行版的包管理器中安装(在我的例子中是`pacman`):**

```
sudo pacman -S restic
```

**老实说，我们想要一个*也能*触发夜间备份并在出错时向您发送通知的工具。但是对于 Linux 来说没有这样的工具存在:快照工具比比皆是，但是假设(虔诚地遵守 UNIX 传统)你会把它们和其他东西串在一起作为触发器和通知(见下文)。**

**使用 Restic，首先用`restic init`初始化**一个(受密码保护的)备份库文件，然后用`restic backup`命令添加到它。为了在 B2 存储桶中创建一个存储库，我们给命令加上前缀`-r b2:bucketname:file`，如下所示。****

**下面是我如何为我的台式机(被亲切地称为`alfred`)设置备份回购的:**

```
export B2_ACCOUNT_ID=<keyID>
export B2_ACCOUNT_KEY=<key>restic -r b2:mybucket-desktop-backups:alfred/ init
```

**`<keyID>`和`<key>`值需要是 Backblaze 在您创建密钥时显示的值。**

**现在，您可以使用以下命令手动触发系统到 B2 的备份:**

```
restic -r b2:mybucket-desktop-backups:alfred/ backup ~/Documents
```

**使用`snapshots`命令，您可以随时查看存储快照的历史记录:**

```
restic -r b2:mybucket-desktop-backups:alfred/ snapshots
```

# **设置邮件**

**跟我重复:**手动备份毫无价值。**让我们设置一个备份脚本，这样我们就可以自动创建快照。**

**然而，因为备份如此重要，所以我们需要通知，以便我们知道它在工作和/或是否出现了问题。`mailx`命令是实现这一点的一种很好的、轻量级的方式(不用跳进全面的`sendmail`深渊)。**

```
sudo pacman -S mailx
```

**现在我们需要通过**向** `**/etc/mail.rc**`添加以下行来配置合法的 **SMTP 服务器**。我有一个 GMail 帐户，所以我将利用 GMail 的 SMTP 服务:**

```
set v15-compat

set mta=smtp://username:[app-password]@smtp.gmail.com smtp-use-starttls
set from="Eric 'Siggy' Scott <username@gmail.com>"
```

**其中`[app-password]`是我的谷歌账户让我生成的 16 个字符的唯一密码。这让我可以绕过 2FA。**

**现在我们可以像这样发送邮件:**

```
echo "Test test test" | mailx --subject "Test from Alfred" destinationaddress@gmail.com
```

# **创建备份脚本**

**综合起来，这是我想要每天触发的**完整脚本**。**

**它在我的系统上备份了两个目录(我的文件夹和保存我所有克隆的库的目录)。**

**然后，它根据`restic`调用的**返回代码**构造并发送一个**电子邮件消息**，其中包含成功消息或失败消息:**

**为了方便起见，我选择将它放在`/usr/bin/backup_script.sh`中。**

# **触发器:创建一个系统计时器**

**同样，手动备份是没有价值的，所以我们希望这个脚本自动执行。**

**在 Linux 系统上，这个问题的当代答案是使用一个 T4 计时器。对于外行人来说，这是经典的`cron`工作的现代等价物——现在**我们仁慈的霸主** `**systemd**`已经成为所有 Linux 发行版核心的上帝使者。你可以在这里找到关于`systemd`定时器[的深入教程](https://opensource.com/article/20/7/systemd-timers)。**

**但是基本上，我们需要创建**两个**配置文件:一个*服务*和一个*定时器*。**

## **`backup.service`单位文件**

**首先，我们将构建一个调用备份脚本的`systemd` **单元文件**。单元将基本的**子程序**归档，这些子程序构成了`systemd`作业和服务的构件。**

**将以下内容放入文件`/etc/systemd/system/backup.service`**

```
[Unit]
Description=Restic backup script
Wants=backup.timer[Service]
Type=oneshot
ExecStart=/usr/bin/backup_script.sh
User=yourusername[Install]
WantedBy=multi-user.target
```

***注意*:重要的是**要包含** `User=yourusername`(在我的例子中是`User=eric`)，否则我们的备份脚本将以 root 用户身份运行——这除了通常令人害怕之外，还会导致`restic`无法使用其缓存功能，导致您的备份每次都要花费**小时而不是**秒钟！**

**为了确保备份服务正常工作，我们可以像这样手动触发它:**

```
systemctl start backup.service
```

## **`backup.timer`单位文件**

**其次，我们将建立一个单元文件，它使用一个计时器在每晚 2:00AM 触发一次**备份服务。****

**把这个放在`/etc/systemd/system/backup.timer`:**

```
[Unit]
Description=Daily Backup Script
Requires=backup.service [Timer]
Unit=backup.service
OnCalendar=*-*-* 02:00:00
; Run immediately if the system was off
Persistent=true[Install]
WantedBy=timers.target
```

**这里特别重要的是`Persisent=true`行。没有它，如果机器在凌晨 2 点关机或睡眠，定时器**不会被触发。因为这是我的工作站，不是服务器，所以几乎总是这样！****

**`Persistent=true`确保作业将在凌晨 2 点后的第一个机会*运行。因此，**实际上，我的备份在每天早上 7 点左右运行，也就是我第一次坐在机器前的时候。*****

## **启动计时器**

```
systemctl start backup.timer
```

**或者更重要的是，`enable`定时器，这样它会在每次系统重启时激活:**

```
systemctl enable backup.timer
```

**瞧啊。像这样的信息现在是我早晨例行公事的一部分:**

**![](img/880a3aa2e0550ab65b75cc994a96457f.png)**

## **排除故障**

**我们单位档案的所有日志都是由`journald`、`systemd`的承租人(也是老`syslog`传统的替代者)收集的。**

**当您试图确保您的`systemd`服务和定时器正确运行时，以下是一些有用的命令:**

```
journalctl -S today -u backup.service
journalctl -t systemd | grep backup.service
journalctl -t systemd | grep backup.timer
journalctl -t backup_script.sh
```

# **旁白:用 Git 管理系统配置**

**为了设置这个，我们**接触或创建了相当多的**配置文件和脚本。Linux 是关于自由配置的，但是它让我很不舒服，因为它积累了很多像这样的未记录的和高度特定的系统调整。**

*   **实施起来很费时间**
*   **他们可能会被系统更新意外**核爆****
*   **当我重新安装我的操作系统时，它们都从宇宙中消失了**
*   **很难记住哪些配置文件携带了重要的定制内容，哪些只是锅炉板或 cruft(我在看你，点文件在 T7！)**

**当然，如果你陷入真正的重配置活动——比如设置一个轻量级窗口管理器之类的，而不是选择 Gnome 的默认设置，问题会变得更糟。**

**出于这些原因，我非常喜欢在一个 **git 仓库**中跟踪我所有的手工 Linux 定制。信不信由你，我实际上把我的**根目录**变成了一个巨大的 git repo。然而，我使用了一个`.gitignore`文件，因此在默认情况下，除非我显式地提交它，否则不会跟踪任何东西。**

**这里有一个[，所以张贴](https://stackoverflow.com/questions/505467/can-i-store-the-git-folder-outside-the-files-i-want-tracked)如何做到这一点。诀窍是将回购的`git-dir`与`work-tree`分开。**