# 5 个强大的 Python 一行程序

> 原文：<https://medium.com/codex/5-powerful-python-one-liners-260dd61c4d09?source=collection_archive---------1----------------------->

> Python 一行程序可以像用另一种语言编写的用来做同样事情的冗长而乏味的程序一样强大。在其他语言中，这几乎是不可能的(T4)，但是在 Python 中，这要容易得多。诀窍是想出“用一点点做很多事”的东西。最重要的是，阅读和编写关于 Python 的一行程序(例如，在本文中)非常有趣！甚至有一种亚文化围绕着谁能为一个给定的问题写出最短的代码。

![](img/2292011d888f18fdf8b24d9c3943f2d0.png)

## 1.将您的 Wi-Fi 密码打印成二维码，与您的客人分享

```
echo "WIFI:T:WPA;S:NETWORK_NAME;P:PASSWORD_HERE;H:;" | qr --output=wifi.png
```

![](img/8ecb6b096dbed1028d80beded8ea3057.png)

要运行此命令，您需要安装两个依赖项:

`pip install pillow`和`pip install qrcode`

## 2.CSV 到 JSON

```
python -c "import csv,json;print json.dumps(list(csv.reader(open('csv_file.csv'))))"
```

## 3.将正则表达式应用于 stdin 中的行

```
[another command] | python -c "import sys,re;[sys.stdout.write(re.sub('PATTERN', 'SUBSTITUTION', line)) for line in sys.stdin]"
```

好的，这一个还有其他工具可以用更短的命令完成同样的任务。但我喜欢的是它不太可能面临不兼容，例如`sed`，它可能在 Linux 和 Mac 上有不同的实现。

## 4.剖析 python 脚本

```
python -m cProfile foo.py
```

查看如何使用它[在这里](https://docs.python.org/3/library/profile.html#instant-user-s-manual)。

## 5.在当前目录下启动 web 服务器

```
$ python -m SimpleHTTPServer 8000
```

或者在 python3 上

```
$ python -m http.server
```

我过去经常在同一个网络内的机器之间共享文件。

你知道其他有用的俏皮话吗，请在评论中分享。