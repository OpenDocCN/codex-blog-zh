# 提取业务逻辑——项目

> 原文：<https://medium.com/codex/extracting-the-business-logic-the-project-62c07977c9e1?source=collection_archive---------19----------------------->

![](img/87f09ae518c25840ac50dea792b97fbd.png)

在[的上一篇文章](https://developer20.com/refactoring-for-better-testability/)中，我们为一个项目写了一些测试，以确保我们的重构不会破坏任何东西。为了更好地理解这个项目，我们将分离这个领域的一部分，并向它添加一个测试。这将使测试更加真实。

端到端(e2e)测试有一个问题:数据库在引擎盖下。这种态度不是无忧无虑的。首先，那些测试相当慢。当测试数量增加时，这可能是一个问题。我们使用一个有开销的真实数据库连接。

其次，这些测试不像孤立的测试那样稳定。我们必须记住在运行测试、运行所有迁移和(有时)清除表之前启动数据库。如果某样东西可以分裂——它最终会发生。如果我们想要一个有用的 CI，我们也必须在那里运行那些测试。我们需要像在本地机器上那样配置配置项。设置比仅仅运行`go test ./...`要复杂得多。

已经证实集成测试是有帮助的。单元测试应该是我们测试集的基础。这些知识激励我们去写它们。我们必须先了解核心领域。根据我们发送请求的顺序，我们可以假设创建项目是起点。看看下面的处理程序。

```
func (p Project) Create(w http.ResponseWriter, r *http.Request) {
	b, err := ioutil.ReadAll(r.Body)
	if err != nil {
		log.Printf("cannot read the body: %s", err)
		http.Error(w, "cannot read the body", http.StatusBadRequest)
		return
	}

	req := httpmodels.CreateProjectRequest{}
	err = json.Unmarshal(b, &req)
	if err != nil {
		log.Printf("cannot read the body: %s", err)
		http.Error(w, "invalid JSON provided", http.StatusBadRequest)
		return
	}

	if req.Name == "" {
		log.Printf("the name is required")
		http.Error(w, "the name is required", http.StatusBadRequest)
		return
	}

	id, err := p.Repo.CreateProject(req.Name)
	if err != nil {
		log.Printf("internal server error: %s", err)
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	resp := httpmodels.CreateProjectResponse{id}
	b, _ = json.Marshal(resp)

	w.Header().Add("content-type", "application/json")
	w.WriteHeader(http.StatusOK)
	_, _ = w.Write(b)
}
```

第一行在这一点上不是很有用。读取请求并将其解组到一个结构中是唯一的标准。我们可以看到一个看起来有点可疑的`if`语句。

```
if req.Name == "" {
		log.Printf("the name is required")
		http.Error(w, "the name is required", http.StatusBadRequest)
		return
	}
```

它说我们需要添加名字，这是绝对必要的。当我们继续阅读时，我们会注意到我们将项目的 id 返回给了 API 调用者。id 是从创建项目的函数中下载的。要创建新方案，我们必须输入其名称。每个项目都有一个`ID`。我将向您展示如何在代码中对此建模。

```
type Project struct {
    id string
    name string
}

func (p Project) Name() string {
	return p.name
}

func (p Project) ID() string {
	return p.id
}
```

我们用私有方法创建了一个新的结构。它将提供项目的正确状态，不会导致任何问题。我们使用私有字段，它是通过编写两个 getter 方法得到的。他们没有构造函数。尽管我们仍然可以创建一个自定义的构造函数。

在我们的领域中，项目必须有一个有效的名称和一个 id。我们至少可以通过两种方式实现这一目标。第一个是创建构造函数方法。这个方法将完成所有的审计。第二种方法是创建一个函数`func (p Project) IsValid() bool`。每次我们想检查项目是否是重要对象时，都会用到它。

就我个人而言，我更喜欢第一种选择，但第二种也是有效的。这都是关于偏好和案件的细节。现在，您可以进行测试了。创建一个名为 domain/project_test.go 的新文件，并如下所示放置测试。请注意，我们创建了一个新的域包。

**域包里有什么？**

在领域驱动设计(DDD)中，领域是我们应用程序的基础。它保存了应用程序的所有业务逻辑。它不能包含任何与基础结构交互的代码。该领域应该是平台和框架不可知的。

```
package domain

import "testing"

func TestProject_Test_Validation(t *testing.T) {
	testCases := map[string]struct {
		id string
		name string
	}{
		"empty ID": {
			name: "jfslfjal",
		},

		"empty name": {
			id: "jfslfjal",
		},
	}

	for _, tc := range testCases {
		_, err := NewProject(tc.id, tc.name)
		if err == nil {
			t.Error("expected that the validation fails but got no error")
		}
	}
}
```

我们必须确保检查所有的要求。测试变成红色(不编译)。这意味着还没有这样一个`NewProject`。此时，您必须将它添加到 domain/project.go 文件中。

```
func NewProject(id, name string) (Project, error)  {
	if id == "" {
		return Project{}, errors.New("the ID cannot be empty")
	}

	if name == "" {
		return Project{}, errors.New("the name cannot be empty")
	}

	return Project{id: id, name: name}, nil
}
```

测试现在应该变成绿色。我们提取了域的第一部分！该域不能与代码的其他部分直接结合。我们需要一个额外的层。现在我们将创建一个新的包，并将其命名为`app`(用于应用层)。

**app 包里有什么？**

应用层负责协调外部世界(DB、HTTP 等)和您的应用程序之间的通信。这个过程通常是这样的:从存储库中获取一个域对象，执行一个动作，然后把它放回那里。

当我们查看创建项目的 HTTP 处理程序时，我们会注意到一个简单的流程:用户提供名称，我们创建一个新项目，并返回它的`ID`。我们应该写一个测试来模拟它。

```
package app

import (
	"context"
	"github.com/bkielbasa/gotodo/domain"
	"github.com/google/uuid"
	"testing"
)

func TestAddNewProject(t *testing.T) {
	name := "my name:" + uuid.New().String()
	ctx := context.Background()

	projectServ := NewProjectService()
	p, err := projectServ.Add(ctx, name)
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	if p.ID() == "" {
		t.Errorf("ID is empty")
	}

	if name != p.Name() {
		t.Errorf("name don't match, expected (%s) but got (%s)", name, p.Name())
	}
}
```

此时，我们创建了一个新的应用程序服务。服务工作是接受项目的名称，并返回一个新创建的项目，后面跟一个错误(如果发生的话)。之后，我们必须确保名称和我们提供的一样，并且 ID 不是空字符串(这是我们现在知道的关于`ID`的信息)。您可以看到测试没有编译。这就是如何让它工作。

```
type ProjectService struct {}

func NewProjectService() ProjectService {
	return ProjectService{}
}

func (serv ProjectService) Add(ctx context.Context, name string) (domain.Project, error) {
	return domain.Project{}, nil
}
```

我们为新类型——应用程序服务——创建了缺失的构造函数。该服务有一个简单的方法来编译初始代码。当我们运行测试时，我们会注意到它失败了。这并不奇怪，因为我们在`Add`函数中什么也没做。

```
func (serv ProjectService) Add(ctx context.Context, name string) (domain.Project, error) {
	id := "gopher"
	return domain.NewProject(id, name)
}
```

从现在开始，测试是绿色的。我们可以再添加一个测试来检查我们是否写对了名字。

```
func TestAddNewProjectWithEmptyName(t *testing.T) {
	name := ""

	projectServ := NewProjectService()
	_, err := projectServ.Add(context.Background(), name)
	if err == nil {
		t.Errorf("expected error but got nil")
	}
}
```

测试应该是绿色的。我们没有检查正确，所以我们必须改变它。我们将通过获取特定 ID 的项目来更新第一个测试，并检查`Get()`方法是否仍然返回相同的项目。

```
func TestAddNewProject(t *testing.T) {
	name := "my name:" + uuid.New().String()
	ctx := context.Background()

	projectServ := NewProjectService()
	p, err := projectServ.Add(ctx, name)
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	if p.ID() == "" {
		t.Errorf("ID is empty")
	}

	if name != p.Name() {
		t.Errorf("name don't match, expected (%s) but got (%s)", name, p.Name())
	}

	p2, err := projectServ.Get(ctx, p.ID())
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	if p.ID() !=  p2.ID() {
		t.Errorf("expected ID %s but %s given", p.ID(),  p2.ID())
	}

	if p.Name() !=  p2.Name() {
		t.Errorf("expected name %s but %s given", p.Name(),  p2.Name())
	}
}
```

如您所见，代码看起来很难读懂。我们可以通过提供一个助手函数 requireProject 来修复代码。

```
func TestAddNewProject(t *testing.T) {
	name := "my name:" + uuid.New().String()
	ctx := context.Background()

	projectServ := NewProjectService(newStoreMock())
	p, err := projectServ.Add(ctx, name)
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	checkProjectName(t, p, name)

	p2, err := projectServ.Get(ctx, p.ID())
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	checkProjectName(t, p2, p.Name())
	checkProjectID(t, p2, p.ID())
}

func checkProjectID(t *testing.T, p domain.Project, expectedID string) {
	if p.ID() !=  expectedID {
		t.Errorf("expected ID %s but %s given", expectedID,  p.ID())
	}
}

func checkProjectName(t *testing.T, p domain.Project, expectedName string) {
	if p.Name() !=  expectedName {
		t.Errorf("expected name %s but %s given", expectedName,  p.Name())
	}
}
```

现在看起来好多了，但是代码不编译。要修复它，我们必须添加缺失的`Get`函数。

```
func (serv ProjectService) Get(ctx context.Context, id string) (domain.Project, error) {
	return domain.NewProject(id, "fjsfsl")
}
```

测试还是红色的。为了让它工作，我们必须添加存储，以保留我们创建的项目列表，并能够取回它。这就是我如何设计它的界面并更新`Add()`和`Get()`函数来使用的。

```
type Repository interface {
	Store(ctx context.Context, p domain.Project) error
	Get(ctx context.Context, id string) (domain.Project, error)
}

func (serv ProjectService) Add(ctx context.Context, name string) (domain.Project, error) {
	id := "gopher"
	p, err := domain.NewProject(id, name)
	if err != nil {
		return domain.Project{}, err
	}

	err = serv.storage.Store(ctx, p)
	if err != nil {
		return domain.Project{}, err
	}

	return p, err
}

func (serv ProjectService) Get(ctx context.Context, id string) (domain.Project, error) {
	return serv.storage.Get(ctx, id)
}
```

`ProjectService`不包含新功能。我们现在应该添加它。

```
type ProjectService struct {
	repo Repository
}

func NewProjectService(storage Repository) ProjectService {
	return ProjectService{storage: storage}
}
```

差不多完成了。我们只需在创建新的 ProjectService 结构的任何地方放置新的依赖项。我们需要一个新的结构来实现这个接口。让我们用一个包含 domain.Project 实例的映射创建一个新的。

```
type repoMock struct {
	data map[string]domain.Project
}

func newRepoMock() *storeMock {
	return &repoMock{
		data: make(map[string]domain.Project),
	}
}
func (s *repoMock) Store(ctx context.Context, p domain.Project) error {
	s.data[p.ID()] = p
	return nil
}

func (s *repoMock) Get(ctx context.Context, id string) (domain.Project, error) {
	return s.data[id], nil
}
```

又变绿了！我将增加一个测试，因为我们没有覆盖一个重要的案例。项目不存在怎么办？Get 函数不应该返回错误吗？存储知道项目是否存在，所以错误应该来自它。我们应该为这种情况创建一个单独的错误。

```
// in app/project.go
var ErrProjectNotFound = errors.New("the project is not found")
```

为了使我们的测试更容易，我们需要向模拟 storeMock 添加一个新的错误，并创建一个新的方法来设置给定的错误。

```
type repoMock struct {
	data map[string]domain.Project
	err error // new field
}

func (s *repoMock) Get(ctx context.Context, id string) (domain.Project, error) {
	return s.data[id], s.err // added the error here
}

func (s *repoMock) withError(err error) *storeMock {
	s.err = err
	return s
}
```

现在它是安全的，因为有了新的帮助器方法。我们可以很容易地编写一个测试。

```
func TestAGetNotExistingProject(t *testing.T) {
	id := "not exists"
	ctx := context.Background()
	storage := newRepoMock().withError(ErrProjectNotFound)

	projectServ := NewProjectService(storage)

	_, err := projectServ.Get(ctx, id)
	if !errors.Is(err, ErrProjectNotFound) {
		t.Errorf("expected error ErrProjectNotFound but got %v", err)
	}
}
```

这项工作现在差不多完成了。您应该注意到，我们对每个项目 ID 都有一个硬编码的 ID: `gopher`。我们需要准备一个能迫使我们成功的测试。

```
func TestEveryProjectShouldHaveUniqueID(t *testing.T) {
	name := "a name"

	projectServ := NewProjectService(newRepoMock())
	p1, err := projectServ.Add(context.Background(), name)
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	p2, err := projectServ.Add(context.Background(), name)
	if err != nil {
		t.Errorf("expected no error but got: %s", err)
	}

	if p1.ID() == p2.ID() {
		t.Error("every project should have a unique ID")
	}
}
```

现在是红色的。有许多方法可以生成唯一的 ID。为了消除红色，我们将使用简单的方法— [uuid](https://github.com/google/uuid) 。

```
id := uuid.New().String()
```

仅此而已！我们通过了测试。我们从当前代码中提取了域。当然，这不是我们要重构的全部业务逻辑，但这是一个很好的起点。你可以在这个[拉取请求](https://github.com/bkielbasa/chamgotodo/pull/2)中找到我们变化的不同之处。如果你对这部分有任何问题或建议，下面有一个评论区。回头见！

PS。如果你想了解更多关于领域驱动设计的知识，我可以推荐埃里克·埃文斯的这本书。(#ad)。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

*原载于*[*https://developer20.com*](https://developer20.com/extracting-the-business-logic-project/)*。*