# Ruby on Rails 以及如何对抗嵌套资源 Boss

> 原文：<https://medium.com/codex/ruby-on-rails-and-how-to-fight-the-nested-resources-boss-e63738eadf63?source=collection_archive---------4----------------------->

最初提交于 2020 年 7 月。这篇文章是我的 Ruby on Rails 项目博客。希望对你有帮助和启发。

是的，这就是我给我的 Rails 项目博客起的名字。所以那种感觉，就像到了最后的 boss，过得很艰难，就像一个不错的老视频游戏。

![](img/17a8d2289cb2cfc06e3ac77116bddba6.png)

老谋深算的医生和我不得不选择在他面前面对的每个人都是我童年最大的敌人。

对于 Rails 项目，我决定继续进行小评审项目。以防这是你从我这里读到的第一篇博客，小评论是我现实生活中的电影评论项目。我人生的一个目标是将小评论(取自西班牙语单词*reseita*，我会说*pequea resea*而不是波多黎各俚语中的*resea*)变成一个网站或应用程序，供与我有着相同热情的人使用。

在这个项目中，我决定使用 gems `Devise`创建整个`CRUD`结构用于认证，使用`Omniauth`供脸书用户登录(我决定使用脸书，因为我希望这尽可能接近真实的上线)。有许多宝石可供选择。相应地挑选和使用它们，但是注意不要太贪婪或者对它们上瘾。

![](img/eab53ba20e51148df867292ca2f0f6ba.png)

创建用户并允许他们登录后，我继续添加评论和类别表，开始谈论我们最喜欢的电影，并按类型对它们进行分类。要添加嵌套资源，还有什么比添加注释更好的呢？就像一个作者通过杀死一个角色来逃避懒惰的写作一样，我选择通过添加评论来简化项目。毕竟这就是我们在网上所做的，评论任何事情，对吗？

嗯，嵌套的资源总感觉像迷宫一样。有点吓人，但我会搞定的。首先，让我们看看评论和评论模型。

```
clas Review < ApplicationRecord
    belongs_to :user
    belongs_to :category
    has_many :comments, dependent: :destroy 
    has_many :users, through: :comments
    validates :title, presence: true, length: { in: 3..30 }
    validates :content, presence: true, length: { maximum: 250 }
    default_scope { order(created_at: :desc)}
end
```

```
class Comment < ApplicationRecord
    belongs_to :user
    belongs_to :review
    validates :content, presence: true, length: { maximum: 250 }
end
```

评论将属于用户，属于一个类别，并将有许多评论以及许多用户通过评论。评论将属于写评论的用户，评论也是写的。现在，让我们来看看控制器。

审核控制者:

```
class ReviewsController < ApplicationController
    before_action :set_review, only: [:show, :edit, :update, :destroy]
    before_action :authorize!, only: [:edit, :destroy]

    def index
      @reviews = Review.all
    end

    def new
      @review = Review.new
      @comment = Comment.new
      @comment.review_id = @review.id 
      #We need to declare the comments in the new action. 
    end

    def create
      @review = Review.new(review_params)
      @review.user_id = current_user.id
      if @review.save
        redirect_to review_path(@review)
      else
        render 'new' 
      end
    end

    def show
      @comment = Comment.new 
      #We also need to declare the new comment in the show action. 
    end

    def edit
    end

    def update
      if @review.update(review_params)
        redirect_to review_path(@review)
      else  
        render 'edit'
      end  
    end

    def destroy
      @review.destroy
      redirect_to reviews_path   
    end

    private

      def set_review
        @review = Review.find_by(id: params[:id])
    end 

      def review_params
        params.require(:review).permit(:title, :content, :category_id)
      end

      def authorize! 
        authorize @review #authorize method using the Pundit gem
    end 
end
```

注释控制器:

