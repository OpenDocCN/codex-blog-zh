# 强大的 BI 工具:Apache 超集

> 原文：<https://medium.com/codex/powerful-bi-tool-apache-superset-ba8e17a8549b?source=collection_archive---------4----------------------->

## 如何构建和运行超集的清晰解释。我们如何添加像 MsSQL 这样的新数据源。我们如何在自定义数据库中存储元数据。如何在超集上应用局部更改？

我记得第一次看到 Apache Superset 的时候，我超级开心。我非常兴奋能使用它。然而，没过多久，当我试图在本地运行它时，我就开始痛苦了。然后我创作了一些脚本，我将在接下来的文章中与你分享，以结束我的痛苦。在深入故事之前；您可以访问下面保存我的脚本的回购。

[](https://github.com/mebaysan/Superset-Production-Environment) [## GitHub-mebaysan/Superset-Production-Environment:我创建这个 repo 是为了准备一个超集…

### 实际上，这是一个保存了一些脚本的简单回购。那么，我为什么要创造这个呢？我在试着跑的时候很痛苦…

github.com](https://github.com/mebaysan/Superset-Production-Environment) ![](img/bbf8869d6740330c324feba10a521c9b.png)

汉斯-彼得·高斯特在 [Unsplash](https://unsplash.com/s/photos/business-intelligence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这个故事中，我将讲述我在尝试运行超集时获得的经验。

# 从源运行超集

实际上，有几种运行和安装 Superset 的方法在它的官方文档中提到过。我们可以通过下面的链接来查看它们。

 [## 简介|超集

### Apache Superset 是一个现代化的企业级商业智能 web 应用程序。它速度快，重量轻…

superset.apache.org](https://superset.apache.org/docs/intro) 

我试过三种方法:

*   使用 [PyPi](https://pypi.org/project/apache-superset/) 安装
*   从[源代码](https://github.com/apache/superset/tree/latest)运行
*   运行与[对接编写](https://superset.apache.org/docs/installation/installing-superset-using-docker-compose)

对他们中的任何一个，我都得不到我想要的结果。

*   我用 PyPi 的方法成功了，但是我不能设置配置，比如改变元数据数据库。
*   我不能从源代码运行。每当我尝试的时候都会出错。
*   用 Docker Compose 得到的结果真的很接近我想要的结果。我需要改变元数据数据库。然而，我只能运行`non-dev`模式。为此，我需要在`dev`模式下运行超集。当我试图运行`dev`模式时，我得到了错误`npm WARN EBADENGINE Unsupported engine`。我通过给`docker-compose.yml`文件赋予`sudo`特权解决了这个问题。然后另一个问题出现了:`MSDialect_adodbapi`。我差一点就崩溃了。

然后，我在网上阅读了一些关于如何定制超集以及如何将它作为 Docker 容器来运行的论文。我开始创建自己的脚本，为制作做准备。

# 准备 docker 文件，使超集成为生产就绪

Docker Hub 上有一个来自 Apache 的超集 Docker 映像。

 [## 码头枢纽

### 编辑描述

hub.docker.com](https://hub.docker.com/r/apache/superset) 

我通过使用 Docker 映像成功运行了超集。但对我来说，运行 Superset 并不重要。我与如何将我的本地更改(例如，设置元数据数据库)应用到超集有关。因为我已经通过多种方式成功地在本地运行了超集。

此时，我无法将我的元数据存储在我想要的数据库中。[默认情况下，超集使用 SQLite 将我们的元数据存储在一个卷中。我很担心如果卷被删除会发生什么。此外，我无法安装我想要使用的数据库驱动程序。](https://github.com/apache/superset/blob/master/docker/pythonpath_dev/superset_config.py#L56)

我阅读源代码来解决我的特殊问题，比如如何更改我的元数据数据库。我了解到，如果我们设置`SUPERSET_CONFIG_PATH`变量，那么`CONFIG_PATH_ENV_VAR`将在运行时创建，我们将能够覆盖配置变量。我们可以通过访问链接来了解这一点。

[](https://github.com/apache/superset/blob/master/superset/config.py#L1351) [## 主 apache/superset 上的 superset/config.py

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/apache/superset/blob/master/superset/config.py#L1351) 

显然，我需要对`superset_config.py`文件进行修改。我也是从下面的链接得知的。

 [## 配置超集|超集

### 要配置应用程序，需要创建一个文件 superset_config.py，并将其添加到 PYTHONPATH 中。如果你的…

superset.apache.org](https://superset.apache.org/docs/installation/configuring-superset) 

经过学习努力，我创造了下面的`Dockerfile`。

```
FROM apache/superset# Switching to root to install the required packages
USER rootRUN pip install psycopg2RUN pip install psycopg2-binary# Switching back to using the `superset` user
USER superset# If we set SUPERSET_CONFIG_PATH variable, CONFIG_PATH_ENV_VAR will be created while runtime and we will be able to override the configure variables [[https://github.com/apache/superset/blob/master/superset/config.py#L1351](https://github.com/apache/superset/blob/master/superset/config.py#L1351)]
COPY superset_config.py /app/ENV SUPERSET_CONFIG_PATH /app/superset_config.py
```

我想使用 PostgreSQL 来存储我的元数据。所以我在我的`Dockerfile`里加了第 4 行和第 5 行。

现在我们应该看看`superset_config.py`文件。我们可以通过这个文件覆盖超集约束变量。

```
SQLALCHEMY_DATABASE_URI = 'postgresql://USERNAME:PASSWORD@HOST:PORT/DB_NAME'
```

我们告诉 Superset 应该查看该文件以获得配置:`/app/superset_config.py`。

另外，还有一点非常重要:如果我们想使用不同的数据库类型，我们应该在我们的容器中安装它们的驱动程序。为此，我们可以添加更多的`RUN pip install <package_name>`。超集使用`SQLAlchemy`来连接数据库。

我创建了`build-image.sh`脚本来从上面创建的`Dockerfile`构建一个 Docker 图像。我可以通过这个脚本轻松地将我的本地更改应用到超集。

```
#! /bin/bash # You should execute this script everytime when you changed something in `superset_config.py` or in `Dockerfile` IMAGE_TAG='local/superset' sudo docker image rm -f $IMAGE_TAG sudo docker image build -t $IMAGE_TAG . echo -e "\e[1;32m IMAGE BUILDED: $IMAGE_TAG \e[0m"
```

还有一个脚本是`first-build-superset.sh`文件。我使用这个文件在我的元数据数据库上创建和初始化超集的数据库。此外，我可以让一个默认用户访问超集仪表板。

```
#! /bin/bash

# You should execute this script only if it's the first time you are setting up the Superset on your env

CONTAINER_NAME='superset-live'

APP_URL='http://localhost:8088/'

sudo docker container rm -f $CONTAINER_NAME

echo -e "\e[1;31m CONTAINER REMOVED: $CONTAINER_NAME \e[0m"

sudo docker run --rm -d -p 8080:8088 --net=host --name $CONTAINER_NAME local/superset

echo -e "\e[1;32m CONTAINER CREATED: $CONTAINER_NAME \e[0m"

sudo docker exec -it $CONTAINER_NAME superset fab create-admin \
               --username admin \
               --firstname Superset \
               --lastname Admin \
               --email admin@superset.com \
               --password admin

echo -e "\e[1;32m ADMIN USER CREATED! \e[0m"

sudo docker exec -it $CONTAINER_NAME superset db upgrade

echo -e "\e[1;32m DATABASE UPGRADED! \e[0m"

sudo docker exec -it $CONTAINER_NAME superset load_examples

echo -e "\e[1;32m EXAMPLES LOADED! \e[0m"

sudo docker exec -it $CONTAINER_NAME superset init

echo -e "\e[1;32m SUPERSET INIT COMPLETED! \e[0m"

echo -e "\e[1;32m YOU CAN VISIT: $APP_URL \e[0m"
```

最后，我有`build-superset.sh`文件。我用它在我的主机上重新运行超集。我不使用前一个，因为它覆盖了元数据数据库上的元数据，现在，我只需要运行超集，而不执行任何数据库操作。因为我已经通过执行前一个创建了我的元数据数据库。我只是使用下面的脚本运行超集，然后超集将自动连接到元数据数据库。我可以安全地删除我的 Docker 容器，因为我已经在我想要的数据库中存储了元数据。删除容器没有风险。

```
#! /bin/bash

# You should execute this script everytime when you changed something in `superset_config.py` or in `Dockerfile`

IMAGE_TAG='local/superset'

sudo docker image rm -f $IMAGE_TAG

sudo docker image build -t $IMAGE_TAG .

echo -e "\e[1;32m IMAGE BUILDED: $IMAGE_TAG \e[0m"
```

我们有 3 个脚本可以在本地甚至服务器上轻松运行超集。如果需要任何更新，我们可以调整`superset_config.py`文件，然后重新构建映像并重新创建一个容器。

![](img/4c09bafcaa5a97c810cf8b95151dc49f.png)

作者图片

如果需要重新共享我为这些脚本创建的 repo，您可以使用下面的链接访问它。

[](https://github.com/mebaysan/Superset-Production-Environment) [## GitHub-mebaysan/Superset-Production-Environment:我创建这个 repo 是为了准备一个超集…

### 实际上，这是一个保存了一些脚本的简单回购。那么，我为什么要创造这个呢？我在试着跑的时候很痛苦…

github.com](https://github.com/mebaysan/Superset-Production-Environment) 

# 最后

希望你喜欢。我认为应用局部变化是使用超集的关键部分。我们可以通过使用不同的方式来运行超集。但是，如果我们不能将元数据存储在我们想要的位置，我们就必须在每次重启时做以前做过的事情。另外，我会在我的 Youtube 频道上发布一个关于这个话题的视频。

[https://www.youtube.com/c/BaysanSoft](https://www.youtube.com/c/BaysanSoft)

亲切的问候。