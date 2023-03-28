# 如何删除自己的 FB 帖子

> 原文：<https://medium.com/codex/how-to-delete-your-own-fb-posts-fc5857b2613?source=collection_archive---------13----------------------->

## 使用 Python 和 Selenium Web 驱动程序

一个 python 脚本，允许你删除你自己的脸书帖子。

它运行在 MacOSx 10.11.6 (El Capitan)上，在一个终端中，使用 python 3.9(最新的 selenium web 驱动程序)和 Mozilla 最新的 geckodriver。

使用 pip 安装 selenium。从他们自己的网站下载 Mozilla 的 geckodriver。将可执行文件复制到/usr/local/bin:这将把它放到可执行文件路径中，这是 selenium 需要它的地方。

编辑脚本 main.py 以设置身份验证变量，并运行该脚本。

已知问题:本地化。脸书经历了很多麻烦，使他们的网页不能自动脚本这样。所以我们必须通过标题来选择文章删除按钮。但是脸书用你选择的语言翻译了这个标题。如果那是法语，标题就是“Supprimer”。这就是 main.py 中的脚本所寻找的。对于其他语言，标题会有所不同，您需要相应地调整脚本。