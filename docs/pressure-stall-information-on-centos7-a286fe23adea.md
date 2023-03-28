# CentOS7 上的压力失速信息

> 原文：<https://medium.com/codex/pressure-stall-information-on-centos7-a286fe23adea?source=collection_archive---------9----------------------->

![](img/e545097154b827c6b4220d4873c8d663.png)

照片由[阿诺德·弗朗西斯卡](https://unsplash.com/@clark_fransa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我正在运行数据库，知道压力是在 CPU、RAM 还是 I/O 上至关重要，这并不容易从 CloudWatch 或操作系统日常监控中提供的指标中推断出来。最近的 Linux 内核为此提供了 PSI(压力失速信息),所以让我们启用它。

我有从
AWS-market place/CentOS Linux 7 映像供应的 EC2 实例，但 CentOS 运行速度不快，而且内核较旧:

```
[yugabyte]$ cat /etc/system-releaseCentOS Linux release 7.4.1708 (Core)[yugabyte]$ uname --kernel-release3.10.0-693.5.2.el7.x86_64
```

我需要一个更新的版本，我将从 [ELRepo](http://elrepo.org/tiki/HomePage) 安装

```
[yugabyte]$ sudo yum update -yNo packages marked for update[yugabyte]$ sudo yum install -y [https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm](https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm)
```

现在列出主线内核(“ml”是长期“lt”的反义词):

```
[yugabyte]$ yum list available --disablerepo='*' --enablerepo=elrepo-kernel kernel-ml.x86_64Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * elrepo-kernel: mirrors.coreix.net
Available Packages
kernel-ml.x86_64                                                5.13.12-1.el7.elrepo                                                 elrepo-kernel
```

并安装它:

```
[yugabyte]$ sudo yum --enablerepo=elrepo-kernel install -y kernel-ml
```

这是 grub 的第一个菜单项:

```
[centos]$ sudo grep ^menuentry /boot/grub2/grub.cfgmenuentry 'CentOS Linux (5.13.12-1.el7.elrepo.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.5.2.el7.x86_64-advanced-6f15c206-f516-4ee8-a4b7-89ad880647db' {
menuentry 'CentOS Linux (3.10.0-1160.36.2.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.5.2.el7.x86_64-advanced-6f15c206-f516-4ee8-a4b7-89ad880647db' {
menuentry 'CentOS Linux (3.10.0-693.5.2.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.5.2.el7.x86_64-advanced-6f15c206-f516-4ee8-a4b7-89ad880647db' {
menuentry 'CentOS Linux (0-rescue-f073c429a7456b53ec3e2c53460c5c8f) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-f073c429a7456b53ec3e2c53460c5c8f-advanced-6f15c206-f516-4ee8-a4b7-89ad880647db' {
```

我将它设为默认设置(menuentry 0):

```
[yugabyte]$ sudo sed -e '/^GRUB_DEFAULT=saved/s/=.*/=0/' -i /etc/default/grub
```

我需要在内核命令行中添加`psi`:

```
[yugabyte]$ sudo mkdir -p /etc/tuned/psi && sudo tee /etc/tuned/psi/tuned.conf <<'TAC'[main]
  summary=Enable Pressure Stall Information as in https://dev.to/aws-heroes/pressure-stall-information-on-ec2-centos-7-2nbb-temp-slug-5559720
[bootloader]
cmdline=psi=1TAC
```

正在检查当前配置文件:

```
[yugabyte]$ tuned-adm profile
Current active profile: virtual-guest
```

添加新的:

```
[yugabyte]$ sudo tuned-adm profile virtual-guest psi
```

启用所有这些 GRUB 更改:

```
[yugabyte]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

现在准备重新启动节点。这就是我欣赏运行分布式数据库的地方(YugabyteDB🚀)因为我可以在不中断应用程序的情况下滚动重启节点。

正在检查:

```
[yugabyte]$ tail /proc/pressure/*==> /proc/pressure/cpu <==
some avg10=27.80 avg60=25.88 avg300=16.13 total=77572758
full avg10=0.98 avg60=0.94 avg300=0.55 total=4422080==> /proc/pressure/io <==
some avg10=12.03 avg60=13.02 avg300=7.36 total=32530366
full avg10=4.73 avg60=5.33 avg300=3.08 total=15034660==> /proc/pressure/memory <==
some avg10=0.12 avg60=0.02 avg300=0.00 total=309168
full avg10=0.12 avg60=0.02 avg300=0.00 total=307455
```

现在还有待解释。我在过去的博客文章中解释了一点，在关于 www.kernel.org 的[和](https://www.kernel.org/doc/html/latest/accounting/psi.html)中有完整的描述。基本上,“some”行显示了在过去的 10 秒、1 分钟和 5 分钟内，一个任务被停止的时间百分比，以及当所有非空闲任务都在等待时的“full”。所以如果你觉得某个东西很慢，应该更快，不要盲目伸缩。您知道哪个资源负责响应时间。