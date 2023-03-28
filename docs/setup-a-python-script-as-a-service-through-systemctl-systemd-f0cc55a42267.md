# 通过 systemctl/systemd 将 python 脚本设置为服务

> 原文：<https://medium.com/codex/setup-a-python-script-as-a-service-through-systemctl-systemd-f0cc55a42267?source=collection_archive---------0----------------------->

![](img/f4f607208d1c51e6a093164ea1cf3a2f.png)

Muhannad Ajjan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有几种方法可以让你的程序在 Linux 中作为后台服务运行，比如 **crontab，。但是今天我要写 systemd。我最初在寻找一种方法，将我的 python 脚本作为后台服务运行，这样即使服务器由于某种原因重启，我的脚本也可以在后台运行，我发现 systemd 允许我这样做。我们开始吧**

我将在一台 Ubuntu 18.10 机器上进行设置。

几乎所有版本的 Linux 都自带了 **systemd** ，但是如果您的没有自带，那么您可以简单地运行以下命令:

```
sudo apt-get install -y systemd
```

**注意:**-y 标志表示快速安装软件包和依赖项。

要检查 systemd 的版本，只需运行以下命令:

```
systemd --version
```

创建一个你喜欢的 python 文件。我要把我的叫做 test.py。

**sudo nano test.py**

```
import time
from datetime import datetime while True:
    with open("timestamp.txt", "a") as f:
        f.write("The current timestamp is: " + str(datetime.now()))
        f.close()
    time.sleep(10)
```

上面的脚本将每隔 10 秒钟在文件中写入当前时间戳。现在让我们编写服务。

**sudo nano/etc/systemd/system/test . service**

```
[Unit]
Description=My test service
After=multi-user.target[Service]
Type=simple
Restart=always
ExecStart=/usr/bin/python3 /home/<username>/test.py[Install]
WantedBy=multi-user.target
```

在你的操作系统中写有 **<用户名>** 的地方插入用户名。ExecStart 标志接收你想要运行的命令。所以基本上第一个参数是 python 路径(在我的例子中是 python3 ),第二个参数是需要执行的脚本的路径。**重启**标志设置为总是，因为如果服务器重启，我想重启我的服务。关于这个的更多信息，你可以去这个[链接](https://www.freedesktop.org/software/systemd/man/systemd.service.html)。现在我们需要重新加载守护进程。

```
sudo systemctl daemon-reload
```

让我们启用我们的服务，以便在服务器重启时不会被禁用。

```
sudo systemctl enable test.service
```

现在让我们开始服务。

```
sudo systemctl start test.service
```

现在我们的服务已经开始运行。

**注意:**文件会写在根目录 *(/)* 因为程序会从 systemd 的角度写在 path 里。要改变它，只需编辑掉文件路径。例如:

```
import time
from datetime import datetimepath_to_file = "enter the desired path of the file"while True:
    with open(path_to_file, "a") as f:
        f.write("The current timestamp is: " + str(datetime.now()))
        f.close()
    time.sleep(10)
```

您可以使用几个命令来启动、停止、重启和检查状态。

停止服务。

```
sudo systemctl stop *name_of_your_service*
```

重新启动。

```
sudo systemctl restart *name_of_your_service*
```

检查状态。

```
sudo systemctl status *name_of_your_service*
```

这是对 systemd 的一个非常基础的介绍，面向那些想要开始为 python 编写自己的 systemd 服务的初学者。如果你想深入了解 systemd 和 systemctl，这里有一个由数字海洋提供的[详细指南。](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)

**注意:**这不仅仅适用于 python 脚本。你可以用它运行任何程序，不管你的程序是用什么语言写的。