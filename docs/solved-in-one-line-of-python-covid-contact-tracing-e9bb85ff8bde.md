# 在 Python 的一行中解决— Covid 接触追踪

> 原文：<https://medium.com/codex/solved-in-one-line-of-python-covid-contact-tracing-e9bb85ff8bde?source=collection_archive---------6----------------------->

![](img/a2a30d7c87cc6d320a158c129d564f54.png)

我们有一本字典，代表了一个彼此联系密切的人的网络。

```
network = {
    0: [4],
    1: [4],
    2: [4,3],
    3: [2,5,6],
    4: [0,1,2],
    5: [3,6],
    6: [3,5],
    7: [8,9],
    8: [7],
    9: [7]
}
```

在这里，1 个整数代表 1 个人。键代表每个人，而值代表那些…