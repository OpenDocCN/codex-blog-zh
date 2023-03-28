# 系统设计目标:从多个来源高效地读取属性

> 原文：<https://medium.com/codex/system-design-goal-efficient-property-reads-from-multiple-sources-da015ff0407a?source=collection_archive---------21----------------------->

## 选择正确的组件设计和数据结构来读取和选择线程安全的属性值。

🔔这篇文章最初发表在我的网站上，[MihaiBojin.com](https://MihaiBojin.com/application-settings/system-design/read-key-from-multiple-sources?utm_source=Medium&utm_medium=organic&utm_campaign=top-promo)。由于表格和美人鱼图不在介质上渲染，**我强烈推荐阅读** [**那里**](https://mihaibojin.com/application-settings/system-design/read-key-from-multiple-sources?utm_source=Medium&utm_medium=organic&utm_campaign=top-promo) **！**🔔

![](img/cc8083ae445c1dc34480c157ad433a44.png)

照片由 Hasan Almasi 在 Unsplash 上拍摄

## 所有权的问题

我开始构建 [props](https://github.com/props-sh/props) 库来标准化从多个*源*中检索`(key,value)`对，同时也允许一个简单的机制在多个源知道同一个键时建立*键*优先级。

来源可以是属性文件、系统属性，也可以来自键值存储或数据库。

由于每个 *(key，value)* 对可以在每个源上独立地改变，我们需要建立一个键的优先级，基本上回答**“谁为这个键提供值？”**问题。

我们通过引入**层的概念来做到这一点。**

每个添加的图层都可以从源中检索数据。当我们添加更多层时，对于它定义的任何键，每个层都优先于前一层。

我们称这个概念为**所有权。**

## 源与层

让我们考虑一下来源和层次。

一种简单的方法是在源和层之间建立 1:1 的关系模型，基本上创建一个实体来表示两者，然后将这些实体的列表传递给一个*注册表*。

如果两个源都指向磁盘上的*相同文件，并且*我们有两个独立读取相同数据的对象，这就产生了潜在的争用和一致性问题(例如，第 1 层读取数据，文件在磁盘上更新，第 2 层读取不同的数据)。

一致性并不总是可能的，也不总是系统的属性，但是在这种情况下，我们可以确保一个*源*在一个正在运行的 JVM 进程中拥有相同的数据视图。

与其让*源*和*层*相同(1:1 ),不如让单个*源*代表一个数据集和多个*层* (1:N ),它们在数据集改变时接收更新。这种设计的一个额外好处是，我们的系统只需要对每个数据集进行**一次读取操作，而不是对每个层进行*一次读取操作。由于 I/O 通常比内存操作更“昂贵”，因此**这是一个胜利！*****

以下是我们系统的组成部分:

*   `Source`:从原始位置(如文件系统)加载数据，使用用户定义的逻辑(如时间间隔)重新加载数据，并将任何重新加载事件通知注册层
*   `Layer`:保存每个注册表的数据副本
*   `Registry`:查询各层数据

## 检索有效值

但是如果一个层添加了一个新的键或者删除了一个现有的键呢！？

在两次读取 *X* 之间，所有者从第 3 层变为第 2 层。

解决这个问题的一个简单算法总是查询所有层，例如:

*   问第 1-3 层:你定义了 *X* 吗？
*   第 2 层和第 3 层响应*是*
*   第 3 层具有优先权，*拥有*密钥
*   x 从第 3 层复位
*   我们重复前三个步骤，并将第 2 层建立为 X 的*所有者*

这个选择并不理想。首先，我们必须在每次读取时重复这个**，这将导致每个读取属性的`O(K)`时间复杂度(其中 K 是注册表中的层数)。这听起来不像表演！**

这里的一个附带目标是处理高频率的所有权变更；例如，源数据集可能会在短时间间隔内重复设置和取消设置一个或多个键。

## 铸造值类型

在讨论所有权之前，让我们快速进入数据类型。当我们从属性文件、系统属性和环境中读取这些值时，它们总是被序列化为字符串。(该库将支持从其他数据存储(如数据库)加载值，但从属性文件转换字符串的问题仍然存在)

下面是一个标准 Java 属性文件的示例:

```
string.prop=some value
int.prop=42
float.prop=1.23
```

即使我们有整型和浮点型值，相关的 Java APIs 仍然会返回一个`Map<String,String>`，把将这些值转换成适当类型的工作留给开发人员。

实际上，当开发人员在项目中“来来去去”时，这导致了许多方式，不是那么优化地，加载字符串并重复地将它们转换成期望的类型。

当我开始构建`props`库时，我想给开发人员“一种”加载对应于每个键的值的方式，而不用担心类型、简单的验证或缓存。我的目标是实现一个简单、高效的库来处理应用程序属性！

## 我们如何建立密钥所有权？

好了，回到所有权！

注册表需要跟踪足够多的键(假设在 10，000 个范围内)并从适当的层检索值。

开发人员将能够依靠一个**注册表**来检索转换成他们想要的类型的值。

让我们想象下面的例子:一个客户端查询注册中心(1)，注册中心依次检查它的所有层(2)，等待响应(3)，然后确定*的所有者*并将*的有效值*返回给客户端(4)。

在实际应用中，我们可以假设一个*注册表*会有几层(`K`)。

如果我们的应用程序处理`N`属性(键)，那么读取所有属性至少一次将是一个`O(N*K)`操作。假设最终一个密钥是由一个层拥有的，我们所能期望的最好结果是 N 个密钥的 T4，或者每个密钥的固定时间。

这种时间复杂度不仅更有效，而且完全可以实现！

在内部，*注册表*需要跟踪哪一层拥有每个键。

> 2021 年 9 月 21 日更新:我发现了这个设计的致命缺陷！查看[下一篇文章](https://mihaibojin.com/application-settings/system-design/algorithms-for-effective-key-ownership?utm_source=Medium&utm_medium=organic&utm_campaign=rss)获得深入解释！

由于层可以同时更新，我们需要一个线程安全的数据结构。

因为我们需要**(双关语)键到层，我们需要使用一个`Map`(咄！)**

*Java SDK 提供了一个非常高效、线程安全的映射: [ConcurrentHashMap](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html) 。*

*让我们看看这在代码中会是什么样子:*

```
*public class Registry {
    ConcurrentHashMap<String, Layer> owners;

    // retrieves the value for the specified key
    public <T> T get(String key, Class<T> clz) {
        // finds the owner
        Layer owner = owners.get(key);
        // retrieves the value
        String effectiveValue = owner.get(key);
        // casts it
        return clz.cast(effectiveValue);
    }
}

public interface Layer{
    // retrieves the value for the specified key, from this layer alone
    String get(String key);
}*
```

*难题的最后一块是更新所有权。下面，我们来考察一下*新钥匙*的案例。*

```
*public class Source{
    List<Layer> layers;

    // reloads data periodically, at the specified interval
    void reload(Duration interval) {
        // the trigger will be periodically scheduled at an interval
        var trigger = () -> {
            // data is reloaded from source
            Map<String, String> data = ...;

            // and then sent to all registered layers for this source
            for (Layer layer : layers) {
                layer.onReload(data);
            }

            // we may also want a sync stage here
            // to make the new dataset available to all sources at the same time
            // but it's beyond the scope, for the moment
        }
    }
}

public class Layer{
    // processes the reloaded data
    public void onReload(Map<String, String> data) {
        // for each key in the dataset
        for (var entry : data.entrySet()) {
            // updates the value
            updateValue(entry.getKey(), entry.getValue());
            // and notifies the registry that a new owner might be available
            registry().bindKey(entry.getKey(), this);
        }
    }

    // decides the priority of this layer, in the current registry
    int priority();

    // reference to the registry that owns this layer
    abstract Registry registry();

    // stores the (key,value) pair
    abstract void updateValue(String key, String value);
}

public class Registry {
    ConcurrentHashMap<String, Layer> owners;

    // registers ownership of a layer over a key
    void bindKey(String key, Layer layer) {
        // finds the current owner
        Layer owner = owners.get(key);

        // determines if ownership change is required
        if (owner.priority() < layer.priority()) {
            // change the current owner
            owners.put(key, layer);

            // from this point, all "get"s of "key" will be routed to "layer"
        }
    }
}*
```

*上面的场景详细描述了系统将如何处理出现在层中的新键。*

*完整的实现还需要考虑*删除的密钥*和*更新的密钥*。对于这些情况，我们希望避免调用`registry().bindKey(...)`，因为这无论如何都会导致无效操作。*

*总的来说，我愿意打赌，在现实世界的场景中，所有权的变化不会像价值的变化那样频繁。因此，*添加*和*删除*密钥的前期成本(由从一个层注册和注销密钥表示)对于实现每个密钥的恒定时间(`O(1)`)读取是值得的。*

## *结论*

*在这一点上，**我对实现这段代码感到由衷的兴奋！***

*我相信随着我的发展，我会学到更多，并可能改变我对这些选择的想法，但这是目前的计划！*

*直到下一次…*

*如果你喜欢这篇文章，并想阅读更多类似的文章，请订阅我的时事通讯。我每隔几周就发一封！*