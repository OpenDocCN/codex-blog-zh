# ShardingSphere 的 Show processlist & Kill 是如何工作的？

> 原文：<https://medium.com/codex/how-does-shardingspheres-show-processlist-kill-work-6bfad30b6687?source=collection_archive---------18----------------------->

对于经常使用数据库的人来说，您可能想知道:

1.如何检查数据库当前正在执行什么 SQL，以及处于什么状态？

2.如何终止异常 SQL？例如，如果一个用于查询包含大量数据的表的`SELECT`语句不包含查询条件，它将会降低整个数据库的性能。这可能会促使想要终止这个异常的 SQL。

针对以上问题， [Apache ShardingSphere](https://shardingsphere.apache.org/) 引入了`Show processlist`、`Kill <processID>`等功能。

![](img/0f1658df9892dcd2e0cdb470d8d0ee9f.png)

## 1.介绍

`Show processlist`:该命令可以显示 ShardingSphere 当前正在执行的 SQL 列表以及每条 SQL 的执行进度。如果 ShardingSphere 是以集群模式部署的，`Show processlist`函数会聚合集群中所有代理实例运行的 SQL，然后显示结果，这样您就可以随时看到当时所有正在运行的 SQL。

```
mysql> show processlist \G;
*************************** 1\. row ***************************
     Id: 82a67f254959e0a0807a00f3cd695d87
   User: root
   Host: 10.200.79.156
     db: root
Command: Execute
   Time: 19
  State: Executing 0/1
   Info: update t_order set version = 456
1 row in set (0.24 sec)
```

`Kill <processID>`:该命令基于`Show processlist`执行，可以终止`Show processlist`中列出的正在运行的 SQL。

```
mysql> kill 82a67f254959e0a0807a00f3cd695d87;
Query OK, 0 rows affected (0.17 sec)
```

## 2.它们是如何工作的？

现在您已经了解了`Show processlist`和`Kill <processID>`的功能，让我们来看看这两个命令是如何工作的。由于`Kill <processID>`背后的工作原理与`Show processlist`相似，我们将重点解释`Show processlist`。

## 2.1 SQL 是如何保存和销毁的？

在 ShardingSphere 中执行的每个 SQL 都将生成一个`ExecutionGroupContext`对象。该对象包含关于该 SQL 的所有信息，其中有一个`executionID`字段确保其唯一性。

当 ShardingSphere 接收到 SQL 命令时，调用`GovernanceExecuteProcessReporter# report`将`ExecutionGroupContext`信息存储到`ConcurrentHashMap` 的缓存中(目前只支持 MySQL 的 DML 和 DDL 语句；以后的版本将支持其他类型的数据库。查询语句也归入 DML)。

```
public final class GovernanceExecuteProcessReporter implements ExecuteProcessReporter {

    @Override
    public void report(final QueryContext queryContext, final ExecutionGroupContext<? extends SQLExecutionUnit> executionGroupContext,
                       final ExecuteProcessConstants constants, final EventBusContext eventBusContext) {
        ExecuteProcessContext executeProcessContext = new ExecuteProcessContext(queryContext.getSql(), executionGroupContext, constants);
        ShowProcessListManager.getInstance().putProcessContext(executeProcessContext.getExecutionID(), executeProcessContext);
        ShowProcessListManager.getInstance().putProcessStatement(executeProcessContext.getExecutionID(), executeProcessContext.getProcessStatements());
    }
}@NoArgsConstructor(access = AccessLevel.PRIVATE)
public final class ShowProcessListManager {

    private static final ShowProcessListManager INSTANCE = new ShowProcessListManager();

    @Getter
    private final Map<String, ExecuteProcessContext> processContexts = new ConcurrentHashMap<>();

    @Getter
    private final Map<String, Collection<Statement>> processStatements = new ConcurrentHashMap<>();

    public static ShowProcessListManager getInstance() {
        return INSTANCE;
    }

    public void putProcessContext(final String executionId, final ExecuteProcessContext processContext) {
        processContexts.put(executionId, processContext);
    }

    public void putProcessStatement(final String executionId, final Collection<Statement> statements) {
        if (statements.isEmpty()) {
            return;
        }
        processStatements.put(executionId, statements);
    }
}
```

如上图所示，`ShowProcessListManager`类有两个缓存映射，分别是`processContexts`和`processStatements`。前者存储了`executionID`和`ExecuteProcessContext`之间的映射。

后者包含`executionID`和`Statement objects`之间的映射，这可能会在 SQL 被覆盖后生成多个语句。

每次 ShardingSphere 接收到一个 SQL 语句，SQL 信息就会被缓存到这两个映射中。SQL 执行后，地图的缓存将被删除。

```
@RequiredArgsConstructor
public final class ProxyJDBCExecutor {

    private final String type;

    private final ConnectionSession connectionSession;

    private final JDBCDatabaseCommunicationEngine databaseCommunicationEngine;

    private final JDBCExecutor jdbcExecutor;

    public List<ExecuteResult> execute(final QueryContext queryContext, final ExecutionGroupContext<JDBCExecutionUnit> executionGroupContext,
                                       final boolean isReturnGeneratedKeys, final boolean isExceptionThrown) throws SQLException {
        try {
            MetaDataContexts metaDataContexts = ProxyContext.getInstance().getContextManager().getMetaDataContexts();
            EventBusContext eventBusContext = ProxyContext.getInstance().getContextManager().getInstanceContext().getEventBusContext();
            ShardingSphereDatabase database = metaDataContexts.getMetaData().getDatabase(connectionSession.getDatabaseName());
            DatabaseType protocolType = database.getProtocolType();
            DatabaseType databaseType = database.getResource().getDatabaseType();
            ExecuteProcessEngine.initialize(queryContext, executionGroupContext, eventBusContext);
            SQLStatementContext<?> context = queryContext.getSqlStatementContext();
            List<ExecuteResult> result = jdbcExecutor.execute(executionGroupContext,
                    ProxyJDBCExecutorCallbackFactory.newInstance(type, protocolType, databaseType, context.getSqlStatement(), databaseCommunicationEngine, isReturnGeneratedKeys, isExceptionThrown,
                            true),
                    ProxyJDBCExecutorCallbackFactory.newInstance(type, protocolType, databaseType, context.getSqlStatement(), databaseCommunicationEngine, isReturnGeneratedKeys, isExceptionThrown,
                            false));
            ExecuteProcessEngine.finish(executionGroupContext.getExecutionID(), eventBusContext);
            return result;
        } finally {
            ExecuteProcessEngine.clean();
        }
    }
```

如上所示，`ExecuteProcessEngine.initialize(queryContext, executionGroupContext, eventBusContext);`将把 SQL 信息存储在两个缓存映射中。最后，代码块中的`ExecuteProcessEngine.clean();`将清除缓存中的地图。

`Show processlist`中显示的 SQL 是从`processContexts`中获得的。但是这个地图只是一个本地缓存。如果 ShardingSphere 部署在集群模式下，`Show processlist`如何获取集群中其他机器上运行的 SQL？让我们看看 ShardingSphere 是如何处理的。

## 2.2`Show processlist`是如何工作的？

当 ShardingSphere 接收到`Show process`命令时，它被发送到执行器`ShowProcessListExecutor#execute`进行处理。`getQueryResult()`的实施是重点。

```
public final class ShowProcessListExecutor implements DatabaseAdminQueryExecutor {

    private Collection<String> batchProcessContexts;

    @Getter
    private QueryResultMetaData queryResultMetaData;

    @Getter
    private MergedResult mergedResult;

    public ShowProcessListExecutor() {
        ProxyContext.getInstance().getContextManager().getInstanceContext().getEventBusContext().register(this);
    }

    @Subscribe
    public void receiveProcessListData(final ShowProcessListResponseEvent event) {
        batchProcessContexts = event.getBatchProcessContexts();
    }

    @Override
    public void execute(final ConnectionSession connectionSession) {
        queryResultMetaData = createQueryResultMetaData();
        mergedResult = new TransparentMergedResult(getQueryResult());
    }

    private QueryResult getQueryResult() {
        ProxyContext.getInstance().getContextManager().getInstanceContext().getEventBusContext().post(new ShowProcessListRequestEvent());
        if (null == batchProcessContexts || batchProcessContexts.isEmpty()) {
            return new RawMemoryQueryResult(queryResultMetaData, Collections.emptyList());
        }
        Collection<YamlExecuteProcessContext> processContexts = new LinkedList<>();
        for (String each : batchProcessContexts) {
            processContexts.addAll(YamlEngine.unmarshal(each, BatchYamlExecuteProcessContext.class).getContexts());
        }
        List<MemoryQueryResultDataRow> rows = processContexts.stream().map(processContext -> {
            List<Object> rowValues = new ArrayList<>(8);
            rowValues.add(processContext.getExecutionID());
            rowValues.add(processContext.getUsername());
            rowValues.add(processContext.getHostname());
            rowValues.add(processContext.getDatabaseName());
            rowValues.add("Execute");
            rowValues.add(TimeUnit.MILLISECONDS.toSeconds(System.currentTimeMillis() - processContext.getStartTimeMillis()));
            int processDoneCount = processContext.getUnitStatuses().stream().map(each -> ExecuteProcessConstants.EXECUTE_STATUS_DONE == each.getStatus() ? 1 : 0).reduce(0, Integer::sum);
            String statePrefix = "Executing ";
            rowValues.add(statePrefix + processDoneCount + "/" + processContext.getUnitStatuses().size());
            String sql = processContext.getSql();
            if (null != sql && sql.length() > 100) {
                sql = sql.substring(0, 100);
            }
            rowValues.add(null != sql ? sql : "");
            return new MemoryQueryResultDataRow(rowValues);
        }).collect(Collectors.toList());
        return new RawMemoryQueryResult(queryResultMetaData, rows);
    }

    private QueryResultMetaData createQueryResultMetaData() {
        List<RawQueryResultColumnMetaData> columns = new ArrayList<>();
        columns.add(new RawQueryResultColumnMetaData("", "Id", "Id", Types.VARCHAR, "VARCHAR", 20, 0));
        columns.add(new RawQueryResultColumnMetaData("", "User", "User", Types.VARCHAR, "VARCHAR", 20, 0));
        columns.add(new RawQueryResultColumnMetaData("", "Host", "Host", Types.VARCHAR, "VARCHAR", 64, 0));
        columns.add(new RawQueryResultColumnMetaData("", "db", "db", Types.VARCHAR, "VARCHAR", 64, 0));
        columns.add(new RawQueryResultColumnMetaData("", "Command", "Command", Types.VARCHAR, "VARCHAR", 64, 0));
        columns.add(new RawQueryResultColumnMetaData("", "Time", "Time", Types.VARCHAR, "VARCHAR", 10, 0));
        columns.add(new RawQueryResultColumnMetaData("", "State", "State", Types.VARCHAR, "VARCHAR", 64, 0));
        columns.add(new RawQueryResultColumnMetaData("", "Info", "Info", Types.VARCHAR, "VARCHAR", 120, 0));
        return new RawQueryResultMetaData(columns);
    }
}
```

您将使用`guava`包的`EventBus`函数，它是一个信息发布/订阅数据库，是[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)的一个优雅实现。`EventBus`将类相互解耦，你将在下面找到更多关于它的内容。

`getQueryResult()`法必贴`ShowProcessListRequestEvent`。`ProcessRegistrySubscriber#loadShowProcessListData`使用`@Subscribe`注释来订阅事件。

这个方法是实现`Show processlist`的核心。接下来，我们将介绍这种方法的具体步骤。

```
public final class ProcessRegistrySubscriber {    
    @Subscribe
    public void loadShowProcessListData(final ShowProcessListRequestEvent event) {
        String processListId = new UUID(ThreadLocalRandom.current().nextLong(), ThreadLocalRandom.current().nextLong()).toString().replace("-", "");
        boolean triggerIsComplete = false;
        *// 1\. Obtain the Process List path of all existing proxy nodes in cluster mode*
        Collection<String> triggerPaths = getTriggerPaths(processListId);
        try {
            *// 2\. Iterate through the path and write an empty string to the node, to trigger the node monitoring.*
            triggerPaths.forEach(each -> repository.persist(each, ""));
            *// 3\. Lock and wait 5 seconds for each node to write the information of currently running SQL to the persistence layer.* 
            triggerIsComplete = waitAllNodeDataReady(processListId, triggerPaths);
            *// 4\. Fetch and aggregate the data written by each proxy node from the persistence layer. Then EventBus will post a ShowProcessListResponseEvent command, which means the operation is completed.*
            sendShowProcessList(processListId);
        } finally {
            *// 5\. Delete resources*
            repository.delete(ProcessNode.getProcessListIdPath(processListId));
            if (!triggerIsComplete) {
                triggerPaths.forEach(repository::delete);
            }
        }
    }
}
```

它包括五个步骤，第二和第三步是重点。

## 2.2.1 步骤 2:集群获取数据实现

在这一步中，一个空字符串将被写入节点`/nodes/compute_nodes/process_trigger/<instanceId>:<processlistId>`，这将触发 ShardingSphere 的监控逻辑。

当 ShardingSphere 启动后，持久层会`watch`去监控一系列的路径变化，比如路径`/nodes/compute_nodes`的添加、删除、修改操作。

但是，监控是一个异步过程，主线程不会阻塞，因此需要步骤 3 来锁定并等待每个 ShardingSphere 节点将其当前运行的 SQL 信息写入持久层。

让我们看看 ShardingSphere 是如何处理监控逻辑的。

```
public final class ComputeNodeStateChangedWatcher implements GovernanceWatcher<GovernanceEvent> {

    @Override
    public Collection<String> getWatchingKeys(final String databaseName) {
        return Collections.singleton(ComputeNode.getComputeNodePath());
    }

    @Override
    public Collection<Type> getWatchingTypes() {
        return Arrays.asList(Type.ADDED, Type.UPDATED, Type.DELETED);
    }

    @SuppressWarnings("unchecked")
    @Override
    public Optional<GovernanceEvent> createGovernanceEvent(final DataChangedEvent event) {
        String instanceId = ComputeNode.getInstanceIdByComputeNode(event.getKey());
        if (!Strings.isNullOrEmpty(instanceId)) {
            ...
        } else if (event.getKey().startsWith(ComputeNode.getOnlineInstanceNodePath())) {
            return createInstanceEvent(event);
            *// show processlist*
        } else if (event.getKey().startsWith(ComputeNode.getProcessTriggerNodePatch())) {
            return createShowProcessListTriggerEvent(event);
            *// kill processlistId*
        } else if (event.getKey().startsWith(ComputeNode.getProcessKillNodePatch())) {
            return createKillProcessListIdEvent(event);
        }
        return Optional.empty();
    }

    private Optional<GovernanceEvent> createShowProcessListTriggerEvent(final DataChangedEvent event) {
        Matcher matcher = getShowProcessTriggerMatcher(event);
        if (!matcher.find()) {
            return Optional.empty();
        }
        if (Type.ADDED == event.getType()) {
            return Optional.of(new ShowProcessListTriggerEvent(matcher.group(1), matcher.group(2)));
        }
        if (Type.DELETED == event.getType()) {
            return Optional.of(new ShowProcessListUnitCompleteEvent(matcher.group(2)));
        }
        return Optional.empty();
    }
}
```

`ComputeNodeStateChangedWatcher#createGovernanceEvent`监测到信息后，会根据路径区分创建哪个事件。

如上面的代码所示，这是一个新的节点，所以`ShowProcessListTriggerEvent`将被发布。由于每个 ShardingSphere 实例将监控`/nodes/compute_nodes`，每个实例将处理`ShowProcessListTriggerEvent`。

在这种情况下，单机加工转化为集群加工。让我们看看 ShardingSphere 是如何处理它的。

```
public final class ClusterContextManagerCoordinator { @Subscribe
    public synchronized void triggerShowProcessList(final ShowProcessListTriggerEvent event) {
        if (!event.getInstanceId().equals(contextManager.getInstanceContext().getInstance().getMetaData().getId())) {
            return;
        }
        Collection<ExecuteProcessContext> processContexts = ShowProcessListManager.getInstance().getAllProcessContext();
        if (!processContexts.isEmpty()) {
            registryCenter.getRepository().persist(ProcessNode.getProcessListInstancePath(event.getProcessListId(), event.getInstanceId()),
                    YamlEngine.marshal(new BatchYamlExecuteProcessContext(processContexts)));
        }
        registryCenter.getRepository().delete(ComputeNode.getProcessTriggerInstanceIdNodePath(event.getInstanceId(), event.getProcessListId()));
    }
}
```

`ClusterContextManagerCoordinator#triggerShowProcessList`将订阅`ShowProcessListTriggerEvent`，其中`processContext`数据自行处理。`ShowProcessListManager.getInstance().getAllProcessContext()`检索当前正在运行的`processContext`(这里的数据指的是 ShardingSphere 在每次 SQL 执行前存储在 Map 中的 SQL 信息，在文章开头有描述)并将其传输到持久层。如果`/nodes/compute_nodes/process_trigger/<instanceId>:<processlistId>`节点被删除，则处理完成。

当您删除该节点时，也会触发监控并发布`ShowProcessListUnitCompleteEvent`。这个事件将最终唤醒挂起的锁。

```
public final class ClusterContextManagerCoordinator {

    @Subscribe
    public synchronized void completeUnitShowProcessList(final ShowProcessListUnitCompleteEvent event) {
        ShowProcessListSimpleLock simpleLock = ShowProcessListManager.getInstance().getLocks().get(event.getProcessListId());
        if (null != simpleLock) {
            simpleLock.doNotify();
        }
    }
}
```

## 2.2.2 步骤 3:锁定并等待数据实现

ShardingSphere 使用`isReady(Paths)`方法来确定是否已经处理了所有实例。只有当所有实例都被处理后，它才返回`true`。

数据处理的最长等待时间为 5 秒。如果处理在 5 秒内没有完成，则返回`false`。

```
public final class ClusterContextManagerCoordinator {

    @Subscribe
    public synchronized void completeUnitShowProcessList(final ShowProcessListUnitCompleteEvent event) {
        ShowProcessListSimpleLock simpleLock = ShowProcessListManager.getInstance().getLocks().get(event.getProcessListId());
        if (null != simpleLock) {
            simpleLock.doNotify();
        }
    }
}
```

## 2.2.3 汇总`processList`数据并返回

在每个实例处理完数据后，接收到`Show processlist`命令的实例需要聚合数据，然后显示结果。

```
public final class ProcessRegistrySubscriber {  

    private void sendShowProcessList(final String processListId) {
        List<String> childrenKeys = repository.getChildrenKeys(ProcessNode.getProcessListIdPath(processListId));
        Collection<String> batchProcessContexts = new LinkedList<>();
        for (String each : childrenKeys) {
            batchProcessContexts.add(repository.get(ProcessNode.getProcessListInstancePath(processListId, each)));
        }
        eventBusContext.post(new ShowProcessListResponseEvent(batchProcessContexts));
    }
}
```

`ProcessRegistrySubscriber#sendShowProcessList`会将运行的 SQL 数据聚合到`batchProcessContexts`，然后发布`ShowProcessListResponseEvent`。

该事件将被`ShowProcessListExecutor#receiveProcessListData`消耗，并且`getQueryResult()`方法将继续显示`queryResult`。

至此，我们已经完成了`Show processlist`命令的执行过程。

# 2.3`Kill <processId>`如何工作？

`Kill <processId>`与`Show processlist`有相似的逻辑，那就是将`EventBus`与`watch`机构相结合。

因为我们不知道`processId`属于哪个 SQL，所以也有必要为每个实例添加空节点。

通过`watch`机制，每个 ShardingSphere 实例监视新节点，并检查`processId`键是否在缓存映射中。如果是，获取与该键对应的值。

该值是一个`Collection<Statement>`集合。然后你只需要迭代`Statement`集合，依次调用`statement.cancel()`。底层是被调用来取消 SQL 执行的`java.sql.Statement#cancel()`方法。

## 3.结论

目前，Apache ShardingSphere 只能为 [MySQL](https://www.mysql.com/) 方言实现`Show processlist`和`Kill <processId>`功能。

一旦你了解了它们是如何工作的，如果你感兴趣，欢迎你参与相关功能的开发。我们的社区非常开放，欢迎任何对开源代码感兴趣的人。

## 相关链接:

[Apache ShardingSphere 官网](https://shardingsphere.apache.org/)

[Apache sharding sphere GitHub](https://github.com/apache/shardingsphere)

[Apache ShardingSphere 松弛通道](https://apacheshardingsphere.slack.com/)

## 作者

徐阳， [Servyou Group](https://www.crunchbase.com/organization/servyou-group) 的中间件 R&D 工程师。负责海量数据的表和数据库分片。开源爱好者和 ShardingSphere 贡献者。目前，他对开发 ShardingSphere 项目的内核模块感兴趣。