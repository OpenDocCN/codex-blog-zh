# 使用深度强化学习训练自主代理来玩网球游戏

> 原文：<https://medium.com/codex/implementation-of-multi-agent-deep-deterministic-policy-gradient-maddpg-algorithm-to-train-2-9503b8c111db?source=collection_archive---------12----------------------->

![](img/249832343ebda09d67c0b2df94901ae5.png)

**使用深度强化学习训练的自主代理人玩网球的游戏(图片来源:**[**https://www . desktop background . org/wallpaper/robot-Tennis-wallpapers-38116**](https://www.desktopbackground.org/wallpaper/robot-tennis-wallpapers-38116)**)**

## 实现多智能体深度确定性策略梯度(MADDPG)算法来训练两个智能体使用网球 Unity ML-智能体环境进行网球比赛

![](img/44050f0647cd154d9dd45d99f5622ea8.png)

# 问题陈述的简要介绍

使用 Tennis Unity ML-Agent 环境的修改版本，项目的目标是训练两个 Agent 进行一场网球比赛。在这个环境中，两个代理都控制球拍将球拍过球网。如果代理人将球击过网，则提供+0.1 的奖励，而如果代理人让球落地或将球击出界，则提供-0.01 的奖励(惩罚)。因此，每个代理的目标是保持球在比赛中。观察空间由对应于球和球拍的位置和速度的 8 个变量组成。每个代理接收它自己的本地观察。动作空间是连续的。有两个连续的动作，分别对应于向网前(或远离网前)移动和跳跃。任务是阶段性的，为了解决环境问题，两个代理人都必须获得+0.5 的平均分(连续 100 集，在两个代理人身上取最大值后)

1.  每集之后，每个代理收到的奖励的总和(无折扣)被用来获得每个代理的分数。这产生了两个(可能不同的)分数。
2.  每集只考虑这两个分数中的最大值。

当每集(超过 100 集)的最高分的平均值至少为+0.5 时，环境被视为已解决。

# 结果展示:

## 未经训练的特工随意移动球拍，无法击中网球

![](img/2716cd45fe8938b69d89c3ffb58db02d.png)

## 训练有素的代理人正确地打网球，控制球拍始终将网球打过网

![](img/44050f0647cd154d9dd45d99f5622ea8.png)

# 状态空间:

观察空间由对应于球和球拍的位置和速度的 8 个变量组成。每个代理接收它自己的本地观察。

# 行动空间:

动作空间是连续的。有两个连续的动作，分别对应于向网前(或远离网前)移动和跳跃。

# 解决方案标准:

任务是情节性的，为了解决环境问题，两个代理都必须获得+0.5 的平均分(超过连续 100 集，在两个代理上取最大值后)。

1.  每集之后，每个代理收到的奖励的总和(无折扣)被用来获得每个代理的分数。这产生了两个(可能不同的)分数。
2.  每集只考虑这两个分数中的最大值。

当每集(超过 100 集)的最高分的平均值至少为+0.5 时，环境被视为已解决。

# 相关概念

本节提供了一个理论背景，描述了目前在这一领域的工作，以及在这项工作中使用的概念和技术。

**a)强化学习:**随着 AlphaGo 等最近的突破和对雅达利 2600 游戏的掌握，强化学习变得非常流行。强化学习(RL)是一个学习策略的框架，该策略通过与环境的交互来最大化代理的长期回报。策略将情况(状态)映射到操作。在每次状态转换后，代理会收到一个即时的短期奖励。一个国家的长期回报是由一个价值函数决定的。状态的值大致对应于代理从该状态开始可以累积的总奖励。对应于在状态 s 中采取行动 a 之后的长期奖励的行动值函数通常被称为 Q 值，并且形成了被称为 Q 学习的最广泛使用的 RL 技术的基础

**b)时间差异学习:**时间差异学习是现代 RL 的中心思想，并且通过基于其他估计更新动作值函数的估计来工作。这确保代理不必等到完成一集后的实际累积奖励来更新其估计，而是能够从每个动作中学习。

**c)Q-学习:**Q-学习是一种非策略时间差分(TD)控制算法。非策略方法评估或改进不同于决策策略的策略。因此，这些决策可以由人类专家或随机策略做出，生成(状态、动作、奖励、新状态)条目以从中学习最优策略。

