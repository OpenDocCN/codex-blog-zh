# Kotlin:更好处理 API 响应的密封类

> 原文：<https://medium.com/codex/kotlin-sealed-classes-for-better-handling-of-api-response-6aa1fbd23c76?source=collection_archive---------0----------------------->

![](img/e376e2f511d243fe7b6a046f80ceef28.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在 Java 中表示一组固定的常数时，一个经典的选择是使用枚举。一个**枚举**类型是一个特殊的数据类型，它使一个变量成为一组预定义的常量。变量必须等于为其预定义的值之一。

例如，一副牌中的花色(梅花、方块、红心、黑桃)、API 的响应(成功、错误、装入)等。

```
public enum Response {
    *SUCCESS*, 
    *ERROR*, 
    *LOADING*
}
```

同样，在科特林我们也可以有枚举，

```
enum class Response {
    *SUCCESS*,
    *ERROR*,
    *LOADING* }
```

因此，在处理固定数量的常量时，枚举非常方便，但也有一些限制。在枚举中，每种类型只有一个对象，因此枚举只能是常量，没有状态，这就给我们带来了 Kotlin 中的密封类。

## **密封类**

密封类的概念与枚举非常相似，密封类表示受限的类层次结构，即我们的类可以有特定数量的子类，这提供了对继承的更多控制。密封类的所有子类在编译时都是已知的。密封类通过限制在编译时而不是运行时匹配的类型来确保类型安全。

与 Enum 类不同，密封类可以有状态，因为我们可以有同一个类的几个对象。

密封类有另一个明显的特征，它们的构造函数默认是私有的。密封类是隐式的**抽象的**，因此它不能被实例化。像抽象类一样，密封类允许我们表示层次结构，子类可以是任何类型，数据类，对象类，任何常规类，甚至是另一个密封类。但是与抽象类不同，我们需要在同一个文件中或者作为嵌套类来定义这些层次结构。

> 一个`sealed`类是“枚举类的扩展”

## **密封类声明**

密封类在类修饰符之前带有 sealed 关键字，

```
sealed class NetworkResult
```

我现在将展示如何使用密封类来管理 API 响应，

```
sealed class NetworkResult<T>(
    val data: T? = null,
    val message: String? = null
) {

    class Success<T>(data: T) : NetworkResult<T>(data)

    class Error<T>(message: String?, data: T? = null) : NetworkResult<T>(data, message)

    class Loading<T> : NetworkResult<T>()

}
```

在这里，我创建了一个密封的类，用 **Success、Error 和 Loading** 作为子类。每个子类可以对应于不同的可能的 API 响应，并被相应地参数化。例如，如果 API 响应成功，我们将获得数据，并且不会有错误消息，如果出现错误，将没有数据，因此我对数据使用了可空类型。类似地，对于加载状态，我们不需要任何参数。

> 我对这个类使用了泛型，这样它就可以被不同的响应类型重用。

假设我们的模型类是**结果**，

```
var response: MutableLiveData<NetworkResult<Result>> = MutableLiveData()
```

我们的响应变量将存储由 NetworkResult 类包装的响应，

```
private fun fetchResult(...) {
    response.value = NetworkResult.Loading()
    if (hasInternetConnection()) {
        try {
            val response = repository.getResult()
            if (response.code() == 200) {
                response.value = NetworkResult.Success(response)
            } else {
                response.value = NetworkResult.Error(response.message())
            }
        } catch (e: Exception) {
            response.value = NetworkResult.Error(e.message())
        }
    } else {
        response.value = NetworkResult.Error("No Internet connection !")
    }
}
```

当我们调用 fetchResult(…)时，我将响应值设置为 Loading，如果我收到一个成功的响应，我将它的值设置为获得的响应体，对于错误情况，我将根据错误设置不同的错误消息。这是一个可能的结果类型可以有不同状态的示例。
现在在调用类中，我们可以观察我们的响应活动数据并呈现 UI 元素，基于响应，

```
private fun requestApiData() { viewModel.response.observe(*viewLifecycleOwner*) **{** response **->** when (response) {
            is NetworkResult.Success -> {
                response.data?.let **{** //bind the data to the ui
                **}** } is NetworkResult.Error -> {
                //show error message
                Toast.makeText(
                    requireContext(),
                    response.message.toString(),
                    Toast.*LENGTH_SHORT* ).show()
            }

            is NetworkResult.Loading -> {
                //show loader, shimmer effect etc
            }
        }
    **}** }
```

这就是我们如何在 API 响应中使用我们的密封类。

这里需要注意的另一件事是，

> **【当】**对密封类的陈述是详尽的

如果我们漏掉任何子类，**什么时候**会抱怨？如果我们实现了所有这些，我们就不需要 else 语句了。一般来说，我们不会推荐这种方法，因为这样我们就能确定我们为他们做的是正确的事情。

希望你今天学到了一些东西，干杯！