# 我总是忘记的 SSH 命令

> 原文：<https://medium.com/codex/the-ssh-commands-i-always-forget-846488db192e?source=collection_archive---------3----------------------->

## 在 Mac 上配置 Git 的 SSH 连接。

![](img/277a0f85e28b67f30a9b928e556fdf13.png)

照片由[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

从显而易见的地方开始: [Bitbucket 的文档](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/)。

这些说明主要是给我的。我把它放在这里只是为了将来参考，因为我总是忘记它们。希望它们也能对某些人有用。

首先打开一个终端，生成 SSH 密钥。

```
$> ssh-keygen
```

系统会提示您输入文件和密码，请保留默认值。

现在转到密钥的目录`/Users/<youruser>/.ssh/`，并验证文件是否在那里。目录可能是隐藏的，所以，使用`cmd + shift + .`来显示隐藏的文件和目录。

确认文件存在后，返回控制台并运行:

```
$> ssh-add -K ~/.ssh/id_rsa
```

如果你改变了文件名，那么在前面的命令中把`id_rsa`改成实际的名字。

打开`id_rsa.pub`文件，复制内容— **注意。**在复制之前，您可以更改密钥的结尾，因为这是您在 Bitbucket 上识别它的方式。

打开 Bitbucket，到`View Profile > Manage Account > SSH Keys`点击`Add key`，然后简单粘贴`.pub`文件的内容，用`Add key`结束。

最后测试 git 命令，它应该可以工作。如果 git 不工作，那么调试时间也是！。