Q-learning 学习接近最佳动作值函数的函数 Q。它通过随机初始化 Q，然后使用从 Q 派生的策略(如 e-greedy)生成动作来实现这一点。e-greedy 策略选择具有最高 Q 值的动作或具有(低)概率的随机动作，随着 e(ε)增加而促进探索。利用这个新生成的(状态(St)，动作(At)，回报(Rt+1)，新状态(St+1))对，使用规则 1 更新 Q。

这个更新规则本质上规定了当前估计必须使用收到的即时奖励加上新状态的最大动作值的折扣估计来更新。这里需要注意的是，更新是在使用估计值执行行动后立即完成的，而不是等待真正的累积奖励，从而在行动中展示 TD。学习率α决定改变当前估计的程度，贴现率γ决定未来回报(估计的行动价值)与当前回报相比有多重要。

**d)经验重放:**经验重放是在固定大小的缓冲区中存储以前的经验(St，At，Rt+1，St+1)的机制。然后，对小批次进行随机采样，添加到当前时间步长的经验中，并用于增量训练神经网络。这种方法可以对抗灾难性遗忘，通过多次训练来更有效地利用数据，并表现出更好的收敛行为

**e)固定的 Q 目标:**在使用函数逼近器的 Q 学习算法中，TD 目标还取决于正在学习/更新的网络参数 w，这可能导致不稳定性。为了解决这个问题，使用了具有相同架构但不同权重的独立网络。并且这个单独的目标网络的权重每隔几步更新一次，以等于持续更新的本地网络。

**f)深度 Q 学习算法:**在现代 Q 学习中，使用神经网络来估计函数 Q，该神经网络将状态作为输入，并输出每个可能动作的预测 Q 值。它通常用 Q(S，A，θ)表示，其中θ表示网络的权重。用于控制的实际策略可以随后通过估计给定当前状态的每个动作的 Q 值并应用ε-贪婪策略从 Q 中导出。深度 Q 学习简单地意味着使用多层前馈神经网络或者甚至卷积神经网络(CNN)来处理原始像素输入

**g)基于价值的方法:**基于价值的方法，如 Q-学习和深度 Q-学习，旨在通过试图找到最优动作-价值函数的估计，从与环境的交互中学习最优策略。而对于具有小状态空间的环境，通过以 Q 表的形式表示最优动作值函数来实现 Q 学习，其中每个状态一行，每个动作一列，通过从对应于每个状态的行中拉出具有最大值的动作，每次一个状态地构建最优策略；不可能为具有巨大状态空间的环境维护 Q 表，在这种情况下，最佳动作值函数使用非线性函数逼近器来表示，例如形成深度 Q 学习算法基础的神经网络模型。

**h)基于策略的方法(对于离散的行动空间):**
与基于价值的方法不同，最佳策略可以从与环境的交互中直接找到，而不需要首先通过使用基于策略的方法找到最佳行动-价值函数的估计。为此，构建一个神经网络来逼近最佳策略，该策略将状态空间中的所有状态作为输入(输入神经元的数量等于状态的数量)，并返回在动作空间中选择每个动作的概率(输出神经元的数量等于动作空间中的动作数量)。代理使用该策略，通过仅将最近的状态作为输入传递给神经网络模型，来与环境进行交互。然后，代理从动作概率中取样，输出一个动作作为响应。该算法需要优化网络权重，因为在训练过程中，每次迭代最有可能选择最佳行动，这种逻辑有助于代理实现其目标，即最大化预期回报。

**注:**上述过程解释了通过对行动概率进行采样，使用神经网络来逼近随机策略的方法。基于策略的方法也可以用于近似确定性策略，方法是在训练期间的每次迭代的前向传递期间，只为馈送到模型的最新输入状态选择贪婪动作

**i)基于策略的方法(用于连续动作空间):**基于策略的方法可以用于具有连续动作空间的环境，通过使用用于估计最佳策略的神经网络，该神经网络具有输出层，该输出层通过输出正态分布的平均值和方差来参数化连续概率分布。然后，为了选择一个动作，代理只需要将最近的状态作为输入传递给网络，然后使用输出均值和方差从分布中进行采样。

基于策略的方法优于基于价值的方法，原因如下:a)基于策略的方法比基于价值的方法简单，因为它们去除了估计最优动作-价值函数的中间步骤，并且直接估计最优策略。b)基于策略的方法倾向于学习真正期望的随机策略，而基于值的方法倾向于学习确定性或接近确定性的策略。c)基于策略的方法最适合于估计具有连续动作空间的环境的最佳策略，因为它们直接将状态映射到动作，这不同于基于值的方法，基于值的方法需要首先估计每个状态的最佳动作，假设动作空间是离散的，具有有限数量的动作；但在连续作用空间的情况下，基于值的方法需要找到非平凡连续作用函数的全局最大值，这本身就是一个优化问题。

