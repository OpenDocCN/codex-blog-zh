# 重构以获得更好的可测试性

> 原文：<https://medium.com/codex/refactoring-for-better-testability-f38ad469c114?source=collection_archive---------12----------------------->

![](img/36598f866038467da9a9ce13be7ed143.png)

当我们谈论软件设计时，我们经常使用非常普通和抽象的词语。但是，实践呢？它在现实世界的项目中看起来如何？今天，你和我将开始重构一个小的待办事项应用程序，以获得更好的可测试性和可维护性。在本文中，我们将使应用程序可测试。我们将编写黑盒测试来防止一些错误，并使未来的重构更加容易和安全。

本文是迷你系列的第一部分，在这个系列中，我们对现有代码进行代码审查，并试图对其进行改进。在以后的文章中，我们将从项目中提取域，并修复一些错误。所以，敬请期待！

完整的源代码可以在 [Github](https://github.com/bkielbasa/gotodo) 上找到。当你看一看这个项目时，你可以了解项目的结构。没那么糟糕，但是有一个大问题让我们无法毫无顾虑地重构逻辑——它没有测试。这是我们今天的目标——让项目可测试。为了能够重构应用程序，我们必须有一些监护人来关注你所做的改变。怎么做？

# 重构 main()函数

在 main.go 文件中，我们有两个函数。`main`函数保存整个应用程序的初始化，并启动 HTTP 服务器。`getDB()`只是返回一个到数据库的连接。我们可以看到一些处理程序的定义。我为您阅读了代码，并准备了一些示例请求，我们可以用它们来手动测试应用程序。

创建新项目:

```
curl --request POST \ --url http://localhost:8090/project/create \ --header 'content-type: application/json' \ --data '{ "name": "Home" } '
```

列出所有可用项目:

```
curl --request GET \ --url [http://localhost:8090/projects](http://localhost:8090/projects)
```

添加待办事项:

```
curl --request POST \
  --url [http://localhost:8090/todo/create](http://localhost:8090/todo/create) \
  --header 'content-type: application/json' \
  --data '{
	"name": "lala"
}
'
```

将待办事项标记为完成:

```
curl --request POST \ --url http://localhost:8090/todo/TASK_ID/done \ --header 'content-type: application/json'
```

标记为未完成

```
curl --request POST \ --url http://localhost:8090/todo/TASK_ID/undone \ --header 'content-type: application/json'
```

诸如此类。手动测试可以工作，但是不可扩展。你想用自动测试取代它们，对吗？让我们用简单的方法做它。创建一个新文件 main_test.go，您将在其中保存应用程序的所有测试。想想你想如何与程序互动。你不想在项目中做太多的改变，所以你会忽略里面有一个真实的数据库的事实。您只想运行应用程序。这是它的样子。

```
func TestRunServer(t *testing.T) {
  ctx := context.Background()
  run, shutdown := todo.App(ctx, port)
  defer shutdown()
  go run()

  // run your tests here
}
```

您创建一个使用指定上下文的新应用程序，并在端口上运行它。然后，您启动应用程序，当测试结束时—关闭它。听起来很简单，不是吗？

为了实现这一点，你必须将`main()`函数重命名为`App.`

```
func App(ctx context.Context, port int) (func() error, func() error)
```

该函数接受上下文，HTTP 服务器将在这个端口上运行，并返回两个函数:启动服务器和关闭服务器。这将使我们完全控制服务器，这将在一会儿有用。函数中值得注意的变化是直接创建一个 HTTP 服务器，这样您就可以控制它。这是修改后的完整功能。

```
func App(ctx context.Context, port int) (func() error, func() error) {
	m := http.NewServeMux()
	s := http.Server{Addr: fmt.Sprintf(":%d", port), Handler: m}
	log.Printf("starting on port %d", port)

	db := getDB()
	repo := repositories.NewPostgres(db)
	todoHandler := handlers.ToDo{Repo: repo}
	projectHandler := handlers.Project{Repo: repo}
	m.HandleFunc("/projects", projectHandler.List)
	m.HandleFunc("/project/create", projectHandler.Create)
	m.HandleFunc("/project/{id:[0-9a-z\\-]+}/archive", projectHandler.Archive)
	m.HandleFunc("/todos", todoHandler.List)
	m.HandleFunc("/todo/create", todoHandler.Create)
	m.HandleFunc("/todo/{id:[0-9a-z\\-]+}", todoHandler.Get)
	m.HandleFunc("/todo/{id:[0-9a-z\\-]+}/done", todoHandler.MarkAsDone)
	m.HandleFunc("/todo/{id:[0-9a-z\\-]+}/undone", todoHandler.MarkAsUndone)
	return s.ListenAndServe, func() error {
		return s.Shutdown(ctx)
	}
}
```

至此，测试几乎运行完毕。您需要做的是添加您删除的缺少的 main()函数。您可以在下面找到当前代码。deferred shutdown()函数现在没有意义，因为当它被执行时，服务器已经不在了，但是接下来您将处理它。

```
func main() {
	ctx := context.Background()
	run, shutdown := App(ctx, 8090)
	defer shutdown()
	err := run()
	if !errors.Is(err, http.ErrServerClosed) {
		fmt.Println(err)
		os.Exit(1)
	}
}
```

此时，测试通过，应用程序仍然可以运行！您几乎什么都不测试，但测试将在以后得到改进。在此之前，main()函数中还缺少一样东西——除了杀死它之外，没有其他方法可以关闭应用程序。你需要一个[优雅的关机](https://developer20.com/golang-tips-and-trics-iii/)。

```
func main() {
	var gracefulStop = make(chan os.Signal)
	signal.Notify(gracefulStop, syscall.SIGTERM)
	signal.Notify(gracefulStop, syscall.SIGINT)

	ctx := context.Background()
	run, shutdown := App(ctx, 8090)

	go func() {
		_ = <-gracefulStop
		fmt.Println("shutting down...")
		err := shutdown()
		if err != nil {
			log.Fatal(err)
		}
		os.Exit(0)
	}()
	err := run()
	if !errors.Is(err, http.ErrServerClosed) {
		fmt.Println(err)
		os.Exit(1)
	}
}
```

在启动服务器之前，会创建一个新的`os.Signal`通道，在那里您会收到一个信号，表明是时候停止进程了。您使用我们的`shutdown()`函数来停止 HTTP 服务器并退出。

# 编写第一个测试

您的测试将包含三个步骤:创建一个新项目，获得一个可用项目的列表，并检查我们的新品牌项目是否在列表中可见。让我们将测试名重命名为`TestAddingNewProject`，并更新它的代码以满足需求。每次都会创建一个新的(唯一的)项目名称，以确保该项目是用正确的名称创建的。

```
name := uuid.New().String()
	reqBody := fmt.Sprintf(`{"name": "%s"}`, name)
	url := "[http://localhost:8090/project/create](http://localhost:8090/project/create)"
	client := http.Client{
		Timeout: time.Second,
	}

	// create a new project
	resp, err := client.Post(url, "application/json", strings.NewReader(reqBody))
	require.NoError(t, err)
    require.Equal(t, http.StatusOK, resp.StatusCode)

	respBody, err := ioutil.ReadAll(resp.Body)
	require.NoError(t, err)
    r := struct{ ID string }{}
	json.Unmarshal(respBody, &r)
	require.NotEmpty(t, r.ID)
```

一件值得注意的事情是，您创建了一个标准 HTTP 客户机的新实例。`http.DefaultClient`没有任何超时设置，在某些情况下，这可能会降低测试速度。等待超时可能需要一些时间:)

您收到了项目创建正确的反馈，并将其 ID 放入匿名结构中。现在，是时候检查新项目是否被保存在数据库中并被读取了。

```
 // list existing projects
	url = "[http://localhost:8090/projects](http://localhost:8090/projects)"
	resp, err = client.Get(url)
	require.NoError(t, err)
	require.Equal(t, http.StatusOK, resp.StatusCode)

	listResp := projectsListResponse{}
	respBody, err = ioutil.ReadAll(resp.Body)
	require.NoError(t, err)
	json.Unmarshal(respBody, &listResp)

	// check if the projects are on the list
	found := false

	for _, proj := range listResp.Projects {
		if proj.ID == r.ID {
			require.Equal(t, name, proj.Name)
			require.False(t, proj.Archived)
			found = true
		}
	}

	require.True(t, found, "cannot found the project on the list")
}
```

这是通过检查`/projects`端点完成的。首先，检查 HTTP 状态，当它成功时，你检查每个项目，一个接一个，并寻找全新的。当您找到它时，您要确保名称是正确的，并且项目没有从一开始就被归档。如果一切正常，测试通过！

# 摘要

今天，我们对项目进行了一点重构，这有助于我们为它编写第一个测试。你会发现我们今天在 https://github.com/bkielbasa/gotodo/pull/1 做的改变有所不同。

你的作业是为其他端点编写测试。您可以编写的一个场景是创建一个新项目，将其存档，并检查其状态是否更改为存档。正如我所说的，这是重构迷你系列的第一部分。该项目在设计和良好实践方面存在更多问题。在修复之前，我们必须做一些测试，对吗？:)

我希望你喜欢这篇文章，如果你有任何问题，请在下面的评论区留言。

这是重构的另一步。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

【https://developer20.com】最初发表于[](https://developer20.com/refactoring-for-better-testability/)**。**