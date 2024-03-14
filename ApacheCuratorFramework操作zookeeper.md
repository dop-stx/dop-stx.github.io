# **curator-framework操作zookeeper**

`curator-framework`模块提供了对ZooKeeper服务器的底层操作,使开发人员可以更简单地与ZooKeeper进行交互。以下是一些常见的用法:

**创建CuratorFramework实例**

```java
CuratorFramework client = CuratorFrameworkFactory.newClient(
                             "127.0.0.1:2181", // ZooKeeper连接字符串
                             new RetryNTimes(5, 1000) // 重试策略
                          );
client.start(); // 启动客户端
```

**创建节点**

```java
client.create().forPath("/path/to/node", "node-data".getBytes()); // 创建持久节点
client.create().forPath("/path/to/node", "node-data".getBytes(), CreateMode.EPHEMERAL); // 创建临时节点
```

*读取节点数据**

```java
byte[] nodeData = client.getData().forPath("/path/to/node"); // 获取节点数据
```

**更新节点数据**

```java
client.setData().forPath("/path/to/node", "new-data".getBytes()); // 更新节点数据
```

**删除节点**

```java
client.delete().forPath("/path/to/node"); // 删除节点
client.delete().guaranteed().forPath("/path/to/node"); // 递归删除节点及其所有子节点
```

**监听节点变化**

```java
client.getData().watched().forPath("/path/to/node"); // 注册监听器
@Override
public void process(WatchedEvent event) {
    // 处理事件
}
```

**使用ZooKeeper Recipes** Curator还提供了对ZooKeeper Recipes的封装支持,如分布式锁、计数器、队列等。

```
InterProcessMutex lock = new InterProcessMutex(client, "/path/to/lock");
lock.acquire(); // 获取分布式锁
// 加锁部分代码...
lock.release(); // 释放分布式锁
```

总的来说,`curator-framework`模块通过简化ZooKeeper客户端的使用,提高了开发效率,同时内置重试和事件监听机制,提高了应用的健壮性。
