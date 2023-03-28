# 深度学习设置:Ubuntu 上的 ECS GPU 任务(第 4 部分)

> 原文：<https://medium.com/codex/deep-learning-setup-ecs-gpu-task-on-ubuntu-part-4-46c364d1b556?source=collection_archive---------5----------------------->

![](img/2f133a22add46d0e8436f4270a347c9f.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

要在 ECS 中运行基于 GPU 的任务，我们需要创建自己的 EC2 实例，因为 Fargate】仍然不支持 GPU。有了 [ECS GPU 优化的 ami](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-gpu.html)，这应该不会太难。

然而，有时这是不可能的，因为团队已经在使用他们最喜欢的 AMI 设置，比如 Ubuntu [CIS 优化 AMI](https://aws.amazon.com/marketplace/seller-profile?id=dfa1e6a8-0b7b-4d35-a59c-ce272caee4fc) 或任何其他风格。这意味着他们需要从头开始安装和配置设置。

在这 4 篇文章中，我们将回顾在 Ubuntu 18.04 操作系统上使用 GPU 所需资源安装和配置 ECS 任务的过程。

第一部分:[英伟达驱动](https://michael-41345.medium.com/deep-learning-setup-ecs-gpu-task-on-ubuntu-part-1-87933804c050)

第 2 部分:[ECS 代理](https://michael-41345.medium.com/deep-learning-setup-ecs-gpu-task-on-ubuntu-part-2-1c7abd6d14ad)

第三部分:[NVIDIA-Docker 运行时间](https://michael-41345.medium.com/deep-learning-setup-ecs-gpu-task-on-ubuntu-part-3-a6ffbc6a3c5a)

**第 4 部分:ECS 代理上的 GPU 配置**

**为 ECS 代理添加 GPU 支持**

![](img/fdc1dd41a8bd437075e7936a92606d24.png)

这部分没有正式记录，配置是独立的[参考文献](https://blog.tedivm.com/rants/2020/07/aws-ecs-with-ubuntu-and-gpu-support/)、[代码](https://github.com/aws/amazon-ecs-agent/blob/master/agent/gpu/nvidia_gpu_manager_unix.go#L50)探索性的和试验性的&错误的集合。

编辑 ECS 配置:

```
sudo vi /etc/ecs/ecs.config
```

添加以下内容:

```
ECS_ENABLE_GPU_SUPPORT=true
ECS_NVIDIA_RUNTIME=nvidia
```

尽管目前没有正式的文档，ECS 代理需要一个用于 GPU 的[附加配置文件](https://github.com/aws/amazon-ecs-agent/blob/e9b600d21720063ed6e8dfee45bbbae5733db819/agent/gpu/nvidia_gpu_manager_unix.go#L52)。我们需要自己创造它:

```
sudo touch /var/lib/ecs/gpu/nvidia-gpu-info.json
```

内容是以下脚本的输出(从这个[源](https://blog.tedivm.com/rants/2020/07/aws-ecs-with-ubuntu-and-gpu-support/)借用并修改):

```
apt-get install -y jq

# Register GPUs with ECS
DRIVER_VERSION=$(modinfo nvidia --field version)
IFS="\n"
IDS=()
for x in `nvidia-smi -L`; do
  IDS+=$(echo "$x" | cut -f6 -d " " | cut -c 1-40)
done

echo "{\"DriverVersion\":\"$DRIVER_VERSION\",\"GPUIDs\":[\"$IDS\"]}" > /var/lib/ecs/gpu/nvidia-gpu-info.json
```

新文件的内容应该如下所示:

```
{“DriverVersion”:”460.27.04",”GPUIDs”:[“GPU-dfa3b669-e59a-da9e-fcb3-b005c2bbcb4e”]}
```

如果我们有一个动态环境，比如上下旋转实例的 ASG，我们可能会希望将配置部分添加到用户数据脚本中，因为当创建新的实例时，上面的 GPU ID 会发生变化。

向 ECS Run 命令添加 GPU 支持

现在我们要编辑 ECS 代理的 docker run 命令，以包含 NVIDIA 驱动程序。编辑服务:

```
sudo vi /etc/systemd/system/ecs-agent.service
```

并添加以下内容:

```
--volume=/var/lib/nvidia-docker/volumes/nvidia_driver/latest:/usr/local/nvidia \
--device /dev/nvidiactl:/dev/nvidiactl \
--device /dev/nvidia-uvm:/dev/nvidia-uvm \
```

整个 ECS 代理服务应该如下所示:

```
[Unit]
Description=AWS ECS Agent
Documentation=[https://docs.aws.amazon.com/AmazonECS/latest/developerguide/](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/)
Requires=docker.service
After=docker.service
[Service]
Restart=always
RestartPreventExitStatus=5
ExecStartPre=/bin/mkdir -p /var/lib/ecs/data
ExecStartPre=/bin/mkdir -p /var/log/ecs
ExecStartPre=-/usr/bin/docker kill ecs-agent
ExecStartPre=-/usr/bin/docker rm ecs-agent
ExecStart=/usr/bin/docker run \
    --name=ecs-agent \
    --restart=on-failure:10 \
    --volume=/var/run/docker.sock:/var/run/docker.sock \
    --volume=/var/log/ecs/:/log \
    --volume=/var/lib/ecs/data:/data \
    --volume=/etc/ecs:/etc/ecs \
    --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro \
    --net=host \
    --env-file=/etc/ecs/ecs.config \
    --volume=/var/lib/nvidia-docker/volumes/nvidia_driver/latest:/usr/local/nvidia \
    --device /dev/nvidiactl:/dev/nvidiactl \
    --device /dev/nvidia-uvm:/dev/nvidia-uvm \
    --volume=/var/lib/ecs/gpu:/var/lib/ecs/gpu \
    amazon/amazon-ecs-agent:latest
ExecStop=-/usr/bin/docker stop ecs-agent
[Install]
WantedBy=multi-user.target
```

最后，重新加载和重启:

```
sudo systemctl daemon-reloadsudo systemctl restart ecs-agent.service
```

现在，我们可以尝试再次运行 ECS 服务，这可以通过触发新部署来完成(ECS →服务→更新→“强制新部署”复选框→)...→更新服务)

**打扫卫生**

需要记住的一点是，如果为 ASG 或 AMI 创建此 ECS 代理配置，保持 ECS 状态干净非常重要:

*   不要在/var/lib/ecs/GPU/NVIDIA-GPU-info . JSON 中保留旧的 GPU ID，并在 ECS 代理启动后更改它
*   保持 ECS 状态和日志记录文件夹为空:

```
rm -f /var/lib/ecs/data/*rm -f /var/log/ecs/*
```

*   移除以前使用过的码头集装箱:

```
docker rm -v $(docker ps -a -q -f status=exited)
```

我们完了！到目前为止，我们的 ECS 任务应该已经成功运行并使用了 GPU 驱动程序。

请在这里发表评论或发送 PM 让我知道这是否对你有帮助，或者是否有任何错误或事情对你不起作用，因为我希望这份文档能被其他人使用，让他们的生活稍微轻松一点。