# Go 中的换行命令

> 原文：<https://medium.com/codex/wrapping-commands-in-go-49a3c372b01a?source=collection_archive---------26----------------------->

![](img/f03edf66fb3b61a1f678492ce03517b0.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [AltumCode](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral) 拍摄的照片

你可以找到很多描述围棋的文章。包括这个博客上的内容。今天，我决定准备一些不同的东西。我将告诉你我的一个任务，并向你展示我是如何用 Go 解决它的。我认为展示一下`exec`包，讲述一下`ssh`命令，更好地学习 AWS EE2 是很有用的。

AWS 有一个功能叫做[亚马逊 EC2 实例连接](https://aws.amazon.com/blogs/compute/new-using-amazon-ec2-instance-connect-for-ssh-access-to-your-ec2-instances/)。您可以使用它通过 SSH 客户机连接到 EC2 实例。整个过程有几个步骤:

*   获取关于实例的信息(区域、实例 id、可用性区域)
*   将您的公钥上传到 EC2 实例
*   使用私钥和 SSH 命令连接到实例

我们今天要解决的问题是自动化这个过程。上传 SSH 密钥后，我们有 60 秒的时间来连接 EC2 实例。如果您连接到许多 EC2 实例，并且您必须一遍又一遍地重复相同的步骤，那么您希望实现自动化。

我的目标是创建一个`ssh`替代程序，它接受相同的参数，表现得像一个常规的`ssh`命令，但是自动完成整个设置过程。

要求很简单——该命令的用法应该尽可能类似于`ssh`命令。在一个完美的世界里——应该是 100%的替代。让我们试试看我们是否能做到这一点。命令示例如下所示:

```
./ec2-ssh HOSTNAME

# or
./ec2-ssh ec2-user@HOSTNAME # the default username is your OS user

# or
./ec2-ssh 192.168.0.1 -4 -k # accepts any parameter that ssh does
```

为了上传我们的公钥，我们需要知道可用性区域、EC2 实例 ID、用户和公钥本身。从命令参数中，我们得到了 IP 或主机名。

让我们从用户名、主机和公钥开始。在`ssh`命令中有一个`-G`参数，用于在评估主机和匹配模块后打印所有配置。我们可以用用户提供的所有参数调用`ssh`命令，并添加`-G`。然后，我们可以解析输出并从中读取所有数据。换句话说，我们想要读取这个命令的输出。

```
./ec2-ssh 192.168.0.1 -4 -k # from this command

ssh -G 192.168.0.1 -4 -k # we'll translate to this
```

我们必须调用`ssh`命令作为子进程，并读取其输出。Go 有一个包含`Cmd`结构的`exec`包。该结构表示一个外部命令，可用于此目的。

```
cmd := exec.CommandContext(ctx, "ssh", args...)

s := ""
buff := bytes.NewBufferString(s)
cmd.Stdout = buff
cmd.Stderr = os.Stdout
cmd.Stdin = os.Stdin

if err := cmd.Run(); err != nil {
	return nil, err
}
```

这个`Cmd`结构有对我们来说最重要的`cmd.Stdout`字段。这是我们可以转发命令输出的地方。该字段接受任何`io.Writer`类型，因此我们将缓冲区放在那里。下一步是将参数放入映射中，我们将从映射中检索值。

```
res := map[string][]string{}

scanner := bufio.NewScanner(buff)
for scanner.Scan() {
	parts := strings.Split(scanner.Text(), " ")
	if len(parts) < 1 {
		continue
	} if _, exists := res[parts[0]]; !exists {
		res[parts[0]] = []string{}
		continue
	}

	res[parts[0]] = append(res[parts[0]], strings.Join(parts[1:], " "))
}

return res, nil
```

我们一行一行地把数据放到地图上。在下一个函数中，我们从地图中获得所需的信息:我们需要它的 IPv4 和用户名。

```
func instanceInfoFromString(hostname, user string) (*instanceInfo, error) {
	info := &instanceInfo{
		username: user,
		host:     hostname,
	}

	err := info.resolveIP()
	if err != nil {
		return nil, err
	}
	return info, nil
}
```

我们需要 IP 地址，因为在后面的步骤中。我们需要它来过滤掉不相关的 EC2 实例。下一步是找到公钥，`ssh`将使用它将我们连接到 EC2 实例。在我们的地图中的`identityfile`键下有一个可能的 SSH 键列表。我们迭代每一项并检查它是否存在。如果是，那么我们返回它。

```
func existingKey(paths []string) (string, error) {
	for _, path := range paths {
		path, err := expandHomeDirectoryTilde(path)
		if err != nil {
			return "", err
		}

		if _, err := os.Stat(path); errors.Is(err, os.ErrNotExist) {
			continue
		}

		return path, nil
	}

	return "", errors.New("cannot find any ssh key")
}
```

每个键的路径都以一个波浪符号(`~`)开始，这意味着用户的主目录。我们必须编写一个函数，将波浪号扩展为完整路径。为什么？波浪号根据您的 shell 的`HOME`值展开。你可以在 [bash 的文档](https://www.gnu.org/software/bash/manual/html_node/Tilde-Expansion.html)或者这个[中阅读它是如何工作的，所以回答](https://unix.stackexchange.com/questions/146671/does-always-equal-home/146697#146697)。让我们回到代码上。

```
publicKey, err := getPublicKey(pk)
if err != nil {
	return fmt.Errorf("cannot read the public key %s.pub. If you want to provide a custom key location, use the `-i` parameter", pk)
}
```

在下面的清单中，我们试图读取公钥。我们需要它上传到 EC2 实例。这个公钥将用于验证我们的身份。这意味着在我们尝试连接它之前，EC2 实例必须知道它。AWS 会把我们的公钥放到`~/.ssh/authorized_keys`文件中。我们只有 60 秒的时间来连接实例。关于 SSH 授权如何工作的更多细节，你可以[访问这个描述](https://www.ssh.com/academy/ssh/public-key-authentication)。

我们几乎拥有了连接 EC2 实例所需的一切。唯一缺少的是 AWS 区域。我们要求尽可能与 ass `ssh`命令兼容，我们不能只是在命令中增加另一个参数。相反，我们将遍历所有区域，并尝试连接到每个实例。我知道这不是最佳方式。如果你有任何我可以改进的想法，请在下面的评论区告诉我。

```
func setupEC2Instance(ctx context.Context, instance *instanceInfo, publicKey, region string) (bool, error) {
	cfg, err := config.LoadDefaultConfig(ctx, config.WithRegion(region))
	if err != nil {
		return false, fmt.Errorf("cannot get config for AWS: %w", err)
	}

	client := ec2.NewFromConfig(cfg)

	ec2Instance, err := findEC2Instance(ctx, client, instance)
	if err != nil {
		return false, err
	}

	if ec2Instance == nil {
		return false, nil
	}

	status, err := instanceStatus(ctx, client, *ec2Instance)
	if err != nil {
		return false, fmt.Errorf("cannot get the instance status: %w", err)
	}

	connect := ec2instanceconnect.NewFromConfig(cfg)
	out, err := connect.SendSSHPublicKey(ctx, &ec2instanceconnect.SendSSHPublicKeyInput{
		AvailabilityZone: status.AvailabilityZone,
		InstanceId:       ec2Instance.InstanceId,
		InstanceOSUser:   &instance.username,
		SSHPublicKey:     &publicKey,
	})

	if err != nil {
		return false, fmt.Errorf("cannot upload the public key: %w", err)
	}

	if !out.Success {
		return false, fmt.Errorf("unsuccessful uploaded the public key")
	}

	return true, nil
}
```

在上面的代码中，我们正在配置 AWS 客户机，试图在选定的区域中找到我们的 EC2 实例。如果一切顺利，我们将上传我们的公钥。如果也成功了，我们就准备好连接了。这里新增了两个函数:`findEC2Instance`和`instanceStatus`。

第一个非常明显——它使用我们之前检索的 IP 地址找到我们的 EC2 实例。

```
func findEC2Instance(ctx context.Context, client *ec2.Client, info *instanceInfo) (*types.Instance, error) {
	resp, err := client.DescribeInstances(ctx, &ec2.DescribeInstancesInput{
		Filters: []types.Filter{
			{
				Name:   strp("private-ip-address"),
				Values: []string{info.ipAddress},
			},
		},
	})

	if err != nil {
		return nil, fmt.Errorf("cannot contact with AWS API: %w", err)
	}

	for _, r := range resp.Reservations {
		for _, inst := range r.Instances {
			if *inst.PrivateIpAddress == info.ipAddress {
				return &inst, nil
			}
		}
	}
	return nil, nil
}
```

当我们知道实例存在并且有了它的引用时，我们可以检查它的状态，这就是`instanceStatus`发挥作用的时候。

```
func instanceStatus(ctx context.Context, client *ec2.Client, instance types.Instance) (types.InstanceStatus, error) {
	descResp, err := client.DescribeInstanceStatus(ctx, &ec2.DescribeInstanceStatusInput{
		InstanceIds: []string{*instance.InstanceId},
	})

	if err != nil {
		return types.InstanceStatus{}, err
	}

	status := descResp.InstanceStatuses[0]
	return status, nil
}
```

`client.DescribeInstanceStatus`为我们返回一些非常有价值的信息:实例的可用区域和实例的 ID。上传 SSH 公钥时，这两个值都是必需的。

此时，我们已经准备好连接到 EC2 实例了！这很简单——必须用我们所有的参数执行`ssh`命令。我们将所有输出转发到标准输出，并对 std 输入进行同样的操作。由于这个原因，我们将能够像往常一样与`ssh`命令交互。

```
func connectToInstance(ctx context.Context, params []string) error {
	cmd := exec.CommandContext(ctx, "ssh", params...)
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stdout
	cmd.Stdin = os.Stdin

	if err := cmd.Run(); err != nil {
		if exiterr, ok := err.(*exec.ExitError); ok {
			// terminated by Control-C so ignoring
			if exiterr.ExitCode() == 130 {
				return nil
			}
		}

		return fmt.Errorf("error while connecting to the instance: %w", err)
	}

	return nil
}
```

仅此而已！完整源代码[可在 Github](https://github.com/bkielbasa/ec2-ssh) 上获得。从现在开始，在使用 AWS EC2 实例时，您可以用`ec2-ssh`替换您的`ssh`命令。如果您有任何问题或建议，请随时使用下面的评论部分。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

*原载于*[*https://developer20.com*](https://developer20.com/wrapping-commands-with-go/)*。*