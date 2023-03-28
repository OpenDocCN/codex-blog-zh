# 使用 Lambdas 的装饰设计模式

> 原文：<https://medium.com/codex/decorator-design-pattern-with-lambdas-9fbe1f03551c?source=collection_archive---------14----------------------->

今天，让我们尝试在 Java Lambda 表达式的帮助下实现装饰设计模式。

![](img/0cae1c14a7e789a447912323617c4fd4.png)

马文·迈耶在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

```
public static void printit(int n, String msg, Function<Integer, Integer> func){
    System.*out*.println(n+" " + msg+" : "+func.apply(n));
}
```

假设我们有一个打印输出的方法。它接受函数接口作为参数。函数接口是包含单个方法的接口。函数接口的美妙之处在于它可以和 Lambda 表达式一起使用，产生令人惊叹的结果。

我们如何执行上述方法？

```
*printit*(5, "printing incremented value .. ", e -> e + 1 );
```

我们在这里所做的是创建一个函数接口的实现来给定值加 1。为了使它更具可读性，让我们把它重写为

```
Function<Integer, Integer> incremented = e -> e + 1;
*printit*(5, "printing incremented value .. ", incremented);
```

在这里，我们创建了 incremented 类，并将其传递给 printit 方法。如果你不熟悉这个 lambda 表达式，这是传统的编码。

```
Function<Integer, Integer> incremented = new Function<Integer, Integer>() {
    @Override
    public Integer apply(Integer e) {
        return e+1;
    }
};
*printit*(5, "printing incremented value ..", incremented);
```

希望你清楚我们正在做的代码。

```
import java.util.function.Function;public class DecoratorDP {
    public static void main(String[] args) {
        Function<Integer, Integer> incremented = e -> e+1;
        *printit*(5, "incremented", incremented);
    } public static void printit(int n, String msg, Function<Integer,  Integer> func){
        System.*out*.println(n+" " + msg+" : "+func.apply(n));
    }
}
```

## 问题

```
Function<Integer, Integer> incremented = e -> e+1;
*printit*(5, "incremented", incremented);
```

这将给定值加 1。

```
Function<Integer, Integer> doubled = e -> e * 2;
*printit*(5, "doubled", doubled);
```

这将使给定值加倍。

现在让我们假设我们需要增加这个值，然后加倍它。

```
Function<Integer,Integer> incAndDouble = e -> (e + 1) * 2;
*printit*(5, "incAndDouble", incAndDouble);
```

是的，这将工作，实施没有问题。但是代码重用呢？如果我们决定将增量逻辑改变两个而不是一个呢？我们将不得不在两个地方更改代码。当代码变得复杂时，代码维护将变得困难，对吗？

## 解决办法

装饰设计模式可以在这里帮助我们！

```
*printit*(5,"incAndDouble ", incremented.andThen(doubled));
```

我们在这里做了什么？我们使用了函数接口的**和**方法来连接两个函数！Java 框架将在递增的基础上应用双精度接口。您可以继续添加更多 as，然后返回相同的函数对象。

```
import java.util.function.Function;public class DecoratorDP { public static void main(String[] args) {
        Function<Integer, Integer> incremented = e -> e+1;
        *printit*(5, "incremented", incremented); Function<Integer, Integer> doubled = e -> e * 2;
        *printit*(5, "doubled", doubled); *printit*(5,"incAndDouble ", incremented.andThen(doubled)); } public static void printit(int n, String msg, Function<Integer,  Integer> func){
        System.*out*.println(n+" " + msg+" : "+func.apply(n));
    }
}
```

## 结论

在这段代码中，当我们需要增量和双倍代码时，我们没有复制代码逻辑。我们重用了相同的旧代码逻辑。这就是使用这种设计的好处。这使得代码维护更容易，代码也更具可扩展性。

例如，如果我们需要先将代码加倍，然后再递增，该怎么办？(在本例中，输出不会改变)

```
*printit*(5,"print ... ", doubled.andThen(incremented));
```

如果我们需要增加一倍，然后再增加一倍呢？

```
*printit*(5,"..", incremented.andThen(doubled).andThen(incremented));
```

快乐编码…

## 另一个例子

```
import java.awt.*;
import java.util.Optional;
import java.util.function.Function;

public class CameraExample {
    public static void main(String[] args) {
        *printit*(new Camera(e -> e));
        *printit*(new Camera(Color::brighter));
        *printit*(new Camera(Color::darker));    
        *printit*(new Camera(Color::brighter,Color::brighter));
    }
    public static void printit(Camera camera){
        System.*out*.println(camera.snap(new Color(100,100,100)));
    }
}

class Camera{
    Function<Color, Color> filter;
    public Camera(Function<Color,Color>... filters) {
        filter = e -> e;

        for(Function<Color, Color> aFilter: filters)
            filter = filter.andThen(aFilter);
    }

    public Color snap(Color input) {
        return filter.apply(input);
    }
}
```

输出将是

```
java.awt.Color[r=100,g=100,b=100]
java.awt.Color[r=142,g=142,b=142]
java.awt.Color[r=70,g=70,b=70]
java.awt.Color[r=202,g=202,b=202]
```

在这段代码中，我们将为相机添加滤镜，以模拟真实世界中相机滤镜的使用。

```
*printit*(new Camera(Color::darker));
```

这段代码将创建一个 camera 类，通过传入代码使颜色变深。在摄像机构造函数中，默认类由

```
filter = e -> e;
```

然后基于构造函数参数添加过滤器

```
filter = filter.andThen(aFilter)
```

这时，增加了使颜色变深的滤镜。当 printit 方法调用时，代码在执行。

```
camera.snap(new Color(100,100,100))
```

到目前为止，我们一直在创建执行代码的类。在 snap 方法中，我们真正传递参数并获得输出。

因为我们使用了一系列的构造函数，我们可以添加许多滤镜来使颜色变得更暗或更亮，就像我们在这里做的那样。

```
*printit*(new Camera(Color::brighter,Color::brighter));
```

这个代码使颜色变亮两倍。

## 代码改进

相机构造函数可以重写为

```
filter = Stream.*of*(filters)
        .reduce(e->e,(aFilter,result)->result.andThen(aFilter));
```

再次改写为

```
filter = Stream.*of*(filters)
        .reduce(Function.*identity*(),Function::andThen);
```

> Java 8 身份函数 **Function.identity()** 返回一个`**Function**`，它总是返回它的输入参数。

最后…

```
public class CameraExample {
    public static void main(String[] args) {
        *printit*(new Camera(Color::brighter,Color::brighter));
    }

    public static void printit(Camera camera){
        System.*out*.println(camera.snap(new Color(100,100,100)));
    }
}

class Camera{
    Function<Color, Color> filter;
    public Camera(Function<Color,Color>... filters) {

        filter = Stream.*of*(filters)
                .reduce(Function.*identity*(),Function::andThen);

    }

    public Color snap(Color input) {
        return filter.apply(input);
    }
}
```

如果你感兴趣，这是我其他博客的链接…

[](https://nuwanzen.medium.com/java-blogs-bb6e034ba1cb) [## Java 博客

nuwanzen.medium.com](https://nuwanzen.medium.com/java-blogs-bb6e034ba1cb)