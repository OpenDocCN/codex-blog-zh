# Rust —模块和项目结构

> 原文：<https://medium.com/codex/rust-modules-and-project-structure-832404a33e2e?source=collection_archive---------0----------------------->

![](img/6f59e1b04940ec60587f6e9a9a879583.png)

照片由[阿兰·范](https://unsplash.com/@alain_pham?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 探索一个 Rust 项目的结构，板条箱，模块，可见性和什么是前奏！？

在本系列的[第一篇文章](https://gian-lorenzetto.medium.com/rust-a-beginner-cheat-sheet-8fd7b0ce49de)中，我讨论了如何安装 Rust 并使用 *cargo* cli 工具创建新项目。在这篇文章中，我想更详细地介绍 Rust 项目的结构，并深入了解箱子、模块和前奏的概念。

如果你还没有，那么[去安装 Rust](https://gian-lorenzetto.medium.com/rust-a-beginner-cheat-sheet-8fd7b0ce49de)并确保你可以创建一个新项目

`$ cargo new hello_rust`

提醒一下，这将创建一个新的*二进制应用程序*，所以您可以在一个终端上运行这个程序，使用——

`$ cargo run`

您应该会看到 cargo 首先编译，然后运行您的应用程序，并将以下内容写入控制台—

```
$ cargo run
“Hello, World!”
```

太好了！在本文的剩余部分，我将讨论—

*   默认的 Rust 项目结构
*   *main.rs* 文件
*   生锈*模块*(基于文件)
*   生锈模块和*可见度*
*   Rust 模块(基于文件夹)
*   什么是*前奏*？

首先，让我们打开默认项目中的内容。

# 默认的 Rust 项目

默认的 Rust 控制台应用程序非常简单，但是文件夹结构是*有意为之的*，不应该被改变

```
hello_rust
  - src
    - main.rs
  - .gitignore
  - Cargo.toml
```

注意你可以在任何时候使用`cargo check`命令来验证你的文件夹结构和 *Cargo.toml* 文件。如果你犯了一个错误(在这种情况下，我把`src`改名为`src1`)，Cargo 会告诉你需要做什么

```
error: failed to parse manifest at `/Users/gian/_working/scratch/hello_rust/Cargo.toml`Caused by:
 no targets specified in the manifest
 **either src/lib.rs, src/main.rs, a [lib] section, or [[bin]] section must be present**
```

在我们的例子中，我们必须有一个`src/main.rs`，因为我们创建了一个*二进制* *应用程序*。如果我们创建了一个新的库(将`--lib`传递给`cargo new`命令)，那么 cargo 会为我们创建`src/lib.rs`。

*Cargo.lock* 文件是自动生成的文件，不可编辑。因为 Cargo 默认为你初始化一个 Git repo，它还包括一个. *gitignore* ，有一个条目—

```
/target
```

在 `cargo build`上自动创建`target`文件夹，并在*调试*或*发布*文件夹中包含构建*工件*(取决于构建配置，记得默认为*调试*)。

如果您交叉编译到另一个平台，那么您将看到一个指定目标平台的附加级别，然后是构建配置。

最后，还有 main.rs 文件，这是我们应用程序的入口点。让我们仔细看看它的内容。

# *main.rs* 文件

默认的 *main.rs* 文件非常简单

```
fn main() {
  println!("Hello, world!");
}
```

我们有`main()`函数，这是我们应用程序的主要入口点，它只打印“Helo，World！”到标准输出。

您可能已经注意到了`println!`中的`!`——这表明`println`函数是一个 Rust 宏(一个高级 Rust 语法特性),除了记住它不是一个常规函数之外，您可以安全地忽略它的大部分内容。

虽然您现在可以愉快地在 *main.rs* 文件中编写所有 Rust 代码，但这通常并不理想；)这就是模块的用武之地！

# 模块

让我们从给 *main.rs* 添加一个结构开始。我们将逐步将这段代码从主文件中移走，但是现在只需将您的 *main.rs* 修改成这样——

```
**struct MyStruct {}**fn main() {
  **let _ms = MyStruct {}; **   <-- Note the '_'
}
```

这是你能写的最简单的程序，但是它能很好地说明 Rust 的模块。注意变量名前面的`_`——Rust 不喜欢未使用的变量(确实如此！)但是通过使用`_`前缀，我们告诉编译器这是有意的，这将防止编译器发出警告。这*不是*何时使用这个特性的好例子(“忽略”模式匹配)，但是它在其他情况下也有合法的用途。

现在，假设我们的代码失控了，我们想把非常复杂的结构转移到另一个文件中。我们希望我们的代码是*松散耦合的*和*高度内聚的*！让我们这样做，并创建一个名为 *my_struct.rs* 的新文件

```
hello_rust
  - src
    - main.rs
    **- my_struct.rs**
```

注意，我们*必须*将文件添加到`src/`文件夹下，这样编译器才能找到它。虽然文件名并不重要，但是使用 *snake_case* 是习惯用法，所以这就是我们在这里要做的。

将结构声明从 *main.rs* 中取出，放入 *my_struct.rs —*

```
// Contents of my_struct.rsstruct MyStruct {}
```

试着建立这个项目—

`$ cargo build`

如果您从 *main.rs* 中删除了结构声明，您将会看到如下错误

```
Compiling hello_rust v0.1.0 (/scratch/hello_rust)
error[E0422]: cannot find struct, variant or union type `MyStruct` in this scope
 → src/main.rs:2:15
  |
2 | let _ms = MyStruct {};
  |           **^^^^^^^^ not found in this scope**
error: aborting due to previous errorFor more information about this error, try `rustc — explain E0422`. error: could not compile `hello_rust`
```

Rust 告诉我们，它再也找不到结构的定义了。这就是*模块*的用武之地——不像其他一些语言，你必须*明确地将代码*包含到你的应用程序中。Rust 不会简单地找到文件并为您编译/包含它。

为了包含结构声明，我们需要更新我们的 *main.rs* 来添加一个*模块*引用，就像这样—

```
**mod my_struct;**fn main() {
  let _ms = MyStruct {};
}
```

在 Rust 中，所有的文件和文件夹都是*模块*。为了在一个模块中使用代码，你需要首先用`mod`语法*导入*它。本质上，这是在找到`mod my_struct;`语句的地方插入来自模块的代码。稍后将详细介绍文件夹模块。

尝试重新构建。等等，这是什么！？还是不行…嗯。让我们来看看错误信息—

```
Compiling hello_rust v0.1.0 (/scratch/hello_rust)
error[E0422]: cannot find struct, variant or union type `MyStruct` in this scope
 → src/main.rs:4:15
  |
4 | let _ms = MyStruct {};
  |           **^^^^^^^^ not found in this scope**
  |
help: consider importing this struct
  |
1 | **use crate::my_struct::MyStruct;**
  |
```

虽然错误是相同的，但现在有一个关于添加的有用提示—

`use crate::my_struct::MyStruct;`

让我们试一试——把 *main.rs* 改成这个样子(但是*还没有*构建！剧透一下，我们还有另一个问题，稍后我会谈到)—

```
mod my_struct;
**use crate::my_struct::MyStruct;**fn main() {
  let _ms = MyStruct {};
}
```

这里有一点需要解开。当您使用`mod`语句导入一个模块时，Rust *会自动*为它创建一个模块名称空间(以避免冲突),因此我们不能直接访问我们的结构类型。模块名称空间自动取自文件名(因为在这种情况下模块是一个文件)，因此是`use`语句的`**my_struct**::MyStruct;`部分——它首先来自文件名 *my_struct.rs* (没有文件扩展名)。

`use`语句中`crate::`部分的原因是*所有*锈项目都是板条箱。正如你现在看到的，Rust 项目可以由多个文件组成(这些文件是*模块*，它们可以嵌套在文件夹中(这些文件夹也是*模块*)。为了访问模块树的根，你可以使用前缀`crate::`。

所以再看一下我们的主页面，我们有——

```
mod my_struct;                 ** <-- Import the module code, placing
                                    it into the 'my_struct'
                                    namespace**use crate::my_struct::MyStruct; **<-- Map the fully qualified (from 
                                    the crate root) struct 
                                    declaration to just 'MyStruct'**fn main() {
  let _ms = MyStruct {};        **<-- Yay, we found it! .. or did we?**
}
```

如果这看起来令人困惑(我必须说，我发现来自 C#的这一点有点令人困惑)，请记住这一点—

*   您*必须*使用`mod`将一个模块(文件或文件夹)包含到您的应用程序中。
*   `use`关键字可以方便地将完全限定的类型名映射到它的类型名(你甚至可以重命名类型，但那是另一篇文章)。

# 模块—可见性

如果你不耐烦了(继续，承认吧！)那么您将试图构建先前的 *main.rs* 实例，并得到另一个错误——

```
Compiling hello_rust v0.1.0 (/scratch/hello_rust)
error[E0603]: struct `MyStruct` is private
 → src/main.rs:2:23
  |
2 | use crate::my_struct::MyStruct;
  |                       **^^^^^^^^ private struct**
  |
```

这告诉我们，尽管我们已经找到了 struct 声明，但是模块的可见性是私有的，因此我们不能在这里访问它。

Rust 中的可见性与 C#等语言略有不同，但记住一些规则是有好处的——

*   模块中的所有内容(即文件夹`/src`中的文件或子文件夹)都可以访问模块*中的其他内容*。
*   模块之外的一切*只能*访问该模块的公共成员。**

这乍一看可能很奇怪，但是它有一些非常吸引人的副作用——模块内的私有函数对于该模块内的测试仍然是可访问的(习惯性 Rust 将单元测试保持在模块内)。第二，每个模块都被强制声明一个公共接口，定义模块外部可以访问的成员。

要使一个模块的成员成为公共的，我们必须添加`pub`关键字。让我们再次访问我们的 *my_struct.rs* 文件，并将内容替换为—

```
**pub** struct MyStruct {}         <-- Add the 'pub' keyword
```

就是这样！您现在可以成功地构建我们异常复杂的应用程序:)注意，您可以将`pub`放在大多数声明上，包括结构、结构字段、函数(关联的或其他的)、常量等等。

# 模块—文件夹

现在假设我们的`MyStruct`结构失控了，我们想把它分成多个文件。我们想把这些收集到一个文件夹里，当然是为了保持整洁。

正如上面提到的，Rust 以同样的方式对待文件和文件夹(作为模块),只有一个关键的不同。

让我们首先创建一个名为`foo/`的文件夹，因为我们已经意识到`MyStruct`实际上是我们应用程序的 foo 功能的一部分。接下来将文件 *my_struct.rs* 移动到`/src/foo`中。也就是说，新的文件夹结构应该是这样的—

```
- src/
  - main.rs
  - **foo/
    - my_struct.rs**
```

现在编辑 *main.rs* 来包含我们的新模块`foo`替换`my_struct` —

```
mod **foo**;                   <-- Change the module to match the folder
use crate::**foo**::MyStruct;  <-- Update the namespace to 'foo'fn main() {
  let _ms = MyStruct {};
}
```

我们现在可以构建这个(`cargo build`)，但是我们会得到一个错误。和往常一样，Rust 的错误信息很有启发性

```
Compiling hello_rust v0.1.0 (/scratch/hello_rust)
error[E0583]: **file not found for module `foo`**
 → src/main.rs:1:1
  |
1 | mod foo;
  | ^^^^^^^^
  |
  = help: **to create the module `foo`, create file “src/foo.rs” or “src/foo/mod.rs”**
```

当试图导入一个定义为文件夹的模块时，我们使用文件夹名(就像我们之前对基于文件的模块所做的那样)，但是 Rust 期望文件夹中存在一个名为 *mod.rs* 的文件。

在这种情况下，我们可以简单地将我们的 *my_struct.rs* 重命名为 *mod.rs* ，瞧！我们的应用程序正在重新构建。

为了完整起见，让我们用另一个结构定义(虚构地命名为另一个)将一个文件添加到`foo/`文件夹中

```
// Contents of src/foo/another.rs**pub** struct Another {}   <-- We're going to expose this as public
                            from the 'foo' module so that we can
                            use it in main.rs
```

我们将新模块导入到 *mod.rs* 文件中——

```
// Contents of src/foo/mod.rs**pub mod another;** <-- Add the module import for 'another'
                            Note the use of 'pub' to expose the 
                            module 'another' as public from the 
                            module 'foo'
pub struct MyStruct {}
```

最后，尝试在 main.rs 中使用我们新的另一个结构

```
mod foo;
use crate::foo::MyStruct;
**use crate::foo::another::Another;** <-- Note that 'another' is a
                                      module within 'foo'fn main() {
  let _ms = MyStruct {};
  **let _a = Another {};           ** <-- Using prefix '_' as before
}
```

如果这看起来有点繁琐，那是因为它是。然而，有一个更好的方法。

# 序幕

让我们重新查看一下`foo/`文件夹中的 *mod.rs* 文件。将内容更改为以下内容—

```
**mod another;** <-- Remove the 'pub' modifier **pub use another::Another;** <-- Add a use'ing to map Another directly
                              into 'foo' and make it public
pub struct MyStruct {}
```

这里我们不再希望模块 another 是公共的，所以我们去掉了`pub`关键字。然后，`use`语句将把`Another`的完全限定类型映射到 *foo* 名称空间(因为我们在 foo 模块中)。

最后，让我们更新一下我们的主页

```
mod foo;
use crate::foo::{MyStruct**,Another**};fn main() {
  let _ms = MyStruct {};
  let _a = Another {};
}
```

好多了！注意，由于我们已经将类型名`Another`映射到了 *foo* 模块中，我们可以利用扩展的`use`语法一次导入多个名称。

这里的关键要点是，你应该把 *mod.rs* 文件看作是定义你的模块的接口。虽然一开始看起来有点令人生畏，但是它给了您对公开内容的很多控制，同时仍然允许在模块内进行完全访问(例如测试)。

好，那太好了…那么我听到你问什么是前奏？嗯，prelude 只是一种模式，以一种惯用的方式，使您想要公开的所有类型都可用。并不是所有的箱子都定义了前奏(虽然很多都定义了),你也不总是需要前奏，但是不管怎样，让我们继续为我们的小项目定义一个前奏。

回到我们的主题，我们开始吧

```
mod foo;**mod prelude {** <-- Create module inline **pub use crate::foo::{MyStruct,Another};** <-- Note the 'pub' here! **}****use crate::prelude::*;** <-- Make the types exposed
                                              in the prelude
                                              availablefn main() {
  let _ms = MyStruct {};
  let _a = Another {};
}
```

我们将 prelude 定义为另一个模块(使用`mod`)，只是这次我们直接指定模块，而不是让 Rust 寻找相应的文件或文件夹。

现在我们也可以像使用其他模块一样使用`prelude`模块，例如在 *mod.rs* 文件中——

```
mod another;
pub use another::Another;**use crate::prelude::*;**pub struct MyStruct {}
```

在这种人为的情况下，前奏完全没有必要。但是您可以看到，如果您在 prelude 中声明了多个板条箱、标准库类型、常量和其他模块，那么您可以立即访问它们，只需一条`use`语句。

它还强调了模块使用的其他一些有趣的部分——

*   您可以使用通配符`::*`从模块中导入所有公共名称
*   您可以使用`crate::`来访问模块树的根(也就是本例中的主模块),并且您可以在应用程序的任何地方这样做。

# 摘要

Rust 中的模块系统无疑是该语言中更令人困惑的方面之一。来自 C++/C#背景，结合模块可见性规则(和前奏)，这是彻头彻尾的混乱！但是一旦你理解了什么是模块(文件，文件夹)以及如何导入它们(`mod`)然后将名字映射到不同的模块(`use`)就开始有意义了。

记住 Rust 项目结构非常具体也很重要(应用程序 vs 库=*main . RS*vs*lib . RS*)，要求某些文件存在于不同的上下文中( *mod.rs* )。

希望这是有帮助的(它是为我写的！).

接下来，[结构，相关的函数和方法](https://gian-lorenzetto.medium.com/rust-structs-functions-and-methods-d60fd597d956)。