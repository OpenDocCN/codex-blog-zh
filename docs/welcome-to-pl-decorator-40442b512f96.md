# 欢迎来到 pl-decorator

> 原文：<https://medium.com/codex/welcome-to-pl-decorator-40442b512f96?source=collection_archive---------8----------------------->

![](img/687ad0e117f20136af228e2aa19bd41e.png)

pl-decorator 是一个 decorator 集合，支持开发人员在低时间开销下开发解决方案

# 装置

用 npm 安装 pl-decorator

```
npm install pl-decorator
```

# 特征

在这个版本中，有非常实用的装饰类，属性，类的属性和其他

装饰类:

*   **@Singleton()**
*   **@可注射()**

装饰方法:

*   **@Log** (模式:" Log " | " debug " | " info " | " warn " = " Log ")
*   **@ try catch**(error handle:new()=>error handle)
*   **@延迟**(毫秒)

属性类的装饰器:

*   **@ format date**(format:string = " DD/MM/yyyy HH:MM:ss "，localeId = "it ")
*   **@FormatNumber** (格式:string | string[] = FORMAT_NUMBER。它，选项？:国际。NumberFormatOptions)
*   **@Inject** (类型:类)

# 用法/示例

## @日志

该注释负责包装带注释的函数，并插入开始和结束函数日志。日志报告输入参数和返回值，以防函数返回值

```
import { Log  } from "pl-decorator"export class Test { public time:Date = null; constructor() {
    this.time=new Date()
  } @Log()
  getDate() {
    return this.time
  }
}
```

## @TryCatch()

该注释包装了方法，添加了 tye catch 块，并将所有异常抛出集中在 HandlerError 中。该注释在输入中接收任何实现 herrorhandler 接口的类，以便传递遇到的异常的结果，从而集中处理错误。

```
@Singleton
    export class MyErrorHandler implements ErrorHandle {
        constructor() { }
        handleError(error: Error, propertyKey: string) {
        console.log(`Exception from  method ${propertyKey}`);
        }
    } export class Test { 
        public time: string = "";
        constructor() { } @TryCatch(MyErrorHandler)
        throwExceptionTest(n) {
            throw new Error("a")
        }
    }
```

## @单身

这个注释实现了类的单例模式。通过用这个注释来注释一个类，在构造该类的时候，它先前创建的实例被返回，如果它不可用，一个新的类被创建并且该实例被保存以使它以后可用。

```
@Singleton
    export class MyErrorHandler implements ErrorHandle {
        constructor() {  }
        handleError(error: Error, propertyKey: string) {
        	console.log(`Exception from  method ${propertyKey}`);
        }
    }
```

## @FormatDate

这个注释负责按照我们喜欢的方式格式化日期。允许的值是根据此格式计算的。

1.  YYYY: 4 位数年份“2019”
2.  YY:两位数年份“19”
3.  MMMM:完整的月份“六月”
4.  嗯:3 个字符的月份“六月”
5.  一年中的某个月，用零填充的“06”
6.  m:一年中的月份“6”
7.  DD:一个月中的某一天，用零填充的“01”
8.  d:一月中的某一天“1”
9.  Do:一个月中的第几天，数字序数缩写为“1”
10.  HH:一天中的小时，从 0 到 24，用零填充，“14”
11.  h:一天中的小时，从 0 到 24，“14”
12.  hh:12 小时制中的一天中的小时，用零填充，“02”
13.  h:12 小时制中的一天中的某个时间，“2”
14.  分钟，零填充，“04”
15.  男:分钟，“4”
16.  ss:第二，零填充
17.  斯:第二
18.  答:上午或下午
19.  答:上午或下午

```
export class Test {
        @FormatDate("HH:mm:ss a")
        public time: Date = null; constructor() { this.time=new Date()} getTime() {
            return this.time;
        }
    }
```

## @格式编号

这个注释负责按照我们喜欢的方式格式化数字，例如，按照货币或其他方式

```
export class Test {
        @FormatNumber(FORMAT_NUMBER.DE, {
            style: 'currency',
            currency: 'GBP'
        })
        public value= 123234345334454; constructor() {  } getValue() {
            return this.value;
        }
    }
```

当我们打印变量值时，它是用 DE 和 GBP 货币格式化的..这是可选的，在 casi 中是默认的

## @延迟

这个注释负责 scedue 执行函数，它在输入时运行

```
export class Test { public time: Date = null; constructor() { this.time=new Date()} @Delay(5000)
        getTime() {
            return this.time;
        }
    }
```

对于调用方法 **getTime()，**需要运行 await 声明，

```
public async method() {
	let t1 = new Test();
	let response = **await** t1.getTime();
	console.log(response);
}
```

## @可注射@注射

这些注释必须一个放在要注入的类上，一个放在类属性上，在同一个注释中声明的类将被注入。

只有当该类被标记为@Injectable 时，它才会被注入，否则将会抛出一个错误，指示该类不能被注入。

```
@Injectable
    export class Test {
        public time: Date = null;
        constructor() { this.time=new Date()}
        getTime() {
            return this.time;
        }
    } @Injectable
    export class Test2 {
        @Inject(Test1) test1:Test1;
        console.log(this.test1.time);
        public time: Date = null;
        constructor() { this.time=new Date()}
        getTime() {
            return this.time;
        }
    } export class main {
        @Inject(Test1) test1:Test1;
        @Inject(Test2) test2:Test2; console.log(this.test2.time);
        console.log(this.test1.time); //in this case, test1 will be created at runtime with injection system because test1 is marked Inject(..) in Test2 class
        console.log(this.test2.test1.time);
    }
```

所有标有@Inject(…)的属性都是自动创建的，并且将处于单例模式

感谢观看