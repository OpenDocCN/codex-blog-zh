# Django 的 7 个快速有用的技术

> 原文：<https://medium.com/codex/7-quick-useful-techniques-in-django-29fc201aea78?source=collection_archive---------0----------------------->

## 让我们看看 django 中的 7 个快速有用的技术，它们可以帮助我们节省时间！！！！

![](img/a10a56bedb71ae92e8a680724fd9a3ff.png)

Tatiana Rodriguez 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正如在官方网站上所说，django 是一个为有截止日期的**完美主义者设计的 web 框架，不深入陈述，Django 是一个用于快速 web 开发的强大 web 框架。说到博客，让我们看看 7 个有用的技术，它们可以帮助我们解决一些与 django 相关的问题，有时还可以节省时间。**

以下是这些技术将涉及的类别:

1.  查询集上的过滤器
2.  用户相关的对象创建
3.  对象引用的不同方式

## 好了，现在让我们深入研究一下技术😃,

# 1.从输入值列表中筛选:

1.  我们看到，当给定一系列输入参数时，我们可以如何过滤对象:

假设我们的数据库中有用户模型和各自的用户对象。我们可以从内置的 django auth 模型中导入用户模型，如下所示:

```
from django.contrib.auth.models import User
```

现在，假设我们得到一个值为的**输入列表，需要根据该列表来过滤用户，例如，我们可能希望根据一个包含**用户 id**或用户名或名字或其他信息的列表来过滤**用户**，为简单起见，假设我们有一个包含**活动用户**的列表，如下所示:**

```
#list of active users.
active_users_list = [1,2,55,21,63,76,88]
```

我们只想要具有这些用户 id 的用户对象(它们是活动的用户对象)，这可以使用以下方法来实现:

```
#list of active users.
**active_users_list** = [1,2,55,21,63,76,88]
# we are interested in filtering only the active users from all the users, so we can do the following:
**active_users** = User.objects.filter(id__in=active_users_list)
```

因此 **active_users** 根据输入过滤器给出一个活动用户的查询集。因此，要获得值列表的通用过滤方法，可以如下所示

```
#Model Posts be the a dummy model with name, user etc
value_list = [val1, val2, val3, val4]    #input list
filtered_value = Posts.objects.filter(Field__in=value_list)
```

# 2.常见问题— F 和 Q 表达式，查询集上的过滤器:

F 表达式，假设我们有一个情况，需要比较一个模型上一个字段的值和同一个模型中另一个字段的值，那么 F 表达式就发挥作用了。

```
# We can import F expressions as following
from django.db.models import **F**
```

假设有社交媒体应用并且有帖子，让我们如下定义帖子的模型:

```
class post(models.Model):
    image = imageField() ##dummy one
    likes = int field
    shares = int field
```

我们可以定义一个简单的条件，比如如果**的点赞数**多于**的分享数**或者类似的东西，就可以制作一个帖子**的趋势**。要做到这一点我们可以用 F 表达式。

```
# we have already imported F expression, to get the posts which are trending, we have the following filter:
**trending_post** = posts.objects.filter(likes__gt=F('shares')))
```

# 3.来自单个对象(一个对象)的外键(其他表)对象引用(多对一关系):

为了定义**多对一关系**，我们在 django 模型中使用**外键**，这意味着一个表中的一个条目与数据库中的多个表相关。通过外键访问相关元素可以有多种方式，其中一种方式是自己调用模型，用不同的参数过滤所需的对象。另一个简单的方法是直接从作为外键的模型中调用。

让我们通过一个例子来看看，假设我们有模型**博客，**的描述，标题和作者字段。一般来说，我们看到每个用户有不止一个博客，所以我们可以将**用户**定义为博客模型的外键。假设我们已经像以前一样导入了用户模型，我们可以如下定义博客模型:

```
from django.contrib.auth.models import User
from django.db import modelsclass blog(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField(null=True, blank=True)
    user = models.ForeignKey(User, related_name='blogs',        on_delete=models.CASCADE)
```

用户是博客模型的外键，因为一个用户可以写多个博客。因此，让我们看看如何引用一个用户的博客可以做到这一点。我们可以用两种方法来做，一种方法是过滤所有有用户的博客，如下所示:

```
from .models import blogdef user_blogs(user):
    user_blogs = blog.objects.filter(user=user)
    return user_blogs
```

第二种方法是使用博客模型中提供的相关名称直接从用户本身引用用户博客。

```
#assuming user is user object of some user
.... code before
user = User.objects.get(pk=1)   # assuming user object with 1 exits.
user_blogs = user.blogs.all()   #gives all the blog object
```

