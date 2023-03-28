# 具有活动存储的 Rails API

> 原文：<https://medium.com/codex/rails-api-with-active-storage-504f64893f23?source=collection_archive---------2----------------------->

![](img/e0375aa87d6f7c7877f39e6c24b095a9.png)

照片由[乔舒亚·柯尔曼](https://unsplash.com/@joshstyle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

[活动存储](https://edgeguides.rubyonrails.org/active_storage_overview.html)是一个允许我们将文件附加到 Rails 模型上的工具。这些附件是动态的，它们可以是我们想要的任何东西，从 pdf 到 JPG 文件。在本教程中，我将教你用活动存储上传文件的基础知识，以及如何在 API 设置中使用附件。

需要一个 App？结帐[加德纳应用开发](https://gardnerappdev.com/)

官方科里角落播客:[https://anchor.fm/coreys-corner](https://anchor.fm/coreys-corner)

在这里学习 Ruby on Rails:[https://www.youtube.com/watch?v=_qxO8c6Scfs](https://www.youtube.com/watch?v=_qxO8c6Scfs)

## 安装活动存储

首先将它添加到您的 gem 文件中

```
gem 'activestorage'
```

然后从控制台运行

```
rails active_storage:install
```

然后

```
rails db:migrate
```

> 活动存储使用应用程序数据库中的三个表，分别名为`active_storage_blobs`、`active_storage_variant_records`和`active_storage_attachments`

换句话说，活动存储在我们的数据库中创建了一系列的表来关联上传的文件和我们的模型。

## 创造我们的脚手架

让我们创建一个用户支架

```
rails g scaffold User name
```

接下来，我们要在 models/user.rb add 中添加一个头像图像

```
has_one_attached :avatar
```

这一行代码只是将我们的一个活动存储 blobs(我们称之为 avatar_image)附加到我们的用户模型中。

## 上传头像

要在创建用户时添加一个头像，我们需要对用户表单及其控制器参数进行一些更改。

将以下内容添加到 _form 文件中:

```
<div class=“field”>
    <%= form.label :avatar %>
    <%= form.file_field :avatar %>
</div>
```

让我们修改我们的控制器参数，这样我们的头像上传就不会被过滤掉。

```
def user_params
     params.require(:user).permit %i[name avatar]
end 
```

## 在视图中使用头像

如果我们看不到用户头像，那么拥有用户头像是没有任何好处的！这是我们在视图中渲染图像的方式。

```
<%= if @user.avatar.attached? %> 
        <%= image_tag url_for(@user.avatar) %>
<% end %>
```

我们用**附？确定我们的虚拟角色是否被附加的方法，这样我们就不会因为调用一个 nil 对象而遇到任何错误。如果有一张图片，我们所要做的就是在头像上调用 url_for 方法，并将其传递给 image_tag 助手。**

## API 模式

在这个场景中，我们的 Rails 应用程序充当移动应用程序的 API，我们的移动应用程序需要能够在前端显示我们的用户头像。移动框架可以像 HTML 一样显示图像，它们所需要的只是图像所在的 url。

首先，让我们通过运行以下命令来创建我们的 API 控制器

```
rails g controller api/v1/users
```

V1 只代表版本 1，版本化我们的 API 允许它同时服务多个版本的移动应用程序。

我们需要通过更改来指定我们的控制器运行在 [API only 模式](https://guides.rubyonrails.org/api_app.html)

```
class *API::V1::UsersController* < ApplicationController
```

到

```
class *API::V1::UsersController* < ActionController::API
```

查看下面的完整控制器

```
class *API::V1::UsersController* < ActionController::API def index
        @users = User.all.joins(:avatar_attachment)
        render json: @users.map { |user| 
           user.as_json(only: %i[name]).merge(
           avatar_path: url_for(user.avatar) }  
     enddef show
       @user = User.find(params[:id])
       if @user.avatar.attached? 
          render json: @user.as_json(only: %i[name]).merge(
                      avatar_path: url_for(@user.avatar)
       else 
         render json: @user.as_json only: :name
      end 
   end end 
```

在我们的索引操作中，我们使用一个 [SQL JOIN 语句](https://www.w3schools.com/sql/sql_join.asp)来获取所有拥有头像附件的用户。然后我们映射我们的用户，将每个用户呈现为一个 JSON 对象。我们通过传递 as_json only 参数以及我们想要获取的属性的符号列表来呈现不必要的数据，如 created_at 和 updated_at。然后我们使用[合并](https://apidock.com/rails/ActiveRecord/SpawnMethods/merge)方法为我们的虚拟角色创建 url 的键/值对。

在我们的 show 方法中，我们简单地找到我们的用户，如果有一个附加的头像，我们通过使用 merge 语句来呈现这个头像的 url。如果没有图像，我们继续。

如果在我们的索引中，我们想呈现所有用户，不管是否有头像，我们可以这样做。

```
def index 

   @users = User.all 
   render json: @user.map{ |user| 
                 if user.avatar.attached? 
                   user.as_json(only: :name).merge(
                        avatar_path: url_for(user.avatar)
                 else 
                    user.as_json only: :name
                 end
end 
```

因为活动存储目前不支持验证，所以使用附加的？方法来确定文件是否是附加的，这样我们可以避免错误。感谢阅读！

需要一个 App？结帐[加德纳应用开发](https://gardnerappdev.com/)

官方科里角落播客:[https://anchor.fm/coreys-corner](https://anchor.fm/coreys-corner)

在这里了解更多 Ruby on Rails:[https://www.youtube.com/watch?v=_qxO8c6Scfs](https://www.youtube.com/watch?v=_qxO8c6Scfs)