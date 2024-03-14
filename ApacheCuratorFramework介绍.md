# ApacheCuratorFramework介绍

**Apache Curator**是一个用于Apache ZooKeeper的客户端库,为ZooKeeper客户端提供了高级API,使用户可以更轻松地实现诸如分布式锁、领导者选举、配置管理等功能。

Apache Curator主要包含以下几个模块:

1. **curator-framework**:这是Curator的核心模块,提供了ZooKeeper基本操作的封装和高级特性,如重试策略、事件监听等。
2. curator-recipes实现了ZooKeeper常见编程模式(Recipes)的封装,包括:
   - 分布式锁(Locks)
   - 分布式计数器(Counters)
   - 分布式队列(Queues)
   - 分布式原子广播(Atomic Broadcast)
   - 分布式屏障(Barriers)
   - 缓存模式(Cache Pattern)
3. **curator-client**:提供了一些ZooKeeper客户端实用工具类,如处理重试策略、添加监听器等。
4. **curator-test**:包含了与测试相关的类,最重要的是`TestingServer`,一个用于单元测试的嵌入式ZooKeeper服务器实现。
5. **curator-examples**:提供了使用Curator进行编程的示例代码。
6. **curator-x-async**:以reactive风格封装了Curator的异步操作。
7. **curator-x-rpc**:实现了基于ZooKeeper的RPC框架。

除了这些官方模块,Curator还包含一些扩展模块,由其他开发者贡献,如:

- curator-rx: RxJava风格的Curator绑定
- curator-recipes-standalonex: 独立Curator recipes实现

总的来说,Curator通过模块化的设计,按需提供了ZooKeeper编程的各种工具,极大简化了开发人员基于ZooKeeper构建分布式应用的工作。
