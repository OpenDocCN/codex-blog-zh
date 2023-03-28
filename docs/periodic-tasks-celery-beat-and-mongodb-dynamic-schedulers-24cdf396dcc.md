# 周期性任务— Celery beat 和 MongoDB 动态调度程序

> 原文：<https://medium.com/codex/periodic-tasks-celery-beat-and-mongodb-dynamic-schedulers-24cdf396dcc?source=collection_archive---------2----------------------->

![](img/59a32d960de867c85ffa3f1b48889a35.png)

对于大多数项目，将有一个场景来卸载长时间运行或 CPU 密集型任务。此外，还需要实现一些批处理或周期性任务。

对于这种需求，有助于实时处理数据的分布式任务队列系统是合适的。

在 python 世界[芹菜分布式任务队列](https://docs.celeryproject.org/en/stable/index.html)提供了这样的解决方案

> “Celery 是一个简单、灵活、可靠的分布式系统，可以处理大量的消息，同时为运营部门提供维护这样一个系统所需的工具。
> 
> 它是一个任务队列，侧重于实时处理，同时也支持任务调度。"

芹菜工程生产者，经纪人和消费者模式。生产者创建任务并将其推给代理，然后消费者(工作者)将任务出队并执行它。

`pip install celery`

```
#create the file tasks.pyfrom celery import Celery//using rabbitmq as the message broker
app = Celery('name',broker='amqp://guest@localhost')@app.task
def hello():
  return 'hello world'
```

**来自[https://docs . celery project . org/en/stable/getting-started/introduction . html # get-started](https://docs.celeryproject.org/en/stable/getting-started/introduction.html#get-started)的示例代码

您可以运行芹菜工人如下

`celery -A tasks worker --loglevel=DEBUG`

现在我们对芹菜如何执行任务有了一点了解，让我们进入我们的博客主题，讨论芹菜的任务调度器**击败**

Celery 通过 [Celery Beat](https://docs.celeryproject.org/en/stable/reference/celery.beat.html#module-celery.beat) 支持任务调度，支持基于特定周期(时间间隔或 crontab 调度)的任务执行。

定期任务可以通过“beat_schedule”选项添加到现有的 celery 应用程序配置中

```
app = Celery('name',broker='amqp://guest@localhost')
app.conf.beat_schedule = {
  'call-every-30-seconds': {
    'task': 'tasks.hello',
    'schedule': 30.0 #time-interval type
   }
}
```

芹菜节拍可以使用下面的命令运行。**启动节拍服务**前，确保芹菜工也在运行。

`celery -A tasks beat --loglevel=DEBUG`

在执行上面的命令之后，您可以在 celery beat 的日志中看到，它每 30 秒对任务进行一次排队，在 celery worker 的日志中，您可以看到 worker 在相同的时间段内执行任务。

celery beat 使用默认调度器*celery . beat . persistent scheduler*，它使用 shelve 数据库文件来存储调度条目及其状态。

这种默认设置的问题是，很难动态地管理任务，例如添加或修改与任务相关的执行配置。

如果,**可以将**这些日程相关信息存储在数据库中，并且**可以动态添加或修改**日程相关信息。

是的，这可以通过使用定制的调度程序来实现，如 [celerybeat-mongo](https://github.com/zmap/celerybeat-mongo) ，它使用 mongodb 作为数据库来存储和管理调度及其状态信息。现在让我们详细看看如何实现它。

`pip install celerybeat-mongo mongoengine`

我们将使用 mongoengine 操作 celerybeat-mongo 的模型来创建和修改时间表。为此，我们将使用来自 celerybeat-mongo 的 PeriodicTasks 类。

让我们创建一个定制类并导入所需的模块。

只完成了基本的创建和更新操作，但是可以扩展这个类来进行自定义验证和实现

默认情况下，这将创建/修改名为“schedules”的集合中的文档。如他们的[设置文档](https://github.com/zmap/celerybeat-mongo#settings)中所述，当它与芹菜应用程序集成时，这个收集选项可以改变。

让我们用一些样本数据集调用如下定义的自定义类。

现在我们有了一些类，它们在 celerybeat-mongo lib 的 PeriodicTask 模型上执行 crud 操作。让我们开始与 celery 应用程序集成配置，例如为 celery beat 提供自定义集合，以同步和拾取由我们的“CustomPeriodicTaskManage”类管理的日程。

```
config = {
    "mongodb_scheduler_db": "my_project",
    "mongodb_scheduler_url": "mongodb://localhost:27017",
    "mongodb_scheduler_collection": "custom_schedule_collection" 
}
app = Celery('name',broker='amqp://guest@localhost')
app.conf.beat_schedule = {
  'call-every-30-seconds': {
    'task': 'tasks.hello',
    'schedule': 30.0 #time-interval type
   }
}
app.conf.update(**config)
```

让我们使用下面的命令运行芹菜工和 beat

```
celery -A tasks worker --loglevel=DEBUG
celery -A tasks beat -S celerybeatmongo.schedulers.MongoScheduler --loglevel=DEBUG
```

在 celery beat 服务的日志中，您可以看到它将后端指向上面定义的 mongodb 配置。还显示了它根据计划选择任务并对任务进行排队。

Celery worker 服务日志显示它将任务出队并执行它。

现在，您可以使用“CustomPeriodicTaskManage”类更新时间表，并看到 celery beat 动态地选择更改并运行它，而无需重新启动服务。

太好了，现在我们可以通过将时间表存储在数据库中来管理它。我们还可以动态地更改时间表(interval 或 crontab ),允许 celery beat 服务拾取更改并执行它，而无需重新启动服务。

希望这篇文章是有用的，并解决了与数据库后端运行芹菜节拍的要求。