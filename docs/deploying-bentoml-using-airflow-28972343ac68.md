# 使用气流部署 BentoML

> 原文：<https://medium.com/codex/deploying-bentoml-using-airflow-28972343ac68?source=collection_archive---------4----------------------->

使用 BentoML 在一个简单的过程中完成培训、预测和服务

![](img/295c84418ee476dd5981acb55143ba91.png)

马蒂亚斯·斯派克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 动机

许多数据科学家知道如何训练和优化他们的模型。然而，将这些模型投入生产是另一回事。这些模型必须预处理数据、训练、部署，然后处理请求的规模。