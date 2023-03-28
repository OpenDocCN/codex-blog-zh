# 将 Kotlin 应用程序部署到 Heroku(一个 Slack 机器人)

> 原文：<https://medium.com/codex/creating-a-slack-bot-kotlin-and-deploying-it-in-heroku-32f884e39bea?source=collection_archive---------8----------------------->

## 这是创建和部署回复 Slack 命令的应用程序所需的最低要求。

![](img/f5a26f273f6225f50773636c1c9cbab6.png)

照片由[布雷特·乔丹](https://unsplash.com/es/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

📝*您可能还想学习如何将 Kotlin 应用程序部署到 Railway* *。*

# 1.初始化项目

1.  [**创建新的 Kotlin 项目**](https://lsoares.medium.com/kotlin-gradle-junit-starter-project-a113a2cc9cbb) **。**
2.  如果你还没有做`git init`，你应该现在就做。Heroku 需要 Git，这样你就可以轻松地将它部署到 Heroku。
3.  确保在根目录下有一个`.gitignore`文件，包含:

```
/.gradle
/.idea
/build
```

# 2.Heroku 应用程序

我们需要创建一个 Heroku 应用程序，为之前创建的 Kotlin 应用程序提供服务。这是基于官方的英雄文献。

1.  安装 Heroku CLI 。这提供了一种与 Heroku 互动的快捷方式——使用终端。
2.  通过运行创建一个 Heroku APP:
    `heroku apps:create **HEROKU_APP_NAME**`
    *Heroku _ APP _ NAME 需要唯一。这将把当前的 Git 存储库绑定到 Heroku 应用程序。可以在* [***Heroku 仪表盘***](https://dashboard.heroku.com/) *确认 app 存在。您可以通过在终端中运行* `*git remote -v*` *来检查 Git 的设置。*
3.  在 Kotlin app 的根文件夹下，创建一个名为`Procfile`的文件，内容如下:
    `web: java -jar build/libs/**PROJ_ID**-1.0-SNAPSHOT-all.jar`
    (要获取`PROJ_ID`，勾选`settings.gradle.kts`)。
    *这代表 Heroku 运行以启动 web 应用程序的命令。*
4.  *Heroku 需要一个* `*stage*` *目标。*在`build.gradle.kts`的底部，添加:

```
task("stage") {
    dependsOn("shadowJar")
}
```

# 3.松弛螺栓应用程序

1.  将以下依赖项添加到`build.gradle.kts`:

```
dependencies {
  implementation("org.slf4j:slf4j-simple:1.+") 
  implementation("com.slack.api:bolt:1.+")
  implementation("com.slack.api:bolt-servlet:1.+")
  implementation("com.slack.api:bolt-jetty:1.+")
}
```

2.添加 Gradle Shadow `build.gradle.kts` ( *这将允许创建一个单独的 fat jar，稍后可以在 Heroku* 中运行):

```
plugins {
   id("com.github.johnrengelman.shadow") version "7.+"
   application
}
// at the bottom:
application {
    mainClass.set("SlackMainKt")
}
```

📝*您可能需要单击 IDE 中文件右上角的按钮来刷新 Gradle。*

3.在`src/main/kotlin/SlackMain.kt`，放:

```
fun main() {
   val app = App()
   app.command("/hello") { _, _ ->
      Response.ok("world")
   }
   SlackAppServer(app, getenv("PORT")?.toInt() ?: 8080)
       .start()
}
```

📝`*PORT*` *是 Heroku 提供的环境变量。它代表 Heroku 公开的端口。因此，我们需要在该端口提供我们的应用程序。*

4.提交所有本地 Git 更改。

5.运行`git push heroku`将 app 部署到 Heroku 中(*其实第一次推送需要另外一个命令，但是消息会告诉是哪个*)。

# 4.松弛应用程序配置

*基于官方文档:* [*用 Bolt 搭建 app for Java*](https://api.slack.com/start/building/bolt-java)*[*入门 Bolt*](https://slack.dev/java-slack-sdk/guides/getting-started-with-bolt) *。**

1.  *进入 [**松弛应用**](https://api.slack.com/apps) 点击“创建新应用”。*
2.  *选择“从头开始”,单击“下一步”,命名应用程序，将其分配到工作区。*
3.  *在左侧菜单中，转到“OAuth & Permissions”，滚动到“Scopes”，添加`chat:write`。*
4.  *在菜单中，单击“斜杠命令”υ“创建新命令”。*
5.  *用
    **命令** : `hello` **请求 URL** : `BASE_APP_URL/slack/events` 获取`BASE_APP_URL`，在终端运行`heroku apps:info`(就是`Web URL`)。
    **简短描述** : `hello world` 保存更改。*
6.  **我们需要将 Slack 环境变量从 Slack UI 复制到 Heroku。*要做到:进入“基本信息”υ“App 凭据”υ“签约秘笈”υ点击“显示”υ复制即可。进入 [Heroku Dashboard](https://dashboard.heroku.com/) 选择已创建的应用程序▽“Settings”▽“Reveal Config Vars”▽添加一个名为`SLACK_SIGNING_SECRET`的新应用程序，使用之前复制的值。*
7.  *返回 Slack UI，转到“OAuth & Permissions”，单击“Install to Workspace”按钮，复制“Bot User OAuth Token”处的值。返回 Heroku 创建另一个名为`SLACK_BOT_TOKEN`的环境变量，粘贴复制的值。*
8.  **该测试 app 了！*转到松弛本身▶在任意频道键入`/hello`按回车键；你应该得到`world`作为回复。*

*![](img/90728d81fa605c0657afd7569d9c9951.png)*

*📝*如果有问题，可以在终端中调用* `*heroku logs --tail*` *。**

*[](https://github.com/lsoares/slack-bot-kotlin-sample) [## GitHub-lsoares/slack-bot-kot Lin-sample

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/lsoares/slack-bot-kotlin-sample)*