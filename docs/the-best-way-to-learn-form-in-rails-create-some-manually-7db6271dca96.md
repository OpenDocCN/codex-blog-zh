# 学习 Rails 中表单的最好方法是手动创建一些表单

> 原文：<https://medium.com/codex/the-best-way-to-learn-form-in-rails-create-some-manually-7db6271dca96?source=collection_archive---------3----------------------->

![](img/c6f19be3166902098895d3de617bb2d1.png)

有很多方法可以在 rails 中创建表单。您可以安装许多表单创建工具中的一个来帮助您完成这个过程，或者只是简单地使用一个内置的 Rails helper 方法。仔细想想，rails 就是*所有关于表单的东西* …它让 rails 应用成为 Rails 应用，并让用户与我们的网页互动。

因此，了解表单的来龙去脉是构建高质量 Rails 应用程序的基本技能。

在本指南中，我们将使用 HTML 从头开始构建表单。随着您在 Rails 之旅中的进步，本练习的一些学习成果将对您有所帮助:

*   熟悉 Rails 使用的 MVC 模型和文件结构
*   手动设置一个 rails 应用程序来创建一个新用户
*   确保表格填写正确
*   建立一个简单的数据库，又名“模型”
*   创建和添加适当的
*   使用`presence`在 Rails 中验证我们的表单

注意，Rails 使用助手方法使得创建这些文件变得非常简单。在某些方面……它太*容易了，所以手动浏览这个过程将帮助我们更好地理解什么成分一起制作美味的 Rails 蛋糕……呃……应用程序。*

## 先决条件

*   我假设你有很少的创建 Rails 应用程序的经验——如果没有，不要太担心，因为如果你已经成功安装了 Ruby 和 Rails，你仍然可以跟着做
*   有大量的指南可以帮助您进行设置
*   如果您以前创建过 Rails 应用程序，那么您已经使用过电脑的终端应用程序，并且不能在命令行上导航文件夹。
*   熟悉 HTML 是有帮助的

## 我在写作时使用的版本

*   Ruby v. 3.0.0
*   Rails 诉 6.1.3.1
*   MacOS 11

## 第一部分:手动设置我们的应用程序

## 设置:生成一个新的 rails 应用程序

让我们为表单项目创建一个名为“强大”的新 Rails 应用程序

