# 在 Java 中使用 gRPC 和 ProtoBuf

> 原文：<https://medium.com/codex/using-grpc-and-protobuf-in-java-f8730bb65aa7?source=collection_archive---------3----------------------->

![](img/01e2532cb18c0348391d664a22726f65.png)

## 本文是构建 Web 服务器系列文章[的一部分](https://sumit-agarwal.medium.com/golang-building-a-web-server-2d34d4f90fa1)

欢迎回到构建 Web 服务器系列。到目前为止，我们主要关注用 GoLang 编写 web 服务器。然而，有人要求我写一篇文章，介绍如何使用 Java 实现 gRPC，以及有哪些可用的库和选项来实现相同的功能。

> 先决条件:基本了解 [Spring](https://docs.spring.io/spring-framework/docs/current/reference/html/) 、 [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/) 、[依赖注入](/swlh/dependency-injection-in-go-using-fx-6a623c5c5e01)、 [RPC 和 ProtoBuf](/swlh/using-grpc-and-protobuf-in-golang-9c218d662db3) 。
> 
> 我们将使用 spring boot 来初始化一个基本服务，然后在其上添加一个 RPC 服务器和客户端。

# 编写 Java gRPC 服务器的步骤

假设我们已经从 spring [initlizr](https://start.spring.io/) 网站生成了一个代码，项目名为 *grpc-server*

1.  我们定义服务 protobuf 文件，并在`pom.xml`中添加所需的依赖项，以编译 proto 文件并生成 RPC Java 接口，作为构建过程的一部分。
2.  接下来，我们继续编写一个基本的`HelloWorld`服务器。
3.  编译代码并调用 RPC 服务器进行测试。

# 1.编写原型定义

Protobuf 定义是语言不可知的，因此不会在我们所有可用的选项中改变。对于这个服务器示例，我们将编写一个非常基本的 HelloWorld 服务，如下所示:

```
syntax = "proto3";
option java_multiple_files = true;
package com.test.grpc;

message HelloRequest {
    string firstName = 1;
    string lastName = 2;
}

message HelloResponse {
    string greeting = 1;
}

service HelloService {
    rpc hello(HelloRequest) returns (HelloResponse);
}
```

确保在`main`目录下创建这个文件，否则原型文件将不会包含在构建过程中。`File Path: <project-root>/src/main/proto/HelloService.proto`

接下来，将下面的依赖项、扩展和插件添加到`pom.xml`

```
// add the following dependency to dependencies section. This dependency helps you to run a basic GRPC server out of the box by using the io.grpc module.
<dependency>
  <groupId>net.devh</groupId>
  <artifactId>grpc-server-spring-boot-autoconfigure</artifactId>
  <version>2.12.0.RELEASE</version>
</dependency>//add the following extension to build section. This extension adds OS specific logic during the protobuf compilation step.
<extensions>
  <extension>
    <groupId>kr.motd.maven</groupId>
    <artifactId>os-maven-plugin</artifactId>
    <version>${os-maven-plugin.version}</version>
  </extension>
</extensions>// add the following plugin to the build section. This plugin compiles the protobuf files and adds it to the target folder.
<plugin>
  <groupId>org.xolstice.maven.plugins</groupId>
  <artifactId>protobuf-maven-plugin</artifactId>
  <version>${protobuf-maven-plugin.version}</version>
  <configuration>
    <protocArtifact>
      com.google.protobuf:protoc:3.3.0:exe:${os.detected.classifier}
    </protocArtifact>
    <pluginId>grpc-java</pluginId>
    <pluginArtifact>
      io.grpc:protoc-gen-grpc-java:1.4.0:exe:${os.detected.classifier}
    </pluginArtifact>
  </configuration>
  <executions>
    <execution>
      <goals>
        <goal>compile</goal>
        <goal>compile-custom</goal>
      </goals>
    </execution>
  </executions>
</plugin>// add the following to properties section
<protobuf-maven-plugin.version>0.6.1</protobuf-maven-plugin.version>
<os-maven-plugin.version>1.6.1</os-maven-plugin.version>
```

对`pom.xml`文件进行修改后，运行`mvn clean install`来编译代码，并从 protobuf 定义中生成所需的样板 java 代码。

# 2.Hello World 服务器

一旦我们了解了 protobuf 的构建和编译步骤，设置服务器就相对容易了。

让我们通过实现基类“*com . test . gRPC . helloservicegrpc . helloserviceimplbase*”来创建一个基本的 java gRPC 服务器，该基类是在我们的原型编译步骤中生成的。

```
@GrpcService
public class HelloServiceImpl extends HelloServiceImplBase {
  @Override
  public void hello(HelloRequest request, StreamObserver<HelloResponse> responseObserver) {
    System.*out*.println("Received:" + request);

    String greeting = new StringBuilder().append("Hello, ")
      .append(request.getFirstName())
      .append(" ")
      .append(request.getLastName())
      .toString();

    HelloResponse response = HelloResponse.*newBuilder*()
      .setGreeting(greeting)
      .build();
    responseObserver.onNext(response);
    responseObserver.onCompleted();
  }
}
```

接下来，我们需要初始化服务器并注册 gRPC 服务实现。修改您的`GrpcServerApplication.java`文件，如下所示:

```
@SpringBootApplication
public class GrpcServerApplication {

   public static void main(String[] args)  throws IOException, InterruptedException {
    Server server = ServerBuilder.*forPort*(8080)
      .addService(new HelloServiceImpl()).build();

    System.*out*.println("Starting server...");
    server.start();
    System.*out*.println("Server started!");
    server.awaitTermination();
   }

}
```

在上面的文件中，我们在端口`8080`上创建了一个新的 gRPC 服务器，并用它注册了我们的`HelloServiceImpl`。

# 3.编译和测试

到目前为止，我们使用 proto 定义在 Java 中创建了一个基本的 RPC 服务器实现，并将其注册到我们的服务器中。RPC 服务器创建的魔力由我们在第一步中添加的`net.devh`依赖项来处理。

现在，继续运行服务器并测试它是否满足请求。

编译运行:`mvn spring-boot:run`

上面的命令启动了一个 gRPC 服务器，可以使用多种工具对其进行测试，如 [yab](https://github.com/yarpc/yab) 和 [grpcurl](https://github.com/fullstorydev/grpcurl) 。

这样，在 Spring boot 中使用 Java 完成了服务器设置。在下一篇文章的[中，我们将通过启动一个客户机来使用这个服务器提供的数据。我们还将为此添加一个 GoLang 客户端，看看基于 RPC 的通信如何更容易、更高效地跨越多个服务，而没有任何语言障碍。](https://sumit-agarwal.medium.com/writing-fx-modules-517193b9c4f0)

C***o 丁趣事:*** *根据很多网上的研究，最不喜欢的编程语言是 Perl、Delphi、VBA。*

***请求:*** *请在评论中添加您的宝贵反馈。这将真正帮助我提高内容的质量，并使其符合您的期望。*