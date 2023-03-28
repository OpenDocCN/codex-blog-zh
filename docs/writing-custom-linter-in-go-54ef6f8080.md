# 在 Go 中编写自定义 linter

> 原文：<https://medium.com/codex/writing-custom-linter-in-go-54ef6f8080?source=collection_archive---------5----------------------->

![](img/2ad7eea3c125270542deac281baf6761.png)

编写 linters 很简单。我很惊讶写一篇 Go linter 这么容易。今天，我们将编写一个 linter 来计算 Go 代码的圈复杂度。

**什么是圈复杂度？**

> *圈复杂度是一种软件度量，用于表示程序的复杂度。* [*参考*](https://en.wikipedia.org/wiki/Cyclomatic_complexity)

这个想法很简单——每当我们发现任何控制流语句，我们就增加一个复杂度。我知道我把它简化了一点，但我不想用不必要的细节淹没你。

有几个我们应该遵循的步骤来编写我们的自定义 linter。首先，我们可以创建一个测试来检查我们的 linter 是否工作。让我们把他归档。

```
package analyzer

import (
	"os"
	"path/filepath"
	"testing"

	"golang.org/x/tools/go/analysis/analysistest"
)

func TestAll(t *testing.T) {
	wd, err := os.Getwd()
	if err != nil {
		t.Fatalf("Failed to get wd: %s", err)
	}

	testdata := filepath.Join(filepath.Dir(filepath.Dir(wd)), "testdata")
	analysistest.Run(t, testdata, NewAnalyzer(), "complexity")
}
```

`analysistest.Run()`函数是简化测试 linters 的助手。它所做的是在`testate`文件夹中的`complexity`包上运行我们的 linter。我们使用`NewAnalyzer()`函数来返回分析器的实例。再加上`pkg/analyzer/analyzer.go`吧。

```
package analyzer

import "golang.org/x/tools/go/analysis"

//nolint:gochecknoglobals
var flagSet flag.FlagSet

func NewAnalyzer() *analysis.Analyzer {
	return &analysis.Analyzer{
		Name:  "cyclop",
		Doc:   "calculates cyclomatic complexity",
		Run:   run,
		Flags: flagSet,
	}
}

func run(pass *analysis.Pass) (interface{}, error) {
  return nil, nil
}
```

我们将使用标志集向 linter 输入参数。在`NewAnalyzer()`中，我们用名称`cyclop`、描述和已定义的`Run`功能来定义分析仪。它接受文档中[描述的`analysis.Pass`结构。我们现在只需要几样东西。](https://pkg.go.dev/golang.org/x/tools@v0.1.0/go/analysis#Pass)

为了在被分析的文件中报告一个问题，我们可以使用`pass.Reportf()`方法。它接受诊断的位置和用户将看到的消息。`pass.Files`是`*ast.File`的一个片段，即包内文件的列表。

我们将使用最后一个来迭代每个文件，并逐个检查它们。当我们发现问题时，我们会报告。为此，我们必须使用下面的循环迭代这些文件。

```
for _, f := range pass.Files {
		ast.Inspect(f, func(node ast.Node) bool {
		  // your code goes here
		}
}
```

这是我们棉绒的核心。`ast.Node`是一个只有两种方法的接口，在报告任何问题时都需要这两种方法。

```
type Node interface {
    Pos() token.Pos // position of first character belonging to the node
    End() token.Pos // position of first character immediately after the node
}
```

我们只对函数或方法感兴趣，所以我们必须将这种类型转换成`*ast.FuncDecl`。

```
type FuncDecl struct {
    Doc  *CommentGroup // associated documentation; or nil
    Recv *FieldList    // receiver (methods); or nil (functions)
    Name *Ident        // function/method name
    Type *FuncType     // function signature: parameters, results, and position of "func" keyword
    Body *BlockStmt    // function body; or nil for external (non-Go) function
}
```

我们的循环应该如下图所示。

```
for _, f := range pass.Files {
		ast.Inspect(f, func(node ast.Node) bool {
    f, ok := node.(*ast.FuncDecl)
			if !ok {
				return true
			}
	})
}
```

是时候计算圈复杂度了。新的`complexity`函数接受`*ast.File`。该算法在每次找到`if`、`for`、`select`或`case`语句以及`if`语句中的`||`或`&&`运算符时都会增加复杂度。

```
func complexity(fn *ast.FuncDecl) int {
	v := complexityVisitor{Complexity:1}
	ast.Walk(&v, fn)
	return v.Complexity
}

type complexityVisitor struct {
	Complexity int
}

func (v *complexityVisitor) Visit(n ast.Node) ast.Visitor {
	switch n := n.(type) {
	case *ast.FuncDecl, *ast.IfStmt, *ast.ForStmt, *ast.RangeStmt, *ast.CaseClause, *ast.CommClause:
		v.Complexity++
	case *ast.BinaryExpr:
		if n.Op == token.LAND || n.Op == token.LOR {
			v.Complexity++
		}
	}
	return v
}
```

有一条`ast.Walk(&v, fn)`线可能会令人困惑。这个函数接受在我们提供的父节点的每个子节点上调用的访问者。我们使用一个简单的[类型断言](https://tour.golang.org/methods/15)来确定特定节点的类型。

当我们有了代码，我们就可以编写测试用例。为此，让我们创建一个新的`complexity`包。在`complexity.go`文件中放入下面的代码。

```
package complexity

import "testing"

func highComplexity() { // want "calculated cyclomatic complexity for function"
	i := 1
	if i > 2 {
		if i > 2 {
		}
		if i > 2 {
		}
		if i > 2 {
		}
		if i > 2 {
		}
	} else {
		if i > 2 {
		}
		if i > 2 {
		}
		if i > 2 {
		}
		if i > 2 {
		}
	}

	if i > 2 {
	}
}

func noComplexity() {}
```

我们创建了两个函数。`noComplexity()`函数具有`1`的复杂性，因为它只有一条执行路径。linter 不应该报告任何问题。`highComplexity`有一堆`if`语句增加了这个指标。It **应该**报告一个问题。注意，我们有一个特殊的注释，让测试套件知道我们期望被报告的诊断。你可以把它想象成[给定时间然后](https://martinfowler.com/bliki/GivenWhenThen.html)方法中的`then`部分。引号内的所有内容都是正则表达式，必须与诊断消息相匹配。你可以在官方文件中读到更多相关信息。

当我们运行测试时，我们应该看到成功！下一步是使 linter 可执行。我们所要做的就是在`cmd/cyclop/cyclop.go`文件中创建一个文件，并将内容放在下面。

```
package main

import (
	"github.com/bkielbasa/cyclop/pkg/analyzer"
	"golang.org/x/tools/go/analysis/singlechecker"
)

func main() {
	singlechecker.Main(analyzer.NewAnalyzer())
}
```

上面的代码将我们的分析器作为一个独立的工具运行。就是这样！我们有一个功能齐全的去棉绒！Github 上有完整的(有点复杂的)代码[。](https://github.com/bkielbasa/cyclop)

你可以做一个可选的步骤——将你的棉绒加入到 [golangci-lint](https://github.com/golangci/golangci-lint) 中。这是我们制作的 T2 棉绒的一个例子。

我希望 Go 社区将会得到许多棒极了的棉绒，这将会节省我们几个小时或几天的时间。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

*原载于*[*https://developer20.com*](https://developer20.com/custom-go-linter/)*。*