**j)策略梯度方法:**策略梯度方法是基于策略的方法的一个子类，其通过首先估计所有轨迹上的预期回报(累积回报)的梯度来估计最优策略的权重，该预期回报是神经网络模型的网络权重的函数，该神经网络模型表示要使用随机梯度上升来优化的策略 PI，该随机梯度上升通过分别查看轨迹中的每个状态-动作对并通过考虑累积回报(即该轨迹的预期回报)的大小来进行。或者更新网络权重以在接收到正奖励的情况下增加为特定状态选择动作的概率，或者更新网络权重以在接收到负奖励的情况下降低为特定状态选择动作的概率，这是通过计算梯度，即在给定状态下使用具有权重θ的策略 PI 选择动作的概率的对数的导数，并将其乘以在所有采样的一组轨迹上求和的轨迹中存在的所有状态-动作对上接收的奖励(预期回报)来实现的。策略的权重θ现在用经过几次迭代计算的梯度估计来更新，最终目标是收敛到最优策略的权重。

行动者-批评家方法:行动者-批评家方法是基于价值的方法(如 Deep-Q Network)和基于政策的方法(如 Reinforce)的交叉点。他们使用基于价值的方法来估计最优行动价值函数，然后将其作为基线来减少基于政策的方法的差异。演员-评论家代理比基于价值的代理更稳定，并且比基于政策的代理需要更少的样本/数据来学习。这里使用了两个神经网络，一个用于演员，一个用于评论家。批评神经网络接收状态，输出策略 PI 的状态值函数。行动者神经网络接受一个状态，并输出对于该状态最有可能采取的动作，然后通过使用当前状态和下一个状态的回报，使用该动作来计算当前状态的 TD 估计。Critic 神经网络使用该 TD 估计值进行训练。然后，评论家神经网络被用于计算优势函数(当前状态的奖励和下一状态的折扣 TD 估计与当前状态的 TD 估计的差的总和)，该优势函数然后被用作训练演员神经网络的基线。

**l)深度确定性策略梯度(DDPG)算法:** DDPG 算法可以最好地描述为用于连续动作空间的深度 Q 网络方法，而不是被称为实际的行动者-批评家方法。DDPG 算法中的 Critic 神经网络模型用于逼近下一状态的 Q 值的最大值，而不是作为学习基线来减少 Actor 神经网络模型的方差。深 Q-网络方法不能用于连续动作空间。在 DDPG 方法 2 中，神经网络用于演员和评论家，类似于基本的演员-评论家方法。行动者神经网络通过只输出给定状态的最佳动作而不是每个动作的概率分布来确定性地逼近最优策略。批评家神经网络使用行动者神经网络输出的最可信动作来估计给定状态的最佳动作值函数。因此，在这里，Actor 被用作近似最大化器来计算新的目标值，以训练批评家来估计该状态的行动值函数。DDPG 算法使用重放缓冲器来存储和获得经验元组的采样集，并使用软更新的概念来更新演员和评论家的目标网络，如下所述。

**演员和评论家网络权重的软更新:**在 DDPG 算法中，演员和评论家网络都有常规和目标网络权重。通过将小部分常规网络权重添加到当前目标网络权重，保持当前目标网络权重的大部分，在每个时间步长更新目标网络权重。使用这种软更新策略来更新目标网络权重对加速学习有很大帮助。

**m)多智能体强化学习(MARL) :** 当多智能体系统使用强化学习技术来训练智能体并使它们学习它们的行为时，这个过程被称为多智能体强化学习。马尔可夫游戏是一个实现 MARL 的框架，就像马尔可夫决策过程(MDP)被用于单代理强化学习技术一样。

**n)马尔可夫游戏:**马尔可夫游戏是一个实现多智能体强化学习(MARL)技术的框架，其实现方式与马尔可夫决策过程(MDP)用于单智能体强化学习技术的方式相同。马尔可夫博弈是一组下列参数:a)代理数量 b)环境状态集合 c)每个代理的动作集合 d)两个代理的组合动作空间 e)每个代理的观察集合 f)每个代理的奖励函数 g)每个代理的策略集合 h)状态转移函数提供所有代理的当前状态和联合动作

# 所用学习算法的描述

