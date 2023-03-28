# 从 lambda 调用 lambda

> 原文：<https://medium.com/codex/invoke-a-second-lambda-from-an-existing-aws-lambda-using-python-2de8ee4b8cc9?source=collection_archive---------0----------------------->

## 使用 python 进行 AWS lambda 同步和异步调用

![](img/a6d79bba612bef9c0f74cce753782770.png)

伊恩·巴塔格利亚在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**注:非会员也可在**[**https://dineshkumarkb . com/tech/invoking-a-lambda-from-a-lambda/**](https://dineshkumarkb.com/tech/invoking-a-lambda-from-a-lambda/)**获取本文。**

我们将在这里讨论如何使用 python 从一个 lambda 函数调用第二个 lambda 函数，并将数据传递给同一个函数。

下面是从现有 lambda 函数调用第二个 lambda 函数的 boto3 方法签名。

[https://boto 3 . Amazon AWS . com/v1/documentation/API/latest/reference/services/Lambda . html # Lambda。Client.invoke](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/lambda.html#Lambda.Client.invoke)

您可以调用 aws lambda 函数

1.  **同步**(并等待响应)或
2.  **异步**。

要异步调用函数**，将`*InvocationType*`设置为`Event.`**

> **事件—异步调用函数。API 响应只包含一个状态代码。**

**当调用类型被设置为 Event 时，调用 lambda 不期望来自被调用 lambda 的响应。它只是触发并继续处理代码的其他部分。这可以在第二个 lambda 执行一些耗时的操作时使用，比如创建一个 aws sagemaker notebook 实例，并等待它的状态变为 InService，这大约需要 2 分钟。**

**要同步调用功能**，将*请求响应*设置为`Event.`****

> ****RequestResponse(默认)—同步调用函数。保持连接打开，直到函数返回响应或超时。API 响应包括函数响应和附加数据。****

****当调用 lambda 需要来自被调用 lambda 的确认时，lambda 调用可以是同步的。如果操作成功，服务将发回以下 HTTP 响应。****

****对于`RequestResponse`调用类型，这个状态代码是 200。对于`Event`调用类型，这个状态代码是 202。对于`DryRun`调用类型，状态码是 204。****

****有效载荷包含从 lambda 1 到 lambda 2 共享的数据。有效负载应该是 json 对象类型，可以从 lambda 2 中检索，如下所示。****

****[https://docs . AWS . Amazon . com/lambda/latest/DG/API _ invoke . html](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)****