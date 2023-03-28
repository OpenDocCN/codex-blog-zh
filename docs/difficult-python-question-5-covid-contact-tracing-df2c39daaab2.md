# 棘手的 Python 问题 5-Covid 联系追踪

> 原文：<https://medium.com/codex/difficult-python-question-5-covid-contact-tracing-df2c39daaab2?source=collection_archive---------4----------------------->

![](img/5f1a857e170cd2543e31dfaba20ca8be.png)

我们得到了一本字典，代表了一个彼此联系密切的人的网络。

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