# macOS 上的 Git SSH 认证

> 原文：<https://medium.com/codex/git-authentication-on-macos-setting-up-ssh-to-connect-to-your-github-account-d7f5df029320?source=collection_archive---------0----------------------->

## 为您的 GITHUB 帐户设置 SSH

## 如何在 Github 帐户上设置 SSH 并在本地保存凭证，以便从终端启用 git 操作

![](img/827f9e7195cfa168b11e57dd1848273b.png)

根据 [T. Q.](https://unsplash.com/@tq_photos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/mac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片和 [Yancy Min](https://unsplash.com/@yancymin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片创建

**本文针对 macOS 用户。**

在本文中，我将带您了解如何在 mac 上设置 SSH 来连接您的 Github 帐户。其实挺简单的！下面是为 Git 操作设置 SSH 的步骤:

1.在您的计算机上生成新的 SSH 密钥(或使用现有的 SSH 密钥)

2.将 SSH 密钥添加到 ssh-agent

3.将 SSH 密钥添加到您的 Github 帐户

*截至 2021 年 8 月 13 日，Github 停止支持用户使用账号密码进行任何经过认证的 Git 操作。*

您必须设置一个基于令牌的认证方法，以便能够实现 Git 操作并连接到您本地机器上的 Github 帐户——使用 SSH 是一种方法。

现在，让我们深入细节！首先，如果您的计算机上已经有一个 SSH 密钥，您可以使用该密钥连接到您的 Github 帐户，而不是生成一个新的 SSH 密钥。

**如果您已经有一个现有的密钥**并且想要使用这个密钥连接到您的 Github 帐户，您可以**直接进入步骤 2:将 SSH 密钥添加到 ssh-agent。**

**如果您没有现有的密钥**或者想要为您的 Gihub 帐户使用新密钥，您可以**向下滚动到步骤 1:在您的计算机上生成 SSH 密钥。**

# **【可选】检查任何现有的 SSH 密钥&SSH 密钥的简要回顾(如果您愿意，可以跳过这一步)**

要检查您是否有现有的 SSH 密钥，请在您的终端中键入以下命令:

```
ls -al ~/.ssh
```

如果您有一个或多个现有的 SSH 密钥，它们将存储在您的根目录中的`.ssh`文件夹中，例如如下所示:

```
id_rsa
id_rsa.pub
id_ed25519
id_ed25519.pub
```

注意，每个 SSH 密钥都是成对的私有和公共密钥。私钥是你不应该与任何人分享的，而应该保存在你的本地机器中。公钥是您将添加到 Github 帐户中的那个。

*   `id_rsa`是私钥
*   `id_rsa.pub`是一个公钥

*还要注意，您的计算机上可以有多个 SSH 密钥对。*这也意味着**即使你已经有一个现有的 SSH 密钥，你也可以生成一个新的用于连接你的 Github 账户。**

在这种情况下，当您生成一个新的 SSH 密钥时，您可以命名新的密钥以更好地识别其特定用途，例如:

```
id_rsa_github
id_rsa_github.pub
```

您可能已经注意到了 SSH 密钥文件名称中的不同关键字:例如上面例子中的`rsa`和`ed25519`。这些表示用于 SSH 密钥的加密算法的类型。**广泛推荐(Github 也推荐)对 SSH 密钥使用** `**ed25519**` **加密。现在，如果您还没有 SSH 密钥，让我们生成一个新的密钥！**

# **1。在你的电脑上生成一个 SSH 密钥**

按照以下步骤生成新的 SSH 密钥:

## 1.打开终端

## 2.输入以下命令:

```
ssh-keygen -t ed25519 -C "*your_email@example.com*"
```

*   用你的 Github 帐户电子邮件替换 your_email@example.com
*   `-C` flag 设置注释或标签来识别您的 SSH 密钥，使用您的电子邮件作为注释是一种常见的做法
*   `-t ed25519`将您的 SSH 密钥的加密算法设置为`ed25519`

## 3.输入保存密钥的路径和文件名

当您得到以下提示时:

```
>Generating public/private ed25519 key pair.
>Enter a file in which to save the key (/Users/*you*/.ssh/id_ed25519):
```

*   如果您只需按 enter 键，您的新 SSH 密钥对将以默认名称(`id_ed25519`)保存在默认位置(`/.ssh`)，如提示的括号内所示
*   如果您想用不同的名称保存新的 SSH 密钥对，您可以复制括号(`Users/you/.ssh/id_ed25519`)内的内容，并用您想要的名称替换名称部分，如下所示:

```
>Generating public/private ed25519 key pair.
>Enter a file in which to save the key (/Users/*you*/.ssh/id_ed25519):
**/Users/you/.ssh/id_ed25519_github**
```

如果您得到一个错误消息，说`/.ssh`目录不存在，您可能需要在根目录中手动创建`/.ssh`目录*。*第一次运行`ssh`命令时会自动生成`/.ssh`文件夹，但如果您从未这样做过，您可以手动创建:

```
mkdir ~/.ssh
```

## 4.输入密钥的密码[可选]

当您得到以下提示时:

```
>Enter passphrase (empty for no passphrase):
```

*   只需按 enter 键就可以生成 SSH 密钥，而不需要密码
*   或者输入密码以增加一层安全性

按下 enter 键后，您将得到一个提示，指示您的私钥(身份)和公钥的保存位置，您的密钥指纹是什么，以及您的密钥的 randomart 图像。

您已经成功生成了一个新的 SSH 密钥！如果查看一下您的`/.ssh`目录，您应该会看到以下内容:

```
id_ed25519_github
id_ed25519_github.pub
```

# 2.将 ssh 密钥添加到 SSH 代理中

要使用 SSH 连接到远程服务器(在我们的例子中是 Github ),我们需要首先向 ssh-agent 添加 SSH 密钥。

## 1.在后台启动 ssh-agent:

```
eval "$(ssh-agent -s)"
```

**(或)**

```
exec ssh-agent zsh
```

用`bash`或者你正在使用的任何一个外壳替换`zsh`。

## 2.将您的 ssh 私有密钥添加到 SSH 代理中

```
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_github
```

*   确保使用默认的 macOS `ssh-add`命令，而不是由外部资源(如自制软件)安装的命令(如果有)
*   `--apple-use-keychain` flag 是苹果的标准版`ssh-add`。这会将您的 SSH 密钥的密码自动添加到钥匙串中，这样您就不必在每次建立 SSH 连接时都输入密码(以前，标记为`-K`)
*   如果您没有为您的密钥设置密码，您可以省略`--apple-use-keychain`标志
*   用您自己的 SSH 密钥的名称替换`id_ed25519_github`

## 3.为一些方便的选项设置配置文件

您可以使用各种选项为 SSH 设置一个配置文件。出于我们的目的，让我们看看两个有用的选项:

*   自动将您的 ssh 密钥添加到 SSH 代理中
*   将您的密码储存在钥匙串中

如果您的`/.ssh`文件夹中还没有`config`文件，请创建一个:

```
touch ~/.ssh/config
```

然后，打开`config`文件并粘贴以下内容:

```
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519_github 
```

*   根据需要替换 SSH 密钥名(`id_ed25519_github`)

# 3.将 SSH 密钥添加到您的 Github 帐户

最后一步是将 SSH 密钥添加到您的 Github 帐户。

## 1.复制您的 SSH 公钥的内容

```
pbcopy < ~/.ssh/id_ed25519_github.pub
```

**(或)**只需打开`id_ed25519_github.pub`文件，复制全部内容。

## 2.去 Github.com，把复制的公钥内容添加到你的账户设置里

*   点击右上角的头像>在下拉菜单中，点击**‘设置’**
*   在页面左侧的用户设置工具条中，点击**‘SSH 和 GPG 键’**
*   点击绿色**‘新 SSH 密钥’**按钮
*   在**“标题”**字段中，输入您的密钥的描述性名称(例如，个人 MacBook Pro)
*   在**‘Key’**字段中，粘贴您的 SSH 公钥的复制内容。
*   点击**‘添加 SSH 密钥’**按钮
*   出现提示时，输入您的 Github 帐户密码进行确认。

您已经成功地将您的 SSH 密钥添加到您的 Github 帐户中！现在，您可以使用 SSH 连接到您的 Github 帐户，并在您的本地机器上执行任何身份验证所需的 Git 操作。

# 测试您的 SSH 连接

现在，让我们测试一下我们到 Github 的 SSH 连接是否工作！当您第一次尝试建立到 Github 的 SSH 连接时，例如，当您第一次`git clone`您的 GitHub repo 时，您可能会得到如下提示:

```
>The authenticity of host 'github.com (IP ADDRESS)' can't be established. 
>RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
>Are you sure you want to continue connecting (yes/no)?
```

这是因为要建立到远程主机(在我们的例子中是 Github)的 SSH 连接，该主机必须是已知的主机。**确保密钥指纹与 Github 的 RSA 公钥指纹匹配，然后输入‘yes’**—这将把 Github 作为已知主机添加到`/.ssh`文件夹的`known_hosts`文件中。现在，您将能够成功地建立连接！

# 资源

GitHub 文档:

 [## 检查现有的 SSH 密钥

### 注意:不再支持 DSA 密钥(SSH-DSS)。现有密钥将继续起作用，但您不能添加新的 DSA…

docs.github.com](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)  [## 生成新的 ssh 密钥并将其添加到 SSH 代理中

### 如果您还没有 SSH 密钥，您必须生成一个新的 SSH 密钥来用于身份验证。如果你不确定…

docs.github.com](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) [](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) [## 向您的 GitHub 帐户添加新的 SSH 密钥

### 在向你的 GitHub 帐户添加新的 SSH 密钥之前，你应该:在向你的 GitHub 帐户添加新的 SSH 密钥之后…

docs.github.com](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)  [## 测试您的 SSH 连接

### 在测试您的 SSH 连接之前，您应该有:当您测试您的连接时，您需要验证这个…

docs.github.com](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/testing-your-ssh-connection)