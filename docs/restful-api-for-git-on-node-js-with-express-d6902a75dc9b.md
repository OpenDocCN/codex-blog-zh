# 带有 Express 的 Node.js 上 Git 的 RESTful API

> 原文：<https://medium.com/codex/restful-api-for-git-on-node-js-with-express-d6902a75dc9b?source=collection_archive---------10----------------------->

![](img/861dbd01062cf24175c4ae8f7c1ae335.png)

使用 Node.js 在 git 存储库上实现 RESTful API 可以快速解决与获取 git 存储库信息相关的任务。下面是使用 Express 框架创建 API 的描述。

首先，您需要将 Express 添加到项目中，以及提供 git CLI 包装的 *nodegit* 库，创建 Postgre 数据库连接池的 *pg* 库(如果其中存储了链接或存储库名称)，用于 BASE64 编码的 *btoa* ，用于在服务器上执行 bash 命令的 child_process:

`npm install express nodegit pg btoa child_process — save`

安装后，我们将模块导入 server.js 文件，并描述服务器在端口 3031 上的启动(如果在环境变量中没有指定不同的端口):

这个例子将着眼于使用多个存储库的能力。将为其提供 API 的存储库列表可以通过各种可用的方式获得。下面是一个如何直接从数据库中获取存储库列表的示例:

YAMLconvert is 函数在这里执行 YAML 到 JSON 的转换。作为执行的结果，repos 数组将包含一个对象列表，该列表包含关于可用存储库和用于身份验证的数据的信息。通常，要使用远程存储库，必须将其克隆到节点。为此，让我们创建一个克隆函数:

克隆函数使用计数器对象遍历存储库列表并克隆到服务器。如果克隆应该在服务器启动时执行一次，或者在用户路径开始时根据请求执行一次，则允许在克隆请求处理程序中声明这样一个对象，如下例所示:

这个处理程序将在一个 GET 请求上沿着/git/api/clone 路径执行，counter 将包含已经从 resolved 字段中的列表克隆的存储库的数量，并将调用 clone 函数，直到遍历整个列表。然而，在这种情况下，在克隆回调内部，我们得到一个在外部变异的对象。这种行为会对执行产生负面影响，因此您可以将计数器对象实现为 iterable:

然后在克隆功能中你可以行:

```
counter.resolved += 1;
counter.onResolve();
```

替换为:

```
counter.next();
```

在将存储库克隆到服务器上之后，您可以对它们执行操作，以获得关于提交、分支等的信息。例如，要通过 get 请求按存储库名称获取分支，您可以实现以下内容:

获取存储库提交:

获取分页存储库目录树中特定路径下分支的文件列表:

需要 walkTree 函数来为所请求的目录过滤文件列表，因为当在 eventEmmiter 中形成列表时，文件名是用来自存储库根目录的完整路径写入的: