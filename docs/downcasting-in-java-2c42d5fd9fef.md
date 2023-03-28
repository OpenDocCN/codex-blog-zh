# Java 中的向下转换

> 原文：<https://medium.com/codex/downcasting-in-java-2c42d5fd9fef?source=collection_archive---------11----------------------->

![](img/d8df6ebdf0f1480b8f8a933a01440e22.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Java 中的向上转换是隐式完成的。考虑一个基类动物和子类狗和猫。

```
public class Animal {
    public void move()
    {
        System.out.println("Animals can move");
    }public void sleep()
    {
        System.out.println("Animals can sleep");
    }
}
```

狗是动物的一个亚纲

```
public class Dog extends Animal {public void move()
    {
        System.out.println("Dogs can move");
    }
    public void bark()
    {
        System.out.println("Dogs can bark");
    }
}
```

猫类是动物的一个亚纲

```
public class Cat extends Animal {public void move()
    {
        System.out.println("Cats can move");
    }
    public void meow()
    {
        System.out.println("Cats can meow");
    }
}
```

使用下面这段代码，我们可以直接访问父类成员和子类的重写方法。

```
Animal animal = new Dog();animal.move();animal.sleep();
```

上面代码的输出:

```
Dogs can move
Animals can sleep
```

但是如果我们想使用动物类型的变量来调用一个只对狗类可用的方法呢？**降档**来了。这是从超类到子类的转换。

如果我们直接执行向下转换，编译器会给出一个编译错误。

```
Dog dog = new Animal();  // Incompatible types error
```

如果我们通过[类型转换](https://www.geeksforgeeks.org/class-type-casting-in-java/)执行向下转换，就会在运行时抛出 ClassCastException。

```
Dog dog = (Dog)new Animal(); //ClassCastException at runtime
```

下面是正确的做法。

```
Animal animal = new Dog();Dog dog = (Dog)animal;dog.bark();  //method specific to subclass is accessible
```

# **安全铸造**

在显式强制转换的情况下，强烈建议在尝试使用 **的 [*实例强制转换*](https://www.baeldung.com/java-instanceof)**之前**检查类型的兼容性。**Java 语言提供了 instanceof 关键字，用于在造型前检查对象的类型。例如:

```
if (animal instanceof Cat) {Cat cat = (Cat) anim;cat.meow();} else if (animal instanceof Dog) {Dog dog = (Dog) anim;dog.bark();}
```

因此，如果您不确定原始对象类型，请在转换前使用 instanceof 运算符检查类型。这消除了抛出的风险。