```
class CommentsController < ApplicationController
    before_action :get_comment, only: [:edit, :update, :destroy]
    before_action :authorize!, only: [:edit, :destroy]
    before_action :set_comments, only: [:edit, :update, :destroy]

    def new
        @comment = Comment.new 
    end 

    def create
        @comment = Comment.new(comment_params)
        @comment.user_id = current_user.id
        @comment.review_id = params[:review_id]
        #Declare the review id. 
        @comment.save
        redirect_to review_path(@comment.review)
    end

    def edit
    end 

    def update
        @comment.update(comment_params)
        redirect_to review_path(@review)
    end 

    def destroy
        @comment.destroy
        redirect_to review_path(@review)
    end

    private 

    def get_comment
        @comment = Comment.find_by(id: params[:id])
    end 

    def set_comments
        @review = @comment.review 
        @comments = @review.comments.find_by(id: params[:id])
    end 

    def comment_params
        params.require(:comment).permit(:content, :review_id)
    end 

    def authorize! 
        authorize @comment #authorize method using the Pundit gem
    end 

end
```

现在我们已经完成了控制器，让我们来处理视图中的代码。这就是评论相关的代码在我们的演出回顾文件中的样子。

```
<% if user_signed_in? %>
<%= render partial: 'comments/form' %>
<% end %>
```

如果用户已经登录，他将能够看到从文件`/views/comments/form`提交的评论表。

这是来自那个有用文件的代码。

```
<%= form_for [ @review, @comment ] do |f| %>

    <% if @comment.errors.any? %>
        <div id="error_explanation">
            <h2>
            <%= pluralize(@comment.errors.count, "error") %>
            prohibited this comment from being saved:
            </h2>

        <ul>
            <% @comment.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
            <% end %>
        </ul>
        </div>
    <% end %>

  <p>
    <%= f.label :content, "What Do You Think?" %><br/>
    <%= f.hidden_field :review_id %>
    <%= f.text_area :content %>
  </p>
  <p>
    <%= f.submit 'Submit' %>
  </p>
<% end %>
```

如您所见，该表单正在迭代错误和所有内容。相当酷。

既然有了形式，那就给那些激情的电影鉴赏家们看看评论吧。

既然有了形式，那就给那些激情的电影鉴赏家们看看评论吧。

```
<% if @review.comments.present? %>
   <h5>Comments:</h5>
   <%= render partial: 'reviews/comment', collection: @review.comments %>
<% end %>
```

在这里，我们声明如果评论被创建，它们将被显示。我们不希望评论页面看起来很丑，带有**评论:**标题，什么也没有。所以我们从 views/reviews 目录中的部分文件进行渲染。让我们看看我们是如何迭代来自该文件的数据的。

```
<div>
  <p><strong><%= comment.user.username %></strong><br>
  <%= comment.content %></p>
  <% if user_signed_in? && (current_user.wrote_this(comment) ||     current_user.admin) %>
  <%= link_to 'edit', edit_review_comment_path(@review, comment) %> 
  <%= link_to 'delete', comment_path(comment), method: :delete, data: { confirm: 'Are you sure?' } %>
  <% end %>
</div>
```

我最后说的是另一种说法。如果用户已经登录，并且是评论的作者或管理员，用户将有权编辑和删除评论。我的一个好朋友向我展示了如何在`User`模型中创建那个方法，我将通过展示给你来回报它。

```
def wrote_this(obj)
  if obj.class == Review && obj.user_id == self.id 
    true
  elsif obj.class == Comment && obj.user_id == self.id 
    true
  else
    false
  end
end
```

很整洁，是吧？最后，让我们做创建嵌套资源时最重要的事情。使资源…嵌套的一件事。让我们转到`routes`文件并添加以下内容。

```
resources :reviews do
  resources :comments
end
```

你甚至可以通过添加`only:[:show, :new, :create, :edit, :update, :destroy]`来修改它，无论你喜欢什么或者你的项目需要什么来实现预期的功能。

如你所见，有很多步骤。然而，结果将是值得的，完成它将是一次令人敬畏的经历。记得用流程图或者甚至在白板上预先计划你的模型和你希望它们交互的方式。[规划是任何项目的一部分](https://vocal.media/journal/five-phases-of-the-project-management-lifecycle)。

一行一行地编码，你终于可以打败老板了。不能保证公主会在城堡里。

![](img/d81db186bf10010a430ce57676b27e13.png)

# 总结:

1.  Ruby on Rails 项目规划。
2.  嵌套资源。
3.  模特。
4.  控制器。
5.  使用 ERB 代码的视图。
6.  路线。