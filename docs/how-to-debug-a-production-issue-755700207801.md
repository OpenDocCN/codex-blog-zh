# 如何调试生产问题？

> 原文：<https://medium.com/codex/how-to-debug-a-production-issue-755700207801?source=collection_archive---------9----------------------->

![](img/4e73bb80b11df6ff3f6bc51eb0b2fd0c.png)

每个从事技术工作的开发人员和管理人员都面临着一个只有在生产环境中才会发生的问题。这种类型的问题是最难调试的，因为您无法轻松地检查日志或进行一些常规测试。

在过去的几年里，我负责在生产中交付不同的产品，并多次面临这种问题。在这篇文章中，我试图分享我的经验，就如何修复它们。

# 找到范围

首先，看一下错误报告，并尝试对问题做出一些猜测。是因为代码的原因还是与服务器有关的原因？会不会和直播系统上的数据有关？

尝试找到影响这些请求的代码行，并一步一步写下请求经过的路径。

# 确保

下一步是确保问题只发生在活动系统上。有几次我收到了一个实时问题的报告，但它也发生在临时服务器上。在这些场景中，您可以很容易地调试临时服务器，并且不会在活动系统上冒任何风险。

# 检查差异

我总是检查最新版本或找到导致问题的版本。检查整个代码，并记录可疑的代码行。有时你会发现一个没人注意的新手错误。

# 试运行与生产

确保试运行和生产设置之间的区别。在生产中，您可能已经扩展了您的项目，拥有不同的配置、多个数据库、不同的缓存策略等等。记下不同之处，以便有一个更好的概述。

# 生命周期

总有一个或几个请求会产生 bug，记录下来，检查每个请求的生命周期。确保请求按照你想的方式进行。这可能会浪费您的时间，因为请求会经过其他代码行。例如，我们在实时系统上发布了一个版本，但是由于命名的原因，一些请求失败了。开发人员添加了一个同名的函数，并且存在冲突。我们更改了函数名，一切恢复正常。

# 缩小范围

一个一个地测试和调试所有的东西，尽可能地缩小你自己的范围。如果你一起改变多个东西，就很难找出哪个是问题所在。

## 贮藏

有时，实时系统上的缓存配置会产生问题。确保所需的缓存已经更新。

## 负载平衡

在一个大型项目中，您在多个服务器之间对请求进行负载平衡，您可能有一个损坏的节点。确保所有节点都已更新，并正确获取请求。

# 一切皆有可能

问题并不总是最难的，一切皆有可能。记住创建不同的测试用例来涵盖每一种可能性。逐一检查您的测试用例，并尝试为其创建最小的范围。

# 有意义的日志消息

当您添加要调试的日志时，记得在日志中有一个清晰的消息。更容易找到它们，而且您也不需要再次检查代码来查看日志在哪里。

这是类别的索引操作。这里没有显示一些类别，我们想找出原因。

```
def index
      city_id = params[:city_id]
      city = Category.city(city_id)&.first
      return head 400 unless city.present?

      cache_key = "categories_#{city_id}"
      expires_in = Rails.application.config.api_cache_lengths.soon cats = Rails.cache.fetch(cache_key, expires_in: expires_in) do
        global_cats = Category.get_global_categories
        cats = []
        if city.present?
          city_cats = Category.get_categories(city) || []
        end
        cats += city_cats
        cats += global_cats
      end 
      cats = Category.remove_no_deals(cats)
      cats = Category.categories_sort_by_position(cats) categories_serializer = CategorySerializer.new(cats) render json: categories_serializer.serializable_hash
    end
```

现在，我将添加一些日志来查看代码是如何工作的，并了解是哪一行代码产生了问题。

```
def index
      Rails.logger.info "Index action started .."
      city_id = params[:city_id]
      Rails.logger.info "City id: #{city_id}"
      city = Category.city(city_id)&.first
      return head 400 unless city.present?
      Rails.logger.info "The city was present"

      cache_key = "categories_#{city_id}"
      expires_in = Rails.application.config.api_cache_lengths.soonRails.logger.info "Cache Key: #{cache_key}"

      cats = Rails.cache.fetch(cache_key, expires_in: expires_in) do
        global_cats = Category.get_global_categories
        Rails.logger.info "Global categories count: #{global_cats.count}"
        cats = []
        if city.present?
          city_cats = Category.get_categories(city) || []
        end
        cats += city_cats
        cats += global_cats
        Rails.logger.info "Final cached categories count: #{cats.count}"
      end 
      cats = Category.remove_no_deals(cats)
      Rails.logger.info "Categories after removing no deals count: #{cats.count}"
      cats = Category.categories_sort_by_position(cats)
      Rails.logger.info "Categories after sorting: #{cats.count}"categories_serializer = CategorySerializer.new(cats)
      Rails.logger.info "Response should be: #{categories_serializer.serializable_hash}"

      render json: categories_serializer.serializable_hash
    end
```

现在你可以看到类别从列表中被删除，并找出原因。，去检查那一行代码。

你应该知道这类问题没有正确的答案。总是与他人分享你的经验，也从同事那里得到帮助。