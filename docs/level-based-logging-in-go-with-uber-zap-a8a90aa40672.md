# 基于级别的登录与优步 Zap 一起进行

> 原文：<https://medium.com/codex/level-based-logging-in-go-with-uber-zap-a8a90aa40672?source=collection_archive---------1----------------------->

## 将日志记录带入另一个层次

![](img/b01f0b7d4ca11beb29dcaa1fe00a99c7.png)

特色图像

# 目录

*   记录还是不记录
*   Go 的内置日志包
*   什么是基于级别的日志记录？
*   介绍“优步 Zap:闪电般的快速、结构化、分级的 Go 登录”
*   Zap 日志示例
*   摘要

# 记录还是不记录

## 日志记录的好处

科技行业的大多数 web 服务器都采用日志记录。对于 web 应用程序来说，日志记录有一些好处。

*   日志记录帮助开发人员发现错误和性能问题
*   日志可以重新构建并用于系统审计

## 应该记录什么

实际上，它是任何使发现和调试程序更容易的东西。日志消息可能包含对调试有用的信息，如时间戳、错误消息等。

## 什么不应该被记录

不鼓励记录安全信息，尤其是在生产环境中。例如，您不应该记录信用卡信息、用户的个人信息等。你明白了。因为如果这些日志泄露，可能会被黑客利用。

# Go 的内置日志包

Go 为开发者提供了内置的日志包。它可以用来轻松地将任何内容记录到标准输出或文件中。

## 基本日志示例

下面的代码展示了用 Go 内置的日志库进行日志记录。

```
package mainimport "log"func main() {
 log.Println("This is logging!")
}
```

上面的代码向终端产生以下结果。您可以看到日志消息中有日期和时间信息。

```
2022/01/08 14:17:45 This is logging!
```

您还可以将消息记录到一个文件中！请复制以下代码并运行。

```
package mainimport (
 "log"
 "os"
)func main() {
 logFile := openLogFile()
 defer logFile.Close()log.SetOutput(logFile)
 log.Println("This is logging!")
}func openLogFile() *os.File {
 f, err := os.OpenFile("access.log", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
 if err != nil {
  log.Fatal(err)
 }
 return f
}
```

上面的代码生成了一个名为`access.log`的文件。该文件将包含一条消息“2022/01/08 14:22:51 这是日志记录！”。

## Go 内置库有什么问题？

内置日志库的一个主要缺陷是它太简单了。当开发人员希望尽快看到结果时，这有利于快速开发。但是在生产环境中。我们通常想要更有结构的东西，而不是简单的文本信息。

"*更有结构的东西是什么意思？*“你可能会问。我们可以通过将日志分为不同的级别来构建日志，这通常称为基于级别的日志记录。

# 什么是基于级别的日志记录？

应用程序通常打印许多类型的日志信息。可能有调试消息、错误、警告、一般信息等等。当涉及到调试或错误发现时，所有这些信息都可能会给我们带来问题。幸运的是，有一个将日志消息划分为不同严重级别的概念。当日志按级别分类时，我们可以对它们进行过滤，只查看我们想要分析的级别。

例如，错误消息可能比警告消息具有更高的优先级，那么我们可以过滤掉所有警告，只查看错误。

# 介绍“优步 Zap:闪电般的快速、结构化、分级的 Go 登录”