外键对象的引用在我们可以细化与用户相关的对象的访问的情况下会很有用。例如，如上所示，我们可以尝试删除对象是否属于用户的检查，例如用户的博客(user_id = 1)只被该用户访问，其他人不能直接访问。

# 4.对查询集的 Or 运算:

我们经常需要根据不同的标准过滤查询集，然后对查询集进行逻辑运算。例如，假设我们有一个需求，要求所有的帖子都有一个特定的“标题”,或者赞数大于某个数字，那么我们可以简单地首先根据标题过滤我们的模型对象，然后根据赞数过滤另一个模型对象，并对其进行 or 运算。

```
from django.contrib.auth.models import User
from django.db import modelsclass blog(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField(null=True, blank=True)
    user = models.ForeignKey(User, related_name='blogs',        on_delete=models.CASCADE)
    likes = models.ForeignKey(likes, related_name='blogs',                 on_delete=models.CASCADE)class likes(models.Model):
    count = models.IntegerField()

#Suppose blog has title as filed, and likes as foreign key then,
Total_blogs_with_title="title"_or_has_likes>500 can be done as following:from django.db.models import Max, Q
blogs_with_title = blog.objects.filter(title="title")
blogs_with_count = blog.objects.filter(Q(likes_count>500))
Total_blogs = (blogs_with_title | blogs_with_count).distinct()
```

我们可以将“**和“**操作包含在过滤器本身中，如下所示:

```
Total_blogs= blog.objects.filter(title="title", Q(likes_count>500))
```

# 5.django 的用户创建:

django 最好的一点是用户管理已经内置在它的系统中，我们可以扩展它或者使用现有的。通常，当创建模型对象时，我们倾向于使用 nameofmodel.object.create(参数)。但是到了用户模型就没什么不同了，我们需要用户的 create_user 方法而不是 create()方法。更多细节我们可以参考[stack overflow 上的这个](https://stackoverflow.com/questions/11544398/user-manager-methods-create-and-create-user)回答。

因此，要创建新用户，我们可以采用以下方式:

```
from django.contrib.auth.models import Usercredentials = {'usernam': 'name_of_the_user',
               'password': 'password_provided',
               'email': 'mailid.provided'.}
User.objects.create_user(**credentials)
```

# 6.使用 filter 和 first()代替 get:

通常我们倾向于使用。get()方法来获取一个对象，我可以观察到的是，我们使用 model.object.get(id=id)或其他一些参数来获取一个特定的对象。但是在某些情况下或者在编写单元测试时，当我们倾向于通过 get 方法或者索引来访问元素时。如果有元素存在，那么我们不会有任何问题，但如果元素被偶然删除，或者在稍后阶段，当我们试图通过它们的 id 或任何其他字段访问元素时，我们的程序可能会崩溃。为了避免这种情况，并有一个故障安全机制，我们可以使用过滤器和第一方法。我们可以这样做:

```
#using get method. assuming user with id-6 exists
user = User.objects.get(pk=6)#using filter and first methods
user = User.objects.filter(pk=6).first()
```

在第一种情况下，如果碰巧 id 为 6 的用户对象不存在，我们会得到一个错误，说没有 id 为 6 的对象，但在第二种情况下，过滤器会给出一个空的查询集，第一个方法不会给出任何值，也不会使我们的程序崩溃。

# 7.虚假迁移:

当数据已经存在于表中，但是看起来好像没有应用迁移时，假迁移就开始起作用了。我们可以看到使用以下命令应用的迁移

```
python manage.py showmigrations
```

当数据库在开发人员之间共享时，或者当数据库转储存在并且想要填充我们的 db 时，这可能会发生，当我们假装迁移时，数据库模式没有发生变化，但是在数据库中创建了一个条目。

我们给 migrate 命令一个 **—假的**标志。

```
python .\manage.py migrate --fake #fake all the migrations.
```

当我们因为表已经存在于数据库中而不能应用初始迁移时，伪初始是使用的另一个标志。在这种情况下，我们可以在 migrate 命令中使用 **— fake-initial** 标志来伪造初始迁移。

django 中还有许多其他有用的命令，可以节省时间并提供解决问题的方法，如 get_object_or_404、transaction.atomic 等，这些命令非常有用。感谢您的耐心阅读。希望可能有用。再次感谢。😄

再次感谢你阅读这篇文章，如果你想支持我，你可以给[我买杯咖啡](https://www.buymeacoffee.com/gautham.dasu)，这让我有动力提高自己的技能，并提供尽可能好的内容。