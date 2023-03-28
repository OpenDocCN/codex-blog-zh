# Rust 如何防止数据竞争

> 原文：<https://medium.com/codex/eda-needs-to-be-using-rust-pt-2-59d2263ebb03?source=collection_archive---------1----------------------->

**TL；dr:** Rust 的内存管理模型在本系列的[第 1 部分](https://jasonmccampbell.medium.com/eda-needs-to-be-using-rust-a6a09911da74)中讨论过，它很好地扩展了，在使用共享内存并发时提供了类似的数据竞争保护。这篇文章着眼于两个具体的例子，说明编译器如何在编译时防止难以重现且耗时的错误*。*

![](img/ec3fd82bde1733935b9438c8fa4e0ca9.png)

管理相互作用的线程可能很难或者不可能进行推理。静态分析可以解决许多共享数据交互不可避免的问题。照片由[尼玛·沙巴尼](https://unsplash.com/@nima__shabani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 唯一好的共享数据是不可变的共享数据

EDA ( [电子设计自动化](https://en.wikipedia.org/wiki/Electronic_design_automation))工具是一类技术计算应用，通常专注于半导体和电子系统的设计和验证。正如本系列第 1 部分[中所讨论的，运行时通常是这些计算密集型工具的竞争优势。能够最大限度提高硬件性能的高效算法是必需的，随着服务器级处理器配备](https://jasonmccampbell.medium.com/eda-needs-to-be-using-rust-a6a09911da74) [16](https://www.amd.com/en/processors/ryzen) 、 [20](https://ark.intel.com/content/www/us/en/ark/products/series/93797/intel-xeon-processor-e7-v4-family.html) 或每个设备更多的内核，这意味着并行算法。

在理想情况下，单线程进程通过传输数据进行通信，而不共享数据。在现实世界中，当一个 8 字节的指针可以传递给另一个线程时，在进程之间复制 GB 和 TB 的数据通常是不切实际的。

不幸的是，共享内存并行性很难获得并保持正确。对共享数据的所有访问都必须受到保护，并且在任何情况下都要使用保护措施。不会错过对共享数据的访问，甚至是对一个无害的、不可重入的函数的隐藏调用，该函数原本是打算供单线程使用的。

Rust 语言的明确设计目标之一是实现安全高效的并发编程，以至于社区已经采纳了“无畏并发”的口号。这篇文章将深入探讨 Rust 独特的所有权模型如何不仅确保内存安全，甚至跨线程，而且在编译时检测和防止数据竞争*。*

# 静态消除数据竞争

前一篇文章这样总结了 Rust 的所有权规则:

1.  每个值都有一个所有者(例如，变量、结构字段)，当所有者超出范围时，该值被释放(丢弃);
2.  **一个值最多只能有一个可变引用；或者**
3.  **对于一个值可能有任意数量的不可变引用，并且当它们存在时，该值可能不会被改变；**
4.  所有引用的生存期必须不长于被引用的值。

规则 2 和 3 与那些用于[读者-写者锁](https://en.wikipedia.org/wiki/Readers%E2%80%93writer_lock)的规则相同:允许多个读者(不可变引用)或单个写者(可变引用)访问。再加上内存管理的值和引用的生命周期分析，编译器现在有了静态检测潜在数据竞争的基础。

为了更好地理解这一点，让我们看一个简单的 C++例子:

```
int main() {
  std::string msg = "Hello"; std::thread t1([&](){ 
    std::cout << msg << std::endl;
  });
  msg += ", world!";
  t1.join();
  return 0;
}
```

这个例子产生了一个线程来打印`msg`的内容，而主线程继续改变这个消息。打印出什么结果？在我使用 g++ 10.2 的笔记本电脑上，我得到“你好，世界！”。稍加改动，结果改成了“你好”。如果我真的(不)幸运，如果访问重叠，我甚至可能会看到数据损坏问题。

下面是 Rust 中的同一个例子:

```
fn main() {
  let mut msg = "Hello".to_string(); let handle = thread::spawn(|| {
    println!("{}", &msg);
  });
  msg.push_str(", world!");
  handle.join().unwrap();
}
```

 [## Rust Playground —运行此示例查看完整的编译器输出](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=6f1e483ead2741a1da8457edbe2b1e78) 

编译器会产生两个错误，本例中最重要的一个错误是:

```
error[[E0502]](https://doc.rust-lang.org/stable/error-index.html#E0502): cannot borrow `msg` as mutable because it is also borrowed as immutable
 [--> src/main.rs:9:5](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=6f1e483ead2741a1da8457edbe2b1e78#)
```

传递给`thread::spawn`的闭包隐式地创建了对`msg`的不可变引用。然而，`msg`之后也可变地使用，违反了禁止同时拥有可变和不可变的值引用的禁令。编译器已经捕捉到了数据竞争。将`push_str`命令移到对`thread::spawn`的调用之上是这个问题的一个潜在解决方案，因为在创建线程时不再需要可变引用。

报告的第二个错误是`msg`可能活得不够长，因为它是堆栈分配的，将在`main`结束时被释放。尽管该示例确实加入了线程，但为了避免这个问题，编译器并不分析派生的线程会对该值做什么。

最终的、正确的版本修改了`msg`的值，然后*将*移动到衍生的线程，保证它在需要的时候保持有效。编译器还保证主线程不再访问该值—它已经被移动了。因此，编译器还防止了释放后使用错误的可能性，即派生的线程释放字符串内存，而原始线程有一个对它的悬空引用。

 [## Rust Playground —最终工作版本](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=ff95280ae1160d243e30749ad4ad683d) 

这个例子展示了编译器如何通过防止两个线程访问同一个值来检测和防止数据竞争，从而帮助实现传输数据而不是共享数据的理想情况。但是当需要共享和修改来自不同线程的数据时呢？下一个例子探索了如何利用 Rust 的所有权模型来保证同步数据被正确访问，并防止意外的(或有意的？)绕过同步的引用泄漏。

# 安全地共享可变数据

共享可变数据需要某种方法来获得对数据的独占访问，以防止在一个或多个读取器处于活动状态时被修改。通常这是通过互斥锁来完成的。

在 C++中，一个常见的模式是使用 [RAII](https://en.cppreference.com/w/cpp/language/raii) 来获取锁，并保证锁在一个块结束时被释放。一个典型的例子是这样的:

```
void MyClass::method() {
  Lock lck(mut);    // Acquire lock on mutex 'mut'
  ... use the synchronized data ...
}
```

这种模式的优点是，它保证锁在块的末尾被`Lock`析构函数释放，即使同步的代码中出现异常。不幸的是，这种模式没有明确告诉我们`MyClass`的哪些成员需要同步，也没有阻止代码在没有持有锁的情况下访问同步的数据。

Rust 利用值的单一所有权规则来消除这两个缺点。具体来说，互斥体*拥有*的值，所以所有的访问都必须通过它，并且不存在给定的数据元素是否需要同步的问题。此外，对数据的引用不能存在超过锁的生存期。怎么会？

让我们来看一个例子，看看到底发生了什么。

```
fn main() {
    let str = "Hello".to_string();
    let sref = &str;  // Attempt to cheat!

    // Atomic ref count (Arc) owns mutex, which owns the string
    let rc = Arc::new(Mutex::new(str));

    let rc_to_thread = Arc::clone(&rc);
    let h = thread::spawn(move || {
        let mut thread_str = rc_to_thread.lock().unwrap();
        (*thread_str).push_str(", world!");
    }); h.join().unwrap();
    println!("m = {:?}", sref);
}
```

 [## Rust Playground —在此运行示例](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=390e3cb99ee28142767befd3d61f37fb) 

与前面的示例类似，创建了字符串`Hello,`,字符串的其余部分将被追加。然而，在这种情况下，派生线程将执行追加，主线程将写出结果。一旦字符串被创建，它就被移动到一个`[Mutex](https://doc.rust-lang.org/std/sync/struct.Mutex.html)` [实例](https://doc.rust-lang.org/std/sync/struct.Mutex.html)中(由该实例拥有)，并且互斥体由一个 Arc ( [原子引用计数](https://doc.rust-lang.org/std/sync/struct.Arc.html))值拥有。

请记住，在 Rust 中，每个值必须只有一个所有者。Arc 是互斥体和字符串的唯一所有者，但它是特殊的:实际的“所有者”是一个堆分配的、原子引用计数的容器，这里的`rc`值是一个指向该容器的智能指针。下面一行:

```
let rc_to_thread = Arc::clone(&rc);
```

克隆智能指针，增加引用计数。因此`rc_to_thread`可以被移动到新线程——它将有一个新的所有者——但是两个 Arc 实例引用相同的值。(关于 Arc 和 Mutex 如何绕过 Rust 的规则的描述，请参见结尾的“附录:进入 Arc 和 Mutex 的不安全杂草”。)

接收到`rc_to_thread`的第二个线程能够访问该字符串。我们已经成功地使该字符串在两个线程中可用，并且只要存在对它的任何引用，该字符串就将保持有效。然而，`Arc`没有任何同步行为，所以它只能返回不可变的引用，因此派生的线程不能向它追加内容。(Rust 不允许在安全代码中使用 C++的`const_cast`)。

这就是`Mutex`发挥作用的地方:通过提供同步，`lock().unwrap()`调用能够返回一个对字符串的*可变*引用。值`thread_str`是一个智能指针，它持有锁，直到该值被丢弃(超出范围)，并提供对字符串值的访问。

下面是传递给线程的 lambda 函数:

```
{
  let mut thread_str = rc_to_thread.lock().unwrap();
  (*thread_str).push_str(", world!");
}
```

注意，这类似于 C++锁定模式，因为`thread_str` 持有锁，直到作用域结束。最大的区别是`thread_str`是一个智能指针，它既持有锁*又在解引用时提供对包含值的访问。*

为什么这种差异很重要？考虑一个稍微复杂一点的试图作弊的 lambda 函数:

```
{
  let str_ref : &String;
  if true {
    let mut thread_str = rc_to_thread.lock().unwrap();
    (*thread_str).push_str(", world!");
    str_ref = &(*thread_str);  // Save a reference outside the lock
  }
  ...
}
```

这里的代码试图在锁之外保存对同步数据的引用。然而，规则 4 阻止了这种尝试:引用不能比它们所引用的值更长久。`str_ref`比锁的持有者`thread_str`活得长，因此被阻止。

最终的工作版本避免了任何绕过锁定机制的尝试，而是在打印时通过`rc`锁定值。完整的代码可以在下面的链接中看到和实验。

 [## Rust Playground —在此运行最终版本

### 尝试注入编译器检测不到的数据竞争！](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=9f06761438f62d33b8d5e3393736d2ef) 

# 摘要

在我职业生涯的某个阶段，我是构建跨平台、基于 Python 的 GUI 团队的一员。我们被一个只在 Mac OS 上出现的罕见的内存损坏问题所困扰。随着 GUI 的稳定，它变得越来越紧迫，因为虽然不频繁，但它不是我们会附带的东西，而且它抵制调试。阿桑什么也没透露。

在专注了一两天却一无所获之后，一天早上醒来，我意识到这不是内存分配问题，而是时间问题。应用程序本身是单线程的，但是底层 GUI 库确实使用了线程。花一个小时审查与堆栈跟踪远程相关的任何代码，发现绑定库中指针的使用不同步。

像这样的错误很容易犯，但是很难重现，因此很难发现和纠正。在这种情况下，我们轻而易举地花了一周，也许更长时间，开发人员的宝贵时间试图找到问题。这正是现在可以在编译时*捕捉并消除的那种问题。*

*希望这里的例子已经提供了足够的细节，并且没有被工具淹没，让我们了解 Rust 是如何工作的，并且能够防止这样的错误。如果你有兴趣阅读更多内容，可以考虑 Mozilla 的“[无畏并发](https://blog.rust-lang.org/2017/11/14/Fearless-Concurrency-In-Firefox-Quantum.html)”帖子，该帖子讲述了他们在两次失败的尝试后成功并行化 Firefox 中的 CSS 渲染器，或者“Rust Book”关于并发的章节以了解更多细节。*

*本系列的前两篇文章关注的是非常量化的好处:内存和并发安全性。在最后一篇文章中，我将改变思路，专注于我认为有可能通过增加开源和公司专有代码的重用来显著提高开发人员生产力的内容。*

# *附录:进入弧和互斥的不安全杂草*

*![](img/75a303338cbe86c39db1678c08135f71.png)*

*unsafe 关键字允许代码在需要时跳出正常的安全界限。[千斤顶单桅帆船](https://unsplash.com/@jacksloop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照*

*`Arc`和`Mutex`类型似乎都违反了 Rust 编译器的规则:Arc 允许一个值有多个所有者，Mutex 可以从一个不可变的引用生成一个可变的引用，类似于 C++中的`const_cast`。这两个例子都很好地说明了 Rust 的`unsafe`关键字如何被用来实现安全抽象的原语。让我们看看它们是如何工作的。*

*调用`Arc::new`会创建智能指针引用容器，以及一个单独分配的内存块，用于保存引用计数和值。分配这个内存块需要一个不安全的操作，因为生存期不依赖于特定的值。当智能指针实例被创建和销毁(“删除”)时，引用计数被更新。一旦它达到零，就会执行另一个不安全的操作来丢弃该值并释放分配的内存。*

*类似地，`Arc`智能指针只返回对所包含值的不可变引用，而对不可变`Mutex`调用`lock()`会返回对所包含值的*可变*引用。一旦满足了锁定语义，互斥体就可以通过调用 C++ `const_cast`(一个不安全的操作)的等价物来实现这一点。*

*这里重要的部分是`Arc`和`Mutex`没有被编译器特殊对待，任何人都可以编写具有相同功能的新原语。那么你可能会想，如果开发人员可以插入一个`unsafe`代码块，这不是否定了 Rust 的安全优势吗？*

*答案就看`unsafe`怎么用了。在这里讨论的两种情况下，类型提供了一个定义良好的安全接口，它封装了少量不安全的代码。这允许仔细检查类型以确保正确的行为，并利用编译器对大部分代码的安全保证。*

*人们当然可以在应用程序中声明大量的代码`unsafe`，如果需要的话，可以通过编译器的检查，就像可以用 C++编写 C 风格的、大量使用指针的代码，而忽略 STL 一样。两者都有合法的用途，但通常只占应用程序的一小部分。或者，套用比尔·克林顿的话，这样的代码应该是不安全的、合法的、罕见的。*