Zap 是由优步开发的一个开源项目。这是一个“速度极快”的日志框架。您可以从他们的[存储库](https://github.com/uber-go/zap)中看到性能和基准信息。

Zap 支持七种类型的日志级别，即调试、信息、警告、错误、恐慌和致命。每个级别的描述如下。

```
const (
	// DebugLevel logs are typically voluminous, and are usually disabled in
	// production.
	DebugLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[DebugLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#DebugLevel)
	// InfoLevel is the default logging priority.
	InfoLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[InfoLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#InfoLevel)
	// WarnLevel logs are more important than Info, but don't need individual
	// human review.
	WarnLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[WarnLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#WarnLevel)
	// ErrorLevel logs are high-priority. If an application is running smoothly,
	// it shouldn't generate any error-level logs.
	ErrorLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[ErrorLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#ErrorLevel)
	// DPanicLevel logs are particularly important errors. In development the
	// logger panics after writing the message.
	DPanicLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[DPanicLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#DPanicLevel)
	// PanicLevel logs a message, then panics.
	PanicLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[PanicLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#PanicLevel)
	// FatalLevel logs a message, then calls os.Exit(1).
	FatalLevel = [zapcore](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore).[FatalLevel](https://pkg.go.dev/go.uber.org/zap@v1.20.0/zapcore#FatalLevel)
)
```

# Zap 日志示例

*免责声明*:关于如何使用这个包，我不会深究太多细节。我想展示一些你可以用 Zap 做什么的基本例子。你可以在这里找到[详细文档](https://pkg.go.dev/go.uber.org/zap)。

## 使用预设记录器记录

通常，您必须在使用它之前配置一个记录器。如果我们只是想马上使用它呢？幸运的是，Zap 提供了一个预置的记录器，方便使用。请参见下面的示例代码。

```
package mainimport (
 "log"
 "os"
 "go.uber.org/zap"
)func main() {
 logger, _ := zap.NewDevelopment()
 defer logger.Sync() logger.Info("Hello Zap!")
 logger.Warn("Beware of getting Zapped! (Pun)")
 logger.Error("I'm out of Zap joke!")
}
```

从上面的代码中，`zap.NewDevelopment()`返回一个预置的开发日志程序，它以人类可读的格式打印消息。之后，我们使用方法`Info`、`Warn`和`Error`来打印不同的日志级别。当您执行代码时，它会产生以下结果。

```
2022-01-08T15:33:54.504+0700    INFO    logging-zap/main.go:13  Hello Zap!2022-01-08T15:33:54.504+0700    WARN    logging-zap/main.go:14  Beware of getting Zapped! (Pun)
main.main
        /home/siraphob-wsl-ubuntu/go/src/github.com/copsterr/logging-zap/main.go:14
runtime.main
        /usr/local/go/src/runtime/proc.go:2552022-01-08T15:33:54.504+0700    ERROR   logging-zap/main.go:15  I'm out of Zap joke!
main.main
        /home/siraphob-wsl-ubuntu/go/src/github.com/copsterr/logging-zap/main.go:15
runtime.main
        /usr/local/go/src/runtime/proc.go:255
```

对于 INFO，将打印一条普通的日志消息。它包含时间戳、日志级别、代码位置和消息。

对于警告和错误，它们也会打印出堆栈跟踪。这对于检测错误发生的位置非常有用。

## 配置记录器

有时候，预设的记录器不适合你的需要。Zap 允许你配置自己的日志。您可以在 JSON 或 YAML 文件中定义记录器配置，然后将其解析到 Zap 记录器中。示例如下所示。

```
package mainimport (
 "encoding/json"
 "log"
 "os" "go.uber.org/zap"
)func main() {
 rawJSON := []byte(`{
   "level": "debug",
   "encoding": "json",
   "outputPaths": ["stdout"],
   "errorOutputPaths": ["stderr"],
   "encoderConfig": {
     "messageKey": "message",
     "levelKey": "level",
     "levelEncoder": "lowercase"
   }
 }`) var cfg zap.Config
 if err := json.Unmarshal(rawJSON, &cfg); err != nil {
  panic(err)
 }
 logger, err := cfg.Build()
 if err != nil {
  panic(err)
 }
 defer logger.Sync() logger.Info("Hi, custom logger!")
 logger.Warn("Custom logger is warning you!")
 logger.Error("Let's do error instead.")
}
```

上面的代码产生以下结果。

```
{"level":"info","message":"Hi, custom logger!"}
{"level":"warn","message":"Custom logger is warning you!"}
{"level":"error","message":"Let's do error instead."}
```

正如您所看到的，记录器生成 JSON 输出，所以如果我们将它们放入日志文件中会更好。我们可以通过如下所示修改记录器配置来实现这一点。

```
rawJSON := []byte(`{
   "level": "debug",
   "encoding": "json",
   "outputPaths": ["stdout, "/my.log"],       <-- This line
   "errorOutputPaths": ["stderr", "/my.log"], <-- This line
   "encoderConfig": {
     "messageKey": "message",
     "levelKey": "level",
     "levelEncoder": "lowercase"
   }
 }`)
```

从上面的代码片段中，您可以看到键`outputPaths`和`errorOutputPaths`有一个文件`my.log`作为它们的值数组的元素。当您执行代码时，它将生成一个包含日志消息的`my.log`文件。

这只是冰山一角。你可以用 Zap 做更多的事情。查看他们的[文档](https://pkg.go.dev/go.uber.org/zap)了解更多信息。

# 摘要

日志记录允许开发人员检测 bug 和错误。基于级别的日志记录有助于仅过滤必要的信息。在这篇文章中，我已经向你展示了如何使用优步 Zap 登录。然而，它可能不是满足您需求的最佳记录器。有许多日志框架供您选择。正确使用记录器可以提高软件质量和开发过程的生产率。我希望这篇文章能给你一些关于如何在你的项目中进行日志记录的想法。编码快乐！