1.  在您的终端应用程序中，导航到您想要创建项目的文件夹，然后输入`rails new formidable`,观察 rails 构建您的项目时一行行代码飞快地掠过
2.  如果您想跟踪您正在做的事情(以及在遇到一些看似不可逆转的难看的红色错误消息之前回滚到项目版本的能力),现在是用 *git* 设置您的项目的好时机
3.  在您的终端中，导航(`cd`进入您的新应用`cd formidable`
4.  在代码编辑器中打开项目。我正在使用 VS 代码，所以我将使用`code .`从命令行打开我的项目。如果对你来说更有意义的话，你也可以使用图形用户界面。

## 创建一个“用户”模型

好了，现在我们要开始使用我们的数据库来建立模型，以接收和输出信息。幸运的是，我们不需要多年使用 SQL 的经验就能做到这一点。Rails 中的过程非常简单。

**注意**在命令行中键入命令时，Rails 就像快捷方式一样——我们将使用几次`generate`命令。我喜欢快捷方式，Rails 让我们使用`g`来代替 generate，所以我会使用它。只要记住它的意思，它做同样的事情。

1.  在你的终端，输入`rails g model User username:string email:string password:string`
2.  好的。不错！你会在你的终端上看到几行… `invoke`和一些`create`行，告诉我们我们的命令告诉 rails 创建一些新的东西。
3.  在代码编辑器中，去看看创建了什么。当我们运行这个命令时，我们构建了第一个*迁移*。迁移本质上是一个文件，它向数据库传达应该向其中输入什么以及如何输入。
4.  在代码编辑器中转至该路径查看文件`db/migrate/`

```
class CreateUsers < ActiveRecord::Migration[6.1]
  def change
	create_table :users do |t|
	  t.string :username
	  t.string :email
	  t.string :password

	  t.timestamps
    end
  end
end
```

这是我的档案的样子。注意说`create_table`的部分，后面跟着几个输入位——啊哈！这是我们在终端命令中定义的东西— `:username`、`:email`和`:password`

1.  好了，这个迁移文件就是我们想要告诉我们的数据库的样子。我们实际上还没有将数据发送到数据库…所以…这是否意味着它只是…基础？明白了吗？
    好，让我们使用这个迁移文件……嗯，将其迁移到终端中的数据库，输入`rails db:migrate` -如果你好奇，请返回到代码编辑器中的`migrate`文件夹，现在你会看到一个`schema.rb` -不要编辑那个人。我们将坚持用“迁移”来编辑数据库。

## 添加表单验证

很可能您想要限制用户发送到数据库的内容类型。你真的不希望用户提交一个空白字段。我们希望确保“用户名”有所填写，并且可能希望确保他们已经设置了密码。让我们这样做

1.  在你的编辑器里，转到`app/models/user.rb`
    应该是蛮光秃秃的。编辑该文件，如下所示:

```
class User < ApplicationRecord
	validates :username, :email, :password, presence: true
end
```

我们模型中的这个小编辑对 Rails 说，“嘿，检查(验证)这些东西，以确保当有人提交表单时它们存在。”

## 控制器:生成我们的第一个控制器

我们已经解决了 Rails MVC 结构中的 M,“模型”或数据库。现在让我们敲掉 C 部分。我们需要告诉 Rails 如何处理我们提供给我们创建的用户模型的那些数据。现在，我们简单地为我们想要执行的每个动作创建一个“空方法”——在本例中是`:create`和`:new`

1.  前往`app/controllers/user_controllers.rb`。它看起来会像这样:

```
class UserController < ApplicationController
end
```

这里没什么事。让我们添加我们的“空方法”。为什么是“空的”——因为我们不打算在这些方法中写任何东西。我们只是告诉控制器“嘿，这些东西是存在的，所以当你做事情时，只要知道它们在那里，mmmk？”将此文件更改为:

```
class UserController < ApplicationController
	def new
	end

	def create
	end
end
```

简单。现在 Rails 知道这些存在，我们离真正有趣的部分更近了一步。表格！！！但是首先，我们需要确保任何使用“新建”或“创建”的请求都被路由到正确的路径。否则…嗯..错误。

## 路线

我们需要能够正确地引导任何传入的请求。在我们的例子中，我们关心的是“创建”一个新用户。让我们的用户模型能够接收`create`和`new`请求。“咦，那些不都是一样的东西吗？”算是吧，也不算。

这可能会让我们陷入困境，但简单的答案是`new`创建一个新的对象实例，而`create`是将其保存到数据库所必需的。

关于“资源”的一个简短说明——它们本质上是我们可以使用表单执行的所有操作，遵循 CRUD 原则(创建、读取、更新、销毁)。这包括创建新用户、编辑用户或内容、删除用户、查看用户信息等。

1.  转至`routes.rb`文件，编辑我们表格的可用方法`config/routes.rb`
    为我们的用户模型添加资源行

```
Rails.application.routes.draw do 
	resources :users, only: [:new, :create]
end
```

## 查看—让我们看看我们有什么！

好吧，我们实际上还看不到任何东西。在看到任何东西之前，我们需要开始构建一些表单。现在让我们只创建我们的第一个视图(又名页面)并在那里放一点东西，这样我们就知道一切都在工作。

1.  在您的编辑器中，转到`app/views/users`。右键单击 users 文件夹，创建一个名为`new.html.erb`的新文件
2.  让我们输入一个标题进行渲染，这样我们就可以在新制作的页面上看到一些东西`<h1> This Site is Formidable! </h1>`
3.  关键时刻到了。在您的终端中，通过运行`rails server`或`rails s`启动您的 rails 服务器(又是这些可爱的快捷方式)。
4.  输出应该说它启动了您的服务器。在你的浏览器中点击这个网址:`http://localhost:3000/users/new`或[http://127 . 0 . 0 . 1:3000/users/new](http://127.0.0.1:3000/users/new)将会把你带到同一个地方。嘣！您应该会看到您的标题出现。

## 第二部分—构建 HTML 表单

这是我一开始就答应你的部分。它应该作为表单的复习工具，加深您对表单如何让 Rails 应用程序运行的理解。戴上你的 HTML 帽子，让我们构建一些丑陋但有用的表单。哦，我说过我们做的东西很丑吗？是的，这里没有 CSS。我很想美化它们，但这本指南已经够长了。

请记住，rails 有助手为我们完成大部分工作，但我们是手动完成的，目标是在表单方面做得更好。

## 手动创建新的用户表单

当我们构建表单时，在 Rails 中工作时，我们需要记住几件事情——`method`和`action`,还有`name`。这个(`name`)特别重要，我们以后会看到。

1.  打开您的`<form>`标签，输入我们的每个模型属性- `username`、`email`和`password`。
2.  记住表单标签上的动作和方法，使密码类型`type='password`。
3.  在复制我下面得到的之前，看看你是否能创建表单。不要忘记提交输入，这样我们的用户在准备好的时候就有一个按钮可以进行 mash。

…………

好了，您的`app/views/new.html.erb`文件中应该有这样的内容:

```
<form action='/users' method='post' accept-charset="UTF-8">
  <label for='username'>Username<label>
  <input type="text" id="username" name="username"> <br>

  <label for="email">Email Address</label>
  <input type="text" id="email" name="email" > <br>

  <label for="password">Password</label>
  <input type="password" id="password" name="password"> <br>

  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token%>" >

  <input type="submit" value="Submit">
</form>
```

如果你明白了，那就拍拍自己的背，喝杯啤酒吧。如果没有，也不用担心。我没有给你提供一些关键信息。

## 动作和方法一`<form>`标签

如果您在终端中运行`rails routes`，就可以找到`action='/users'`和`method='post'`。它会返回一大堆不同的路线。既然我们已经生成了控制器并添加了`:new`和`:create`资源，我们应该会看到与之对应的可用路径列表

它将为每个选项列出合适的路径和方法。

## 鉴定

我没有提到的另一件事是隐藏真实性标记的隐藏输入。这对于安全性非常重要，可以确保您的应用程序不会提交不良或恶意数据，并且只有来自您正确表单的数据才能通过。

*   这是目前为止文件中唯一嵌入的 ruby 代码。看一下上面的代码块，注意隐藏输入的`value``<%= form_authenticity_token%`

Fd Rails 为我们提供了这个令牌，我们只需要在这里实现它

如果你做的一切都正确，你的表格应该提交。当您单击 submit 按钮时，您可以通过查看终端输出来判断。它应该是这样的:

```
Started POST "/users" for 127.0.0.1 at 2021-04-06 12:37:59 -0400
Processing by UsersController#create as HTML
  Parameters: {"username"=>"bryan", "email"=>"bryangoertz@gmail.com", "password"=>"[FILTERED]", "authenticity_token"=>"[FILTERED]"}
No template found for UsersController#create, rendering head :no_content
Completed 204 No Content in 1ms (Allocations: 325)
```

酷，它有我们所有提交的信息！太棒了，但是等等,“找不到模板”是什么问题？

我们成功提交了表单，但是我们没有告诉 Rails 之后会发生什么。

我们有了一个新用户，但是我们还没有将它保存到数据库中。让我们建立`#create`行动来为我们的下一步做准备:

1.  转到`app/controllers`目录中的`UsersController`，在那里我们有`#create`和`#new`的两个空白方法
2.  是时候给我们的 create 方法添加一点逻辑了。目标是成功的用户创建重定向回新的用户表单，空白并准备好另一个新的用户创建。如果它由于某种原因失败了，我们想用现有的信息重新呈现同一个`:new`表单——有一个错误，我们可以稍后构建错误消息。
3.  我们的`users_controller`现在应该是这样的

```
# app/controller/user_controller.rb 
def new
end

def create 
  @user = User.new(username: params[:username], email: params[:email], password: params[:password])

  if @user.save
    redirect_to new_user_path
  else
    render: :new
  end
end
```

如果一切都做得正确，我们的服务器终端应该输出一些关于某种`INSERT INTO "users" ...`之类的信息。那就好！这是一个将数据插入表中的 SQL 命令。

1.  让我们简化一下`create`方法中的 params 参数——这有点难以控制。如果我们使用帮助器方法`form_with`，我们会为用户参数存储一个属性散列
    *这就是表单中* `*name*` *属性发挥作用的地方*
2.  使用硬括号将用户字段(用户名、电子邮件、密码)*嵌套在* `*name*` *属性内，例如`name="user[email]"`*
3.  现在就做。转到您的`app/views/new.html.erb`文件，并适当地更改每个字段的表单`name`属性。完成后，它应该是这样的:

```
<form action='/users' method='post' accept-charset="UTF-8">
  <label for="username">Username</label>
  <input type="text" id="username" name="user[username]"> <br>

  <label for="email">Email Address</label>
  <input type="text" id="email" name="user[email]" > <br>

  <label for="password">Password</label>
  <input type="password" id="password" name="user[password]"> <br>

  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token%>" >

  <input type="submit" value="Submit">

</form>
```

好极了。现在让我们往回走一点。

1.  回到您的`app/controllers/users_controller.rb`中，在 create 方法中注释掉您的新用户行。它以用户实例变量`@user ...`开始，并包含了我们所有的个体变量`params`。
2.  为我们的参数创建一个`private`方法。保安！！
    转到文件底部，就在最后一个`end`的*上面，添加下面的代码来完成我们刚刚注释掉的行的工作，但是安全地使用`permit`和`require`:*

```
...
  private

  def user_params
 params.require(:user).permit(:username, :email, :password)
  end
end
```

现在我们需要告诉我们的`:new`方法在哪里可以找到`private`区域中的参数。我们通过将新的私有方法传递给`new`方法来实现，在我们注释掉它们之前，我们的参数曾经住在这个方法中。

1.  转到上面的注释行，更改为:
2.  @user = User.new(user_params)
3.  整个`users_controller.rb`现在看起来像这样:

```
class UsersController < ApplicationController
  def new
  end

  def create
	@user = User.new(user_params)

	if @user.save
	  redirect_to new_user_path
	else
	  render :new
	end 
  end

  private

  def user_params
	params.require(:user).permit(:username, :email, :password)
  end
end
```

好的..这基本上就是我们所需要的。继续向数据库提交一个新用户，并注意输出。

我不知道你是否注意到了，但之前当我们提交用户数据时，它吐回了我们所有的信息，就像是在赠送免费披萨一样。你的名字、电子邮件和密码被暴露给任何人偷看，让你变得脆弱、害怕，并可能成为一些恶意活动的受害者。

现在，随着我们的私有方法被传递到我们的`:new`方法中，我们的信息被隐藏并且是安全的。这就是我们想要的！

关于我们刚刚学到的所有东西，最好的部分是我们应该知道它，但是如果我们选择使用`form_with` helper 方法，我们真的不需要把它都写出来。

请继续关注关于在 Rails 表单中使用助手方法的指南。他们非常有趣。

## TL；博士，如果您只需要这些步骤，这里有一个我们在上面所做工作的快速步骤列表:

第一部分

1.  `rails new formidable`
2.  启动 git 并开始控制您的版本
3.  移动到您的项目文件夹`cd formidable`
4.  在 VS 代码中打开`code .`

第二部分—创建用户模型和表单验证

*   在终端中，使用命令`rails g model User username:string email:string password:string`生成一个新的数据库模型
*   导航到刚刚创建的迁移文件，确保它创建正确— `db/migrations/crazynumberfile`如果正确，继续。如果不是，请将其删除并重试。
*   运行`rails db:migrate`
*   导航到`app/models/users.rb`并添加您的验证。在红宝石`class`里面加上`validates :username, :email, :password, presence: true`

第三部分—生成控制器并添加路由资源

*   将我们的方法添加到 UserController 中。我们只是为该指南创建一个用户，所以我们将添加`#new`和`#create`操作。在`app/controllers/users_controller.rb` -它们将暂时空着。
*   您的`users_controller.rb`文件应该是这样的

```
class UserController < ApplicationController
	def new
	end

	def create
	end
end
```

*   当发出`new`或`create`请求时，告诉 rails 去哪里:在您的`config/routes.rb`中添加我们想要授予用户对象的资源。

```
Rails.application.routes.draw do 
		resources :users, only: [:new, :create]
	end
```

第四部分——视图和表单时间

*   在`app/views/users`目录中创建一个名为`new.html.erb`的文件
*   添加一个`<h1>`或其他内容来确保你的页面被渲染。
*   启动您的服务器— `rails s`
*   将浏览器导航到您的“新”页面`localhost:3000/users/new`，您应该会看到您新创建的测试内容
*   为我们的 3 个数据库/模型项目— `username`、`email`和`password`—创建带有字段和标签的表单，确保为您的表单提供一个动作(路径)和方法(用于提交我们使用 POST 的表单)。
*   添加一个 rails 认证令牌，使用 Rails 提供的`<%= form_authenticity_token %>`为我们的表单建立安全性——我们把它放在一个隐藏的输入中。*参见本节后的完整代码块以供参考。*
*   因为我们的控制器接管了部分表单提交过程，我们需要为我们的字段赋予适当的`name`属性，包括我们的型号名称和适当的属性，例如`name="user[email]"`。对所有需要名称的表单域执行此操作。`authenticity_token`和`submit`输入不会遵循这种格式。
*   你的完整表格应该是这样的:

```
<h1>This App is Formidable! </h1>

<form action='/users' method='post' accept-charset="UTF-8">
  <label for="username">Username</label>
  <input type="text" id="username" name="user[username]"> <br>

  <label for="email">Email Address</label>
  <input type="text" id="email" name="user[email]" > <br>

  <label for="password">Password</label>
  <input type="password" id="password" name="user[password]"> <br>

  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token%>" >

  <input type="submit" value="Submit">

</form>
```

*   转到`app/controllers`目录中的`UsersController`,在那里我们有两个用于`#create`和`#new`的空白方法
*   我们希望使用`@user = User.new`创建用户实例，后跟我们正在寻找的参数，但我们需要保持我们的数据私有。因此，与其在`@user = User.new`之后的`create`方法中直接传入我们的参数，不如在最后一个`end`之上添加一个`private`方法，如下所示:

```
...
  private

  def user_params
 params.require(:user).permit(:username, :email, :password)
  end
end
```

*   我们现在可以将这个`user_params`方法传递给上面的`#create`动作，结果是我们的`params`(也就是我们允许的)保持安全。`users_controller.rb`将看起来像这样:

```
class UsersController < ApplicationController
  def new
  end

  def create
	@user = User.new(user_params)

	if @user.save
	  redirect_to new_user_path
	else
	  render :new
	end 
  end

  private
  def user_params
	params.require(:user).permit(:username, :email, :password)
  end
end
```

*   注意，我们添加了一些条件逻辑，以便在成功或出错时重定向到适当的页面。

这是简短的版本。如果你在那里卡住了，看看我在哪里更详细地经历这个过程。

派对！