**多智能体深度确定性策略梯度(mad pgg)算法:**mad pgg 算法是针对多智能体的 DDPG 算法概念的扩展。使用 DDPG 算法单独训练每个代理，每个代理具有其自己的演员和评论家模型。DDPG 算法是一种非策略行动者-批评者算法，它使用目标网络的概念，使用行动者和批评者的本地网络的最新权重来软更新目标网络。在实现 MADDPG 时，每个代理的 actor 模型接收代理的个体状态(观察值)作为输入，并输出(二维)动作向量。然而，每个代理的批评家模型接收所有代理的所有行动者的状态和动作。这种方法导致代理之间的信息共享。在训练期间，每个代理的批评者接收额外的信息，例如所有在场的其他代理观察到的状态和采取的行动，而每个代理的行动者仅具有关于该特定代理观察到的状态和采取的行动的信息。在整个培训过程中，所有代理都使用共享的经验回放缓冲区，并抽取独立的样本。MADDPG 算法可用于在合作、竞争或混合合作竞争环境中训练多个智能体。综上所述，可以说 MADDPG 算法是一种集中训练，分散执行的算法。

# 使用的神经网络架构:

两个代理都为演员和评论家使用单独的神经网络模型，但是演员模型和评论家模型的架构是相同的。对于每个代理，为本地网络和目标网络创建 2 个演员模型和 2 个评论家模型。

# a)演员神经网络模型的架构(对于每个代理) :

```
A multilayer feed-forward Neural Net Architecture was used with 2 Hidden layers each having 256 hidden neurons. The input layer
has number of input neurons equal to the state size and the the output layer has number of output neurons equal to action size. A
Leaky ReLU (Rectified Linear Unit) Activation Function was used over the inputs of the 2 hidden layers while a tanh activation 
function was used over the input of the output layer. Batch Normalization was used over the output of the first hidden layer. Weight initialization was done for the first 2  layers  from uniform distribution in the negative to positive range of reciprocal of the  
square root of number of weights for each layer. Weight initialization for the final layer was done from uniform distribution in the range of (-3e-3, 3e-3).
```

# Critic 神经网络模型的架构(对于每个代理) :

```
A multilayer feed-forward Neural Net Architecture was used with 2 Hidden layers each having 256 hidden neurons. The input layer has 
number of input neurons equal to the product of sum of state size and action size for each agent and the number of agents i.e. the 
sum of observation state space and action space for all the agents whereas the the output layer has number of output neurons equal 
to 1\. A Leaky ReLU (Rectified Linear Unit) Activation Function was used over the inputs of the 2 hidden layers. Batch Normalization 
was used over the output of the first hidden layer. Weight initialization was done for the first 2 layers from uniform distribution in the negative to positive range of reciprocal of the square root of number of weights for each layer. Weight initialization for the final layer was done from uniform distribution in the range of (-3e-3, 3e-3).
```

# c)其他实施细节:

```
1) **Adam Optimizer** was used for learning the neural network parameters with a learning rate of 1e-4 for Actor Neural-Net Model and a learning rate of 1e-3 and L2 weight decay of 0.0 for Critic Neural-Net Model for each Agent.

2) For the exploration noise process an **Ornstein-Ulhenbeck Process** was used with mu=0.(mean), theta=0.15 and sigma=0.2(variance)
to enable exploration of the physical environment in the simulation by the 2 Agents controlling the Tennis Rackets movements. But before adding noise to the action returned for the current state using the current policy the noise quantity was multiplied by a hyperparameter "noise weight" whose value was gradually decreased by multiplying with the hyperparameter "noise decay rate" to prefer exploration over exploitation only during the initial stages of training and gradually prefer exploitation over exploration during later stages of training as the value of the noise weight gradually decreases as training proceeds.
```

# 使用的超参数:

1.  **集数:** 5000
2.  **最大时间步长:** 1000
3.  **N 个代理:** 2(不同代理的数量)
4.  **STATE_SIZE :** 24(单个代理的状态维数)
5.  **ACTION_SIZE :** 2(单个代理的动作维数)
6.  **CRITIC _ INPUT _ SIZE**:**(STATE _ SIZE+ACTION _ SIZE)* N _ AGENTS**
    (每个代理的 CRITIC 本地和目标网络接收关于所有代理的观察状态和动作的信息)
