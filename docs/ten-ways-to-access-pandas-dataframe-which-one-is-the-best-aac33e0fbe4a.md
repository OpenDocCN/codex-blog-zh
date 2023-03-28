# 访问熊猫数据框的十种方法，哪一种最好？

> 原文：<https://medium.com/codex/ten-ways-to-access-pandas-dataframe-which-one-is-the-best-aac33e0fbe4a?source=collection_archive---------5----------------------->

![](img/35cd24f7b2eabdf7b2c190ef1122df7a.png)

从熊猫的数据帧中选择数据，我们可以用`df_data['column']`，也可以用`df_data.column`，那么，`df_data.loc['column']`，对，也可以用`df_data.iloc['index']`。接下来，`pd.eval()`，别忘了`df_data.query()`。如果以上还不够，还有一个叫 numexpr 的包，还有很多。