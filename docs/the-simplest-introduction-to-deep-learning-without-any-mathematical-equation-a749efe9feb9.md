# 没有任何数学方程式的深度学习的最简单介绍

> 原文：<https://medium.com/codex/the-simplest-introduction-to-deep-learning-without-any-mathematical-equation-a749efe9feb9?source=collection_archive---------17----------------------->

## 深度学习是一种机器学习技术，它允许 AI 在没有显式程序的情况下进行学习，显式程序只是一个数学方程，以人工神经网络的形式表示，受人脑中神经网络的启发。

![](img/b9416c5d8cf9662ed9287ad7745dd643.png)

[亚历山大·奈特](https://unsplash.com/@agk42?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/deep-learning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 一些定义

AI(人工智能)是一种计算机程序，可以执行通常需要人类智能才能完成的任务。ML(机器学习)是人工智能的一个子集，它允许人工智能在没有明确编程的情况下自己学习。DL(深度学习)是 ML 的一个子集，ML 仍然是允许 AI 在没有显式编程的情况下自己学习的能力。然而，与 ML 不同，DL 使用 ANN(人工神经网络)作为学习过程的基础设施。

# 是什么启发了深度学习？

DL 试图模仿人类大脑中真正的神经网络的机制，这是这个星球上最强大的学习工具。

![](img/fc8fdd41c82b970049235c1c85081c46.png)

来自 [pixabay](https://pixabay.com/illustrations/abstract-neurons-connection-link-1588720/) 的人脑神经网络

人脑中的神经网络是由许多神经元互连而成的，其中每个神经元接收来自前一个神经元的信号作为输入，对信号进行一些修改，并将信号作为输出传递给另一个神经元，以便对某事做出一些决定。

![](img/d6ada5732bc1ddc58bc80c5776300a8f.png)

从 [pixabay](https://pixabay.com/vectors/neuron-nerve-cell-axon-dendrite-296581/) 中绘制人脑中的神经元

当神经元在将它们的输入信号传递给下一个神经元之前调整它们如何修改它们的输入信号时，学习过程发生，下一个神经元改变这个神经元对另一个神经元的影响，这影响人类做出的最终决定。

# 深度学习如何模仿这样的机制？

为了让 DL 模仿这种机制，发明了一种(人工神经元),其行为类似于人脑中的神经元。

![](img/75f676e1f7f008c78bf5580588c72536.png)

来自 [Google](https://www.google.com/search?q=artificial+neuron+picture&source=lnms&tbm=isch&sa=X&ved=2ahUKEwitie_U6N_yAhW3zjgGHb8zAusQ_AUoAXoECAEQAw&biw=1707&bih=837&dpr=1.13#imgrc=RmEX8nl_LvoHFM) 的神经元与人工神经元的比较

可是，这个人工神经元充当数学函数，以矩阵形式接收来自前一个人工神经元的输入信号，对输入进行一些数学修改，并且也以矩阵形式把信号输出到下一个人工神经元。

![](img/03e93374f980eae00096ec6c34bec87c.png)

人工神经网络来自[谷歌](https://www.google.com/search?q=artificial+neural+network+picture&tbm=isch&ved=2ahUKEwivltfX6N_yAhXTnEsFHaguD3UQ2-cCegQIABAA&oq=artificial+neural+network+picture&gs_lcp=CgNpbWcQAzIFCAAQgAQyBggAEAgQHjoGCAAQBxAeOggIABAIEAcQHlC-9ANY8okEYIWLBGgAcAB4AIABfYgB7AqSAQM5LjaYAQCgAQGqAQtnd3Mtd2l6LWltZ8ABAQ&sclient=img&ei=DYQwYe_bGdO5rtoPqN28qAc&bih=837&biw=1707#imgrc=tNqH5K3OnIcDdM)

许多人工神经元的互连构成人工神经网络，其中每一个互连包含一个加权，该加权确定从前一个人工神经元输出的信号对下一个人工神经元的影响或重要性。通过人工神经网络传递信号以产生关于某事、预测或分类的一些决定的过程被称为前向传播。深度学习这个名称来自于许多人工神经元形成人工神经网络的复杂互连的深度。ANN 越复杂，DL 可以估计的复杂函数就越多。

# 人工神经网络学习过程的直观性

人工神经网络的学习过程主要有两个过程。首先是调整两个人工神经元之间连接的权重，这是为了调整从一个人工神经元输出的信号对另一个人工神经元的重要性。另一种是偏置的调整，通过做一些简单的矩阵加法或减法来转换接收到的输入，并将其作为输出传递给下一个人工神经元。学习过程使用一些优化技术，通过改变人工神经网络中的权重值和偏差来最小化或最大化一些目标函数。根据观察到的目标函数调整权重和偏差的过程称为反向传播。

# 摘要

深度学习是一种机器学习技术，它允许 AI 在没有显式编程的情况下进行学习，显式编程只是许多数学方程的复杂集合，这些方程接收一些输入并以矩阵的形式产生一些输出，该矩阵以人工神经网络的形式表示，该人工神经网络受人脑中的神经网络的启发。