7.  **隐藏神经元:** 256(每个代理的演员和评论家本地和目标神经网络的隐藏层的隐藏神经元数量)
8.  **LR _ ACTOR:**1e-4(ACTOR 神经网络模型的学习率)
9.  **LR _ CRITIC:**1e-3(CRITIC 神经网络模型的学习率)
10.  **权重衰减:** 0.0(评论家神经网络模型使用的 L2 权重衰减)
11.  **BUFFER_SIZE :** 10000(重放缓冲区大小)
12.  **批量大小:** 256(小批量)
13.  **伽玛:** 0.99(贴现因子)
14.  **TAU :** 1e-3(用于目标参数的软更新)
15.  **UPDATE_EVERY :** 2(多久更新一次网络)
16.  **噪声 _ 开始:** 0.5(初始勘探噪声加权系数)
17.  **噪声 _ 衰减:** 1.0(勘探噪声衰减率)
18.  **T_STOP_NOISE :** 30000(训练中应用探测噪声的最大时间步数)
19.  **NOISE_ON :** True(如果时间步数超过 T_STOP_NOISE 值，则停止添加探测噪声的布尔标志)
20.  **目标得分:**平均分大于等于+0.5(连续 100 集以上，取每集两个代理得分的最大值后)

# 每集奖励图

**多代理深度确定性策略梯度算法结果:**

![](img/d3a28ee630e0d5b01de60a06461b5c97.png)

2877 集取得+0.5 的成绩

# 结论

由于给定问题陈述的多代理性质，在两个代理的训练期间见证了许多波动/不稳定性。学习曲线波动反映在训练期间每集计算的平均分数中。最终花了 2877 集才最终解决了这个实现的环境。需要进行进一步的探索和实验来优化训练过程，以便通过调整演员-评论家模型架构和超参数值来实现更快和更稳定的学习曲线。对于多代理环境，还需要研究实现比 MADDPG 算法更好的算法。

# 设置项目的安装说明:

# 1)设置 Python 环境:

```
a) Download and install Anaconda 3 (latest version 5.3) from this link (https://www.anaconda.com/download/)for the specific Operating System and Architecture (64-bit or 32-bit) being used for Python 3.6 + version onwards

 b) Create (and activate) a new environment with Python 3.6.:
    Open Anaconda prompt and then execute the below given commands

    Linux or Mac:
    conda create --name drlnd python=3.6
    source activate drlnd

    Windows:
    conda create --name drlnd python=3.6 
    activate drlnd

 c) Minimal Installation of OpenAi Gym Environment
    Below are the instructions to do minimal install of gym :

    git clone https://github.com/openai/gym.git
    cd gym
    pip install -e .

    A minimal install of the packaged version can be done directly from PyPI: pip install gym

 d) Clone the repository (https://github.com/udacity/deep-reinforcement-learning.git) and navigate to the python/ folder.
    Then, install several dependencies by executing the below commands in Anaconda Prompt Shell :

git clone https://github.com/udacity/deep-reinforcement-learning.git
cd deep-reinforcement-learning/python
pip install . (or pip install [all] )

 e) Create an Ipython Kernel for the drlnd environment :
python -m ipykernel install --user --name drlnd --display-name "drlnd"

 f) Before running code in a notebook, change the kernel to match the drlnd environment by using the drop-down Kernel menu.
```

# 2)安装 Unity ML-代理相关的库/模块:

```
Clone the GitHub Repository (https://github.com/Unity-Technologies/ml-agents.git)and install the required libraries by running the below mentioned commands in the Anaconda Prompt

git clone https://github.com/Unity-Technologies/ml-agents.git
cd ml-agents/ml-agents (navigate inside ml-agents subfolder)
pip install . or (pip install [all]) (install the modules required)
```

# 3)下载 Unity 环境:

```
a) For this project, Unity is not necessary to be installed because readymade built environment has already been provided , and can be downloaded from one of the links below as per the operating system being used:

     Linux: https://s3-us-west-1.amazonaws.com/udacity-drlnd/P3/Tennis/Tennis_Linux.zip
     Mac OSX: https://s3-us-west-1.amazonaws.com/udacity-drlnd/P3/Tennis/Tennis.app.zip
     Windows (32-bit): https://s3-us-west-1.amazonaws.com/udacity-drlnd/P3/Tennis/Tennis_Windows_x86.zip
     Windows (64-bit): https://s3-us-west-1.amazonaws.com/udacity-drlnd/P3/Tennis/Tennis_Windows_x86_64.zip

Place the downloaded file in the p3_collab-compet/ as well as python/ folder in the DRLND GitHub repository, and unzip (or decompress) the file.

  c) (For AWS) If the agent is to be trained on AWS (and a virtual screen is not enabled), then please use this link (https://s3-us-west-1.amazonaws.com/udacity-drlnd/P3/Tennis/Tennis_Linux_NoVis.zip) for Tennis Environment to obtain the "headless" version of the environment. Watching the agent during training is not possible without enabling a virtual screen.(To watch the agent,follow the instructions to enable a virtual screen (https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Training-on-Amazon-Web-Service.md)and then download the environment for the Linux operating  system above.)
```

