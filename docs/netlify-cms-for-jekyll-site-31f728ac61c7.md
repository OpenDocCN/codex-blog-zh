# Jekyll 站点的 Netlify CMS

> 原文：<https://medium.com/codex/netlify-cms-for-jekyll-site-31f728ac61c7?source=collection_archive---------5----------------------->

设置和配置概述

![](img/ed2d32b7df0a5cad7ec058f8d73ebf85.png)

图片来源:[维基共享资源](https://commons.wikimedia.org/wiki/File:Netlify_CMS_logo.svg)

## 易于集成

Netlify CMS 是一个很好的选择，可以创建一个可定制的模板来编辑 Jekyll 站点上的内容。这是一个开源的 React 应用程序，提供了编辑和添加文本和/或媒体到您的站点的功能，并提供了实时预览。通过 UI 小部件和插件，有各种各样的定制选项。

一旦您的 Netlify 站点连接到您的项目的 GitHub repo，您还可以预览 pull 请求和 CMS 编辑器页面。

## 配置

最快的入门方法是使用一个[模板](https://www.netlifycms.org/docs/start-with-a-template/)。然而，如果你已经有了一个现有的网站，你会想要按照这个[教程](https://www.netlifycms.org/docs/add-to-your-site)来熟悉这个过程。在本文中，我们将扩展其中包含的概念和步骤。

在深入研究之前，请确保您的 Netlify 站点已连接到您项目的 repo。您可以登录您的 Netlify 帐户，选择您正在工作的当前站点，单击左侧的 Build & Deploy 选项卡，并将 GitHub repo URL 添加到 Build settings 中的 Repository 字段。

在那里，继续将“jekyll build”添加到构建命令字段，并将“_site/”添加到发布目录。因为静态站点的内容是在每次提供之前生成的，所以我们希望在用户导航到您的站点时运行 jekyll build 命令。发布目录告诉 Netlify 从哪个文件夹读取站点内容。_site 文件夹是一个自动生成的文件夹，包含处理 Jekyll Liquid 和显示我们的站点内容所需的所有源代码。

为了让其他用户能够访问 CMS 编辑器，我们需要调整 Identity 部分的一些设置。默认情况下，在当前计费周期内，您最多可以有 1，000 个活动用户。在注册首选项下，您可以选择保持注册开放，这意味着任何人都可以创建一个电子邮件帐户，并登录到网站进行更改。一个更安全的选择是让注册只邀请，这将允许您通过电子邮件发送邀请给一组选定的人。你可以在这里阅读更多关于添加身份用户[的细节。](https://docs.netlify.com/visitor-access/identity/registration-login/#registration-forms)

## 源代码格式

此时，我们需要对源代码进行一些修改。你要做的第一件事是确保你的项目使用标准的 Jekyll 站点文件结构。你可以在这里阅读更多关于 Jekyll 的文档。你也可以看看我以前的博客，[用 Jekyll](https://lauren-cun.medium.com/create-a-static-site-with-jekyll-b223b0e063c3) 创建一个静态站点了解更多细节。

接下来，在根目录下需要一个 admin 文件夹，其中包含一个 index.html 和 config.yml 文件。在 index.html 内部，您将需要添加一些类似如下的代码来为您的 CMS 编辑器创建一个 UI。

```
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Site Content Manager</title>
  <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
</head><body>
<!-- Include the script that builds page and powers Netlify CMS -->
  <script src="https://unpkg.com/netlify-cms@^2.0.0/dist/netlify-cms.js"></script>
</body>
</html>
```

config.yml 是我们存放一些后端配置以及您希望能够编辑的内容的详细信息的地方。该文件的开头通常如下所示:

```
backend:
  name: git-gateway
  branch: master
  identity_url: "https://yoursite.com/.netlify/identity"
  gateway_url: "https://yoursite.com/.netlify/git"
  squash_merges: true

publish_mode: editorial_workflow
media_folder: "assets/img/uploads"

site_url: https://yoursite.com
logo_url: https://yoursite.com/assets/img/logo.svg
```

需要注意的是，YAML 对缩进的要求非常严格。如果您没有正确地缩进，您肯定会遇到一些语法错误，并且无法访问编辑器。

描述后端的顶部链接了保存项目 repo 的 Netlify git-gateway，定义了部署分支、身份和网关 url。

将发布模式设置为编辑工作流允许我们在将这些更改提交到实时站点之前，将草稿保存为 GitHub 中的拉请求。

媒体文件夹定义了可以在 CMS 中查看、删除和添加图像和其他资产的位置。确保它指向源代码中存储资产的文件夹。

授权用户可以通过将/admin 添加到网站的主页 url 来访问 CMS 编辑器。由于用户登录到 CMS 后的默认行为是导航回站点的主页，所以我们想在项目根目录下的 index.html 中添加一个脚本，在授权用户登录时将浏览器窗口推送到编辑器。

```
<script>
  if (window.netlifyIdentity) {
    window.netlifyIdentity.on("init", user => {
      if (!user) {
        window.netlifyIdentity.on("login", () => {
          document.location.href = "/admin/";
        });
      }
    });
  }
</script>
```

## 映射可编辑内容

现在，您应该能够登录并查看 CMS 编辑器页面了。然而，您可能会注意到没有可编辑的字段，也没有显示预览。这是因为我们还没有完成在 config.yml 文件中映射可编辑内容的重要工作。

希望您已经阅读了关于文件结构的 Jekyll 文档，并且至少对文章、页面和集合的概念有一点熟悉。我们将使用现有帖子、页面和收藏的标签来编辑前端内容以及添加新数据。

下面是一个示例，说明如何在 config.yml 文件中，在之前编写的配置设置下，为博客文章设置可编辑的内容。

```
collections:  
 - name: "blog"    
   label: "Blog"    
   folder: "_posts/"    
   create: true # Allow users to create new documents  
   slug: "{{year}}-{{month}}-{{day}}-{{slug}}"
   editor:      
    preview: false    
   fields: # The fields for each document, usually in front matter         
     - { label: "Layout", 
         name: "layout", 
         widget: "hidden", 
         default: "post" }      
     - { label: "Title", name: "title", widget: "string" }      
     - { label: "Publish Date", name: "date", widget: "datetime" }      
     - { label: "Author",          
         name: "author",          
         widget: "relation",          
         collection: "authors",          
         displayFields: [display_name],          
         searchFields: [display_name],          
         valueField: "name", }      
      - { label: "Body", name: "body", widget: "markdown" }
```

小部件定义了我们在一个字段中期望的内容类型。例如，如果您使用一个图像小部件，那么该字段中会出现一个框，其中有一个按钮供用户上传图像。relation 小部件从另一个集合中获取信息。你可以在这里找到可用小工具[的完整列表。](https://www.netlifycms.org/docs/widgets/#list)

## 额外资源

Netlify 提供了相当多的关于设置 CMS 的文档。他们甚至提供与现有 Jekyll 网站[整合的指南。和往常一样，我鼓励您更深入地阅读文档，并做一些教程来接触源代码示例。最好的学习方法是实践！下面再给你一些参考，供你探讨。](https://www.netlifycms.org/docs/jekyll/)

[*Netlify CMS 演示*](https://cms-demo.netlify.com/#/collections/kitchenSink)

[*Jekyll 教程带 Netlify CMS 库*](https://github.com/adamwatters/jekyll-tutorial-with-netlify-cms)

[*Jekyll CMS 源代码示例*](https://github.com/NickStees/jekyll-cms)

[*3 天后实施 CMS*](https://www.dwolla.com/updates/implementing-netlify-cms/)