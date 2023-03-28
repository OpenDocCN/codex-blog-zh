# 如何使用 Python 编写 FTP 通信脚本

> 原文：<https://medium.com/codex/how-to-write-an-ftp-communication-script-using-python-3c4b94b0f68?source=collection_archive---------10----------------------->

大家好！在这个博客中，我们将学习如何用 Python 写一个脚本，它可以执行我们的文件上传和下载的 FTP 请求。

![](img/6beb1a727e43ca8bc25d10b3611f9ad2.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Cup 先生/杨奇煜·巴勒](https://unsplash.com/@iammrcup?utm_source=medium&utm_medium=referral)拍摄的照片

在使用该脚本之前，您需要从 windows 命令行安装“ftplib”包。使用命令`pip install pyftpdlib`安装软件包。

导入所需的库。

我们可以将信息性消息打印到控制台。我们需要`user id`、`address`和`password`来执行 FTP 查询。我们将这些值存储在不同的变量中。

接下来，我们将检查`user id`、`address`和`password`的组合是否匹配一个有效的 FTP 服务器。Try-catch 块捕获此处发生的错误。

该脚本检查您是想要执行下载还是上传操作。对于上传，您为脚本指定文件名。假设脚本找到了文件(一个好的做法是将文件放在与脚本相同的目录中)。在这种情况下，上传操作完成，脚本退出会话，退出逻辑和 try-catch 块。同样的事情也适用于下载操作。

这是完整的脚本。

# 结论

终于！使用上面的方法，我们可以用 Python 写一个 FTP 脚本。感谢您的阅读，我会继续添加新的说明和示例。其他文章和更新，请在 [LinkedIn](http://linkedin.com/in/bkullukcu) 上与我联系。也可以在 [Github](https://github.com/bkullukcu) 上关注我。