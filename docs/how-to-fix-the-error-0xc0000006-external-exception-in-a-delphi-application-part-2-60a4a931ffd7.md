# 如何修复 Delphi 应用程序中的错误 0xc0000006 外部异常—第 2 部分

> 原文：<https://medium.com/codex/how-to-fix-the-error-0xc0000006-external-exception-in-a-delphi-application-part-2-60a4a931ffd7?source=collection_archive---------17----------------------->

## Delphi 的 PE(可移植可执行文件)头标志

在这篇[文章](https://segovoni.medium.com/how-to-fix-the-error-0xc0000006-in-a-delphi-application-ff596d201c09)中，我谈到了与代码 C0000006 (0xc0000006)相关的外部异常错误。这是一个一般性错误，它隐藏了与操作系统试图从网络加载的资源(文件)相关的真正的“低级错误”。

该错误通常由在 Windows 终端服务器环境中使用 Delphi 应用程序的客户报告。

这个解决方案解决了我场景中的问题:在**中添加 PE flag[IMAGE _ FILE _ NET _ RUN _ FROM _ SWAP](https://docwiki.embarcadero.com/RADStudio/Sydney/en/PE_(portable_executable)_header_flags_(Delphi))所有**应用涉及的 Delphi 包和项目(dpk，dpr，…)中。

在这篇文章中，我想把你的注意力集中在两个细节上:第一个是关于将带有 PEflags 的 Delphi 项目拖放到一个项目组中，第二个是关于应用程序使用的第三方模块(BPL)。

## 将 Delphi 项目拖放到项目组中

我们注意到一个问题，当一个包含 PEFlags 的 Delphi 包 dpk 通过拖放操作(将包的项目拖到项目组中)被添加到一个 Delphi 项目组中时，会出现这个问题。在这种情况下，可能会出现本地路径的 *Winapi。将 windows . pas*(C:\ Program files(x86)\ Embarcadero \…)添加到 DPROJ 文件中。如果保存了项目，该路径也会添加到 DPK 文件中。为了避免这种行为，有必要以不同于我在本文第一部分中解释的方式激活 PEflag。标记是使用值$0800，而不是常量名称 IMAGE_FILE_NET_RUN_FROM_SWAP，如下面这段代码所示:

```
{$SetPEFlags $0800}
```

我知道 PEFlags 指令的这种用法不如常量 IMAGE_FILE_NET_RUN_FROM_SWAP 的用法清晰，但对于 Delphi IDE 来说更清晰，而且这种方式不需要添加 *Winapi。将 Windows.pas* 传递到项目的 contains 部分。

要清理已经发生这种行为的项目，必须打开 DPROJ 和 DPK 项目文件，并删除以下行:

```
<DCCReference Include = "C:\Program files (x86)\Embarcadero\Rad Studio\10.0\source\rtl\win\Winapi.Windows.pas"/>
```

## 第三方模块(BPL)

在所有应用程序模块上都设置了编译指令的客户那里，错误不断出现。案例研究使我们怀疑错误是由于没有 IMAGE_FILE_NET_RUN_FROM_SWAP 标志的外部模块(不是我们编译的)造成的，所以我们考虑使用 [EDITBIN](https://docs.microsoft.com/en-us/cpp/build/reference/editbin-options) 工具来编辑这些外部模块。这个工具允许你编辑文件，如 BPL，EXE 等。添加各种选项，包括 [/SWAPRUN](https://docs.microsoft.com/en-us/cpp/build/reference/swaprun) : NET(对应于 IMAGE_FILE_NET_RUN_FROM_SWAP 指令)而无需重新编译项目。我们也认为最好不要只在外部 BPL、EXE 等上运行 [EDITBIN](https://docs.microsoft.com/en-us/cpp/build/reference/editbin-options) 。但也在德尔福的(rtl，vcl 等)。).

现在我们已经在两个客户那里安装了用 EDITBIN 修改的模块。外部异常似乎被永久解决了！