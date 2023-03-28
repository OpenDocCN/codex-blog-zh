# 在 AWS 批处理上运行 Python 代码第 3 部分:创建作业队列、作业定义和运行作业。

> 原文：<https://medium.com/codex/run-a-python-code-on-aws-batch-part-3-creation-of-job-queues-job-definitions-and-run-jobs-6ee0dd49e582?source=collection_archive---------2----------------------->

![](img/6e1cd041c6751198e229b2c50a02affd.png)

标志；徽标

在前面的文章中，我们看到了如何创建容器并将容器上传到 AWS 存储库。如果您没有访问该文章，请单击下面的名称:

1.  [**在 AWS 批处理 part-1 上运行 Python 代码:创建 Python 脚本和 Docker 容器**:](/codex/run-a-python-code-on-aws-batch-part-1-creation-of-python-script-and-docker-container-1b01dc89eaed)[https://medium . com/codex/Run-a-Python-code-on-AWS-Batch-Part-1-Creation-of-Python-Script-and-Docker-Container-1 b 01 DC 89 eaed](/codex/run-a-python-code-on-aws-batch-part-1-creation-of-python-script-and-docker-container-1b01dc89eaed)
2.  [**在 AWS 批处理 Part-2 上运行 Python 代码:上传数据到 ECR 并创建计算环境**](/codex/run-a-python-code-on-aws-batch-part-2-uploading-data-to-ecr-and-creation-of-computing-c5dab12cd3eb) **。:**[https://medium . com/codex/run-a-python-code-on-AWS-batch-part-2-uploading-data-to-ECR-and-creation-of-computing-C5 dab 12 CD 3 EB](/codex/run-a-python-code-on-aws-batch-part-2-uploading-data-to-ecr-and-creation-of-computing-c5dab12cd3eb)

在本节课中，我们将运行 python 代码:

1.  创建作业队列
2.  创建作业定义
3.  创造就业机会
4.  运行作业

我们开始吧

1.  创建作业队列

首先，单击 AWS 仪表板上的作业队列，然后单击“创建”。

![](img/7416d8d92cc61f77947cb7c290327c7e.png)

作业队列 AWS 批处理仪表板

我将给出作业队列名称“test-queue-batch-v1 ”,您可以给出自己感兴趣的名称。我将给出的优先级是 1000，因为对于计算环境来说，优先级的整数值较高的作业队列具有优先权。

![](img/25e73bf736ae54556864fce8dcd994b8.png)

创建作业队列— 1

我将选择计算环境，这是容器运行该计算环境所必需的。在我的例子中，我将计算环境命名为“demo-batch-python-v1”。请单击单选按钮，确保单选按钮为蓝色。

![](img/a7e872578f69f24cc81060d5a0f3d957.png)

创建作业队列— 2

单击“create ”,我们将在状态中看到一条“VALID”消息。绿色横幅将出现在页面的顶部。

![](img/775395b137cd62ba96941f54e1c078a5.png)

作业队列仪表板

2.创建作业定义

如果我们看到 AWS Batch Dashboard 导航栏的左侧，我们会看到作业定义，单击它，然后单击“Create”按钮，就像我们在作业队列中所做的那样。

![](img/779b792083f4a31de3fd5f8d0734f7c1.png)

作业定义仪表板

点击“创建”按钮后，将进入表格，我们需要在表格中填写作业定义的某些参数。我取的名字是“test-job-def-v1 ”,我取的代码执行超时大约为 1 小时，如果我们的代码执行时间超过 1 小时，它将终止代码的执行。

![](img/35cf242af719d2267e4e34aeeae9cd6a.png)

工作定义表— 1

现在，为了平台兼容性，我们必须选择 EC2，如果您熟悉的话，也可以选择 Fargate。

![](img/80a6279cfeaef80c2dcf012cd96b0611.png)

之后，我们会给你你的码头集装箱的位置，你保存它。你可以在下面的图片中看到，我将我的容器存储在 sider Amazon ECR 中，并复制了那个公共 ECR，它是图片中的绿色标记

![](img/cf0133dc04c189a1be001d7fa503add4.png)

并将链接粘贴到图像字段中。

![](img/7ac618b7d869e41d93b76d6fe85d75e4.png)

设置所有作业配置后，然后点击创建按钮。

现在，我们的下一个任务是通过创建一个作业来运行该映像文件，即 AWS Batch 上的 Docker 容器。

现在点击导航栏左侧的 **Jobs** 。现在点击**提交新工作**

![](img/6835c9b46464f41f82b54b64288d58e9.png)

点击提交新工作后，我们将被重定向到表单页面，我们必须一步一步地填写一些详细信息。
在**名称**字段中，我们可以添加自己的作业名称，我们已经创建了作业定义和作业队列。

![](img/3aeafa3a41c53ade2690ea57e3a535c1.png)

如果我们选择**作业定义**，所有字段将由 **AWS** 自动填充。

同样对于**作业配置**也是自动填充的，我们只需要验证即可。

![](img/c2e2aa98eeb5fed3faf946977e46b0b6.png)

之后，点击提交。然后转到**仪表板**，我们可以看到我们的任务已提交。

![](img/39f9b5681388d07cd869bd7328224d77.png)

我们可以在上面的图像中看到不同的列，它们显示了我们的 AWS 批处理作业的状态，或者我们可以说批处理作业的不同阶段。

```
Stages ranges from Submitted -> Runnable -> Starting ->Running -> Succeeded/Failed.
```

所有阶段都是自动完成的，我们可以在 Succeeded/ Failed 列中观察输出。

在我的场景中，我的作业被正确执行，我可以通过单击数字**“2”看到结果，**我以前运行过相同的作业，这就是为什么它显示 2，否则它将显示 1，如果您的作业失败，它将在失败列中显示 1。

![](img/fdd598258ca0a80516e0168bdd512da5.png)

在我的场景中的上图中，我将单击 2，它将被重定向到成功的作业列表。

我将点击**“我的工作-v1”。因为我们现在已经运行了该作业。**

![](img/7ab5ef08a625baaea31d822c0c6f6d0c.png)

并且它将移动到**工作信息**页面。我们看到**日志流名称，下面的**提供了链接，请点击它包含的输出。该链接将到达 **AWS Cloudwatch** 。

![](img/ca8652d5ee1951d5bfbb0bab7e03adc2.png)

什么是 AWS Cloudwatch？用我的话来说，我可以说它用于查看任何服务的输出，如 AWS Batch、AWS Lambda 等。

而你只要点击[**AWS cloud watch**](https://aws.amazon.com/cloudwatch/)**就可以获得更多细节。**

![](img/3c8587966dff647fc0ec8f4def971b53.png)

AWS 批处理的输出

我们可以看到上面的图像，即作业的输出。

我们已经成功运行了 AWS 批处理。