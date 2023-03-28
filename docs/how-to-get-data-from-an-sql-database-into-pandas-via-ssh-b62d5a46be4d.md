# 如何通过 SSH 从 SQL 数据库获取数据到 pandas

> 原文：<https://medium.com/codex/how-to-get-data-from-an-sql-database-into-pandas-via-ssh-b62d5a46be4d?source=collection_archive---------14----------------------->

使用 Python 建立到远程数据库服务器的安全连接，并以 pandas 数据帧的形式获取查询结果。

![](img/1ea451ddb8e59334e36b676fa750d58b.png)

图片由 Nick115 ( [Pixabay](https://pixabay.com/de/photos/panda-familie-pandas-niedlich-3811734/) )原创，作者修改。

对于许多数据科学/分析任务来说，知道如何从 SQL 数据库中检索数据至关重要。如果这个数据库托管在您公司之外的某个地方，您可能需要通过 SSH(安全外壳协议)建立一个连接，以便…