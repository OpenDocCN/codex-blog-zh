# Pytorch 的综合教程

> 原文：<https://medium.com/codex/a-comprehensive-tutorial-to-pytorch-distributeddataparallel-1f4b42bb1b51?source=collection_archive---------0----------------------->

![](img/8d1ab0911e8c5da6cbe26be3b510a8ff.png)

照片由 [XPS](https://unsplash.com/@xps?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

学校有限的计算资源阻碍了跨多个 GPU 的分布式训练。我第一次开始学是在我加入微软做实习生的时候。用 DDP(distributed data parallel 的缩写)包装模型基本上是一件容易的工作。令我沮丧的是，我无法正确调整我的多 gpu 工作流程，包括`DataLoader`、`Sampler`、训练和评估。网上的教程和博客几乎不包括所有这些东西。在解决了我遇到的这么多 bug 之后，我提出了目前为止的最佳实践。

在这个博客中，我想与所有 DDP 初学者分享我的代码和见解。我希望这个博客能帮助他们避免可怕的错误。我不打算详细解释 DDP 是如何工作的，相反，我提供了让模型在多个 GPU 中运行所需的最基本的知识。注意**我只在一台有多个 GPU 的机器上介绍 DDP，这是最一般的情况(**不然就用 [*官博*](https://pytorch.org/tutorials/intermediate/model_parallel_tutorial.html) **里说的模型并行)。**本博客组织如下:

*   [DDP 概述](#e774)
*   [实施 DDP 工作流程(步骤 1-6)](#854f)
*   [关于 dist.barrier()的问题](#63e5)

顺便说一句，我正在使用`torch==1.7.1`，但我认为它在`torch>=1.7.1`中会工作得很好。

# DDP 概述

首先，我们必须了解分布式培训中使用的几个术语:

*   ***主节点*** *:负责同步、制作副本、加载模型、写日志的主 gpu*
*   ***进程组:*** *如果你想在 K 个 GPU 上训练/测试模型，那么 K 个进程组成一个组，它由一个* ***后台支持(*** *pytorch 替你管理，根据*[](https://pytorch.org/docs/1.9.0/generated/torch.nn.parallel.DistributedDataParallel.html?highlight=distributeddataparallel#torch.nn.parallel.DistributedDataParallel)**，****nccl*******是*****
*   *****等级:*** *在流程组内，每个流程都是通过其等级来标识的，从 0 到 K-1；***
*   *****世界大小:*** *组内进程数即 gpu 数——K.***

**Pytorch 为分布式训练提供了两个设置:torch.nn.DataParallel (DP)和 torch . nn . parallel . distributeddataparallel(DDP)，其中后者是[官方推荐的](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html?highlight=distributed)。简而言之，DDP 比 DP 更快，更灵活。**DDP 做的基本事情是将模型复制到多个 GPU，从它们那里收集梯度，平均梯度以更新模型，然后在所有 K 个进程上同步模型。**我们还可以通过`torch.distributed.gather/scatter/reduce`聚集/分散除梯度以外的张量/物体。**

**如果模型可以放在一个 gpu 上(可以用`batch_size=1`在一个 gpu 上训练它),并且我们想要在 K 个 GPU 上训练/测试它，DDP 的最佳实践是将模型复制到 K 个 GPU 上(DDP 类会自动为您完成这项工作),并将数据加载器分成 K 个不重叠的组，分别提供给 K 个模型。**

**现在，事情对我们来说是清楚的。我们必须做以下事情:**

1.  ****设置流程组**，三行代码，无需修改；**
2.  ****将数据加载器拆分到组中的每个进程，**这可以通过 torch . utils . data . distributed sampler 或任何定制的采样器轻松实现；**
3.  ****用 DDP 包装我们的模型，**这是一行代码，几乎不需要修改；**
4.  ****训练/测试我们的模型**，和在 1 个 gpu 上一样；**
5.  ****清理进程组(像 C 中的 free)*、*、**这是一行代码。**
6.  ***可选:在进程间收集额外的数据(分布式测试可能需要)，基本上就是一行代码；***

**很简单，对吧？事实上的确如此。让我们一步一步来。**

# **1.设置流程组**

**在这里，没有额外的步骤。**

```
**import torch.distributed as distdef setup(rank, world_size): os.environ['MASTER_ADDR'] = 'localhost'
    os.environ['MASTER_PORT'] = '12355' dist.init_process_group("nccl", rank=rank, world_size=world_size)**
```

# **2.拆分数据加载器**

**我们可以很容易地通过 torch . utils . data . distributed . distributed sampler 来拆分我们的 dataloader**采样器返回一个遍历索引的迭代器，这些索引被送入 data loader 进行 bachify。****

**分布式采样器将数据集的总索引拆分成 *world_size* 个部分，并在每个进程中均匀地分配给数据加载器，没有重复。**

```
**from torch.utils.data.distributed import DistributedSamplerdef prepare(rank, world_size, batch_size=32, pin_memory=False, num_workers=0):
    dataset = Your_Dataset()
    sampler = DistributedSampler(dataset, num_replicas=world_size, rank=rank, shuffle=False, drop_last=False)

    dataloader = DataLoader(dataset, batch_size=batch_size, pin_memory=pin_memory, num_workers=num_workers, drop_last=False, shuffle=False, sampler=sampler)

    return dataloader**
```

**假设 K=3，数据集长度为 10。我们必须理解**分布式采样器对指数进行均匀划分。****

*   **如果我们在定义分布式采样器时设置了`drop_last=False`，它将自动填充**。**例如，当*排名* =1 时，它将索引[0，1，2，3，4，5，6，7，8，9]拆分为[0，3，6，9]；*排名* =2 时，它将索引[0，4，7，0]拆分为 T17 排名 =3 时，它将索引[2，5，8，0]拆分为[2，5，8，0]。如您所见，这种填充可能会导致问题，因为填充的 0 **是数据记录**。**
*   **否则，**会剥离拖尾元件**。例如，它在*等级* =1 处将索引拆分为[0，3，6]，在*等级* =2 处将索引拆分为[1，4，7]，在*等级* =3 处将索引拆分为[2，5，8]。在这种情况下，它修改了 9，使索引数可以被 world_size 整除。**

**定制我们的采样器非常简单。我们只需要创建一个类，然后定义它的`__iter__()`和`__len__()`函数。更多细节请参考[官方文件](https://pytorch.org/docs/stable/data.html?highlight=distributedsampler#torch.utils.data.distributed.DistributedSampler)。**

**顺便说一下，**分布式训练时最好设置** `**num_workers=0**` **，因为在子进程中创建额外的线程可能会有问题。我还发现** `**pin_memory=False**` **避免了许多可怕的错误，也许这种事情是机器特有的，如果你的读者探索了更多的细节，请给我发电子邮件。****

# **3.用 DDP 包裹模型**

**我们应该首先将我们的模型移动到特定的 gpu(回想一下，一个模型副本驻留在一个 gpu 中)，然后我们用 DDP 类包装它。下面的函数接受一个参数 *rank* ，我们很快会介绍它。现在，我们只需记住*等级*等于 gpu id *。***

```
**from torch.nn.parallel import DistributedDataParallel as DDPdef main(rank, world_size):
    # setup the process groups
    setup(rank, world_size) # prepare the dataloader
    dataloader = prepare(rank, world_size)

    # instantiate the model(it's your own model) and move it to the right device
    model = Model().to(rank)

    # wrap the model with DDP
    # device_ids tell DDP where is your model
    # output_device tells DDP where to output, in our case, it is rank
    # find_unused_parameters=True instructs DDP to find unused output of the forward() function of any module in the model model = DDP(model, device_ids=[rank], output_device=rank, find_unused_parameters=True)**
```

**这里有一些棘手的事情:**

*   **当我们想访问 DDP 包装模型的一些**定制的**属性时，我们必须引用`model.module`。也就是说，我们的模型实例被保存为 DDP 模型的一个`module`属性。如果我们分配一些属性`xxx`而不是内置属性或函数，我们必须通过`model.module.xxx`来访问它们。**
*   **当我们保存 DDP 模型时，我们的`state_dict`会给所有参数添加一个*模块前缀。***
*   **因此，如果我们想要将一个 DDP 保存的模型加载到一个非 DDP 模型中，我们必须手工去掉额外的前缀。我在下面提供我的代码:**

```
**# in case we load a DDP model checkpoint to a non-DDP modelmodel_dict = OrderedDict()
pattern = re.compile('module.')for k,v in state_dict.items():
    if re.search("module", k):
        model_dict[re.sub(pattern, '', k)] = v else:
        model_dict = state_dictmodel.load_state_dict(model_dict)**
```

# **4.训练/测试我们的模型**

**这部分是实现 DDP 的关键。首先我们需要知道多进程的基础:**所有子进程和父进程一起运行相同的代码**。**

**在 PyTorch 中，torch.multiprocessing 提供了创建并行进程的便捷方式。正如官方文件所说，**

> **下面的`spawn`函数解决了这些问题，负责错误传播、无序终止，并在检测到其中一个进程出错时主动终止进程。**

**所以，用`spawn`是个不错的选择。**

**在我们的脚本中，我们应该在生成并行流程之前定义一个训练/测试函数:**

```
**def main(rank, world_size):
    # setup the process groups
    setup(rank, world_size) # prepare the dataloader
    dataloader = prepare(rank, world_size)

    # instantiate the model(it's your own model) and move it to the right device
    model = Your_Model().to(rank)

    # wrap the model with DDP
    # device_ids tell DDP where is your model
    # output_device tells DDP where to output, in our case, it is rank
    # find_unused_parameters=True instructs DDP to find unused output of the forward() function of any module in the model model = DDP(model, device_ids=[rank], output_device=rank, find_unused_parameters=True) #################### The above is defined previously

    optimizer = Your_Optimizer()
    loss_fn = Your_Loss() for epoch in epochs:
        # if we are using DistributedSampler, we have to tell it which epoch this is
        dataloader.sampler.set_epoch(epoch)       

        for step, x in enumerate(dataloader):
            optimizer.zero_grad(set_to_none=True)

            pred = model(x)
            label = x['label']

            loss = loss_fn(pred, label)
            loss.backward()
            optimizer.step() cleanup()**
```

**这个`main`函数运行在每个并行进程中。我们现在需要通过`spawn`方法调用它。在我们的`.py`脚本中，我们写道:**

```
**import torch.multiprocessing as mp
if __name__ == '__main__':
    # suppose we have 3 gpus
    world_size = 3        mp.spawn(
        main,
        args=(world_size),
        nprocs=world_size
    )**
```

**还记得`main`的第一个论点是*排位吗？*由`mp.spawn`自动传递给各个流程，我们不需要显式传递。`**rank=0**`默认情况下**是主节点。等级的范围是从 0 到 K-1(在我们的例子中是 2)。****

# **5.清理进程组**

**`main`功能的最后一行是清理功能，它是:**

```
**def cleanup():
    dist.destroy_process_group()**
```

**太棒了。我们已经完成了分布式培训/测试的基本工作流程！**

# **6.可选:G *其他进程间的额外数据***

**有时我们需要从所有过程中收集一些数据，比如测试结果。我们可以很容易地通过`dist.all_gather`聚集张量，通过`dist.all_gather_object`聚集物体。**

**不失一般性，我假设我们想要收集 python 对象。对象的唯一约束是它必须是可序列化的，这基本上是 python 中的一切。在使用 `**all_gather_xxx**` **之前，应该先赋值** `**torch.cuda.set_device(rank)**` **。和**、**一样，如果我们要在物体中存储一个张量，它必须位于**、`**output_device**`、**。****

```
**def main(rank, world_size):
    torch.cuda.set_device(rank)
    data = {
        'tensor': torch.ones(3,device=rank) + rank,
        'list': [1,2,3] + rank,
        'dict': {'rank':rank}   
    }

    # we have to create enough room to store the collected objects
    outputs = [None for _ in range(world_size)]
    # the first argument is the collected lists, the second argument is the data unique in each process
    dist.all_gather_object(outputs, data) # we only want to operate on the collected objects at master node
    if rank == 0:
        print(outputs)**
```

# **关于 dist.barrier()的问题**

**最让我困惑的是**什么时候用** `**dist.barrier()**` **。**如文档所说，**它同步进程**。换句话说，**它阻塞进程，直到所有进程都到达同一行代码:** `**dist.barrier()**`。我把它的用法总结如下:**

1.  **训练时我们**不需要**，因为 DDP 自动为我们做了(在`loss.backward()`)；**
2.  **我们**在收集数据时不需要它，因为`dist.all_gather_object`为我们做了；****
3.  **当强制执行代码的执行顺序时，我们需要它，[比如一个进程加载另一个进程保存的模型](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html)(我很难想象这种场景是需要的)。**

**在这篇文章中，我们从头开始学习如何在我们的模型中实现 DDP。希望每个读到这篇文章的人都能从中受益。谢谢你。**