# 运行代码以训练代理/测试已训练代理的详细信息:

1.  首先在本地系统上克隆这个存储库([https://github . com/PranayKr/Deep _ Reinforcement _ Learning _ projects . git](https://github.com/PranayKr/Deep_Reinforcement_Learning_Projects.git))。
2.  还要在本地系统上克隆前面提到的存储库(【https://github.com/udacity/deep-reinforcement-learning.git】T2)。
3.  现在，将这个克隆的 GitHub Repo 中存在的所有源代码文件和预训练模型权重放入 Deep-RL 克隆存储库文件夹的 python/文件夹中。
4.  接下来，将包含已下载的用于 Windows (64 位)操作系统的 unity 环境文件的文件夹放入 Deep-RL 克隆存储库文件夹的 python/文件夹中。
5.  打开 Anaconda 提示符 shell 窗口，并在 Deep-RL 克隆存储库文件夹中的 python/文件夹内导航。
6.  在 Anaconda 提示符 shell 窗口中运行命令“jupyter notebook ”,在浏览器中打开 jupyter notebook web-app 工具，从该工具中可以找到笔记本中提供的任何培训和测试源代码。ipynb 文件)可以打开。
7.  在笔记本中运行/执行代码之前，使用下拉内核菜单更改内核(为 drlnd 环境创建的 IPython 内核)以匹配 drlnd 环境。
8.  提供的培训和测试笔记本中的源代码(。ipynb 文件)也可以在各自的新 python 文件(.py 文件)，然后使用命令“python <filename.py>”从 Anaconda 提示符 shell 窗口直接执行。</filename.py>

**注:**

1.  通过选择内核选项卡中的选项(重新启动并全部运行),可以同时执行所有单元。
2.  请更改(*的名称。pth)文件，模型权重在训练期间保存在该文件中，以避免用相同的文件名覆盖现有的预训练模型权重。

**多代理深度确定性策略梯度(MADDPG)算法训练/测试细节(使用的文件):**

```
**For Training :** Open the below mentioned Jupyter Notebook and execute all the cells : **Tennis-Solution-Working.ipynb** 

**Neural Net Model Architecture file Used : MADDPG_Model.py
The Unity Agent file used : MADDPG_Agent.py**

**For Testing :** open the Jupyter Notebook file 
**"Tennis-MADDPGAgent_Test.ipynb"** and run the code to test the 
results obtained using Pre-trained model weights for Actor Neural-Net Model and Critic Neural-Net Model for each of the 2 Pre-trained Agents

**Pretrained Model Weights provided : 
1) checkpoint_actor_agent_0.pth  
2) checkpoint_actor_agent_1.pth
3) checkpoint_critic_agent_0.pth
4) checkpoint_critic_agent_1.pth**
```

# 参考资料:

1.  混合合作竞争环境下的多主体演员-评论家**(**[**)https://papers . nips . cc/paper/7217-MULTI-AGENT-ACTOR-CRITIC-FOR-MIXED-COOPERATIVE-COMPETITIVE-ENVIRONMENTS . pdf**](https://papers.nips.cc/paper/7217-multi-agent-actor-critic-for-mixed-cooperative-competitive-environments.pdf)**)**
2.  连续控制与深度强化学习**(**[**)https://arxiv.org/pdf/1509.02971.pdf**](https://arxiv.org/pdf/1509.02971.pdf)**)**

## 注意:

1.  这篇文章也发表在 LinkedIn([https://www . LinkedIn . com/pulse/autonomous-agents-trained-using-deep-reinforcement-learning-Kumar](https://www.linkedin.com/pulse/autonomous-agents-trained-using-deep-reinforcement-learning-kumar))
2.  如果你想联系我，请通过 LinkedIn**【https://www.linkedin.com/in/pranay-kumar-02b35524/】**给我发信息，或者发邮件到 pranay.scorpio9@gmail.com****