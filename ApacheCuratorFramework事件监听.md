# Apache Curator事件监听机制

​	Apache Curator提供了全面的事件监听机制,允许应用程序监听ZooKeeper中的各种事件,从而及时作出响应。这些监听机制主要分为两大类:数据节点监听和网络事件监听。

**1. 数据节点监听**

​	数据节点监听主要关注ZooKeeper中数据节点的变化,包括节点数据变更和子节点变化。Curator提供了多种方式来监听这些变化,具体如下:

**1.1 使用getData().watched()和getChildren().watched()**

这种方式允许我们为特定的节点路径注册监听器,当该节点的数据或子节点列表发生变化时,监听器会收到通知。但需要注意的是,这种监听是一次性的,每次变化后需要重新注册监听器。

```
// 监听节点数据变化
client.getData().watched().forPath("/path/to/node");
client.getCuratorListenable().addListener(new CuratorListener() {
    @Override
    public void eventReceived(CuratorFramework client, CuratorEvent event) throws Exception {
        if (event.getType() == CuratorEventType.NODE_DATA_CHANGED) {
            // 处理节点数据变化
            byte[] data = client.getData().forPath("/path/to/node");
            // 重新注册监听器
            client.getData().watched().forPath("/path/to/node");
        }
    }
});

// 监听子节点变化
client.getChildren().watched().forPath("/path/to/parent");
client.getCuratorListenable().addListener(new CuratorListener() {
    @Override
    public void eventReceived(CuratorFramework client, CuratorEvent event) throws Exception {
        if (event.getType() == CuratorEventType.CHILDREN_CHANGED) {
            // 处理子节点变化
            List<String> children = client.getChildren().forPath("/path/to/parent");
            // 重新注册监听器
            client.getChildren().watched().forPath("/path/to/parent");
        }
    }
});
```

**1.2 使用NodeCache和PathChildrenCache**

`NodeCache`和`PathChildrenCache`分别提供了对单个节点和节点树的缓存机制,并内置了监听器。当缓存数据发生变化时,相应的监听器会收到通知。这种方式使用更加方便,无需手动重新注册监听器。

```
// 使用NodeCache监听单个节点
final NodeCache nodeCache = new NodeCache(client, "/path/to/node");
nodeCache.start();
nodeCache.getListenable().addListener(new NodeCacheListener() {
    @Override
    public void nodeChanged() throws Exception {
        // 处理节点数据变化
        byte[] data = nodeCache.getCurrentData().getData();
    }
});

// 使用PathChildrenCache监听节点树
final PathChildrenCache childrenCache = new PathChildrenCache(client, "/path/to/parent", true);
childrenCache.start();
childrenCache.getListenable().addListener(new PathChildrenCacheListener() {
    @Override
    public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
        switch (event.getType()) {
            case CHILD_ADDED:
                // 处理子节点添加事件
                break;
            case CHILD_UPDATED:
                // 处理子节点更新事件
                break;
            case CHILD_REMOVED:
                // 处理子节点删除事件
                break;
            default:
                break;
        }
    }
});
```

**2. 网络事件监听**

网络事件监听关注的是ZooKeeper客户端与服务器之间的网络连接状态,以及与会话相关的事件。Curator提供了以下几种监听器:

**2.1 ConnectionStateListener**

用于监听连接状态的变化,如连接丢失(SUSPENDED)、连接重新建立(RECONNECTED)等。

示例代码:

```
client.getConnectionStateListenable().addListener(new ConnectionStateListener() {
    @Override
    public void stateChanged(CuratorFramework client, ConnectionState newState) {
        if (newState == ConnectionState.RECONNECTED) {
            System.out.println("Connection re-established");
            // 可能需要重新获取数据或者重新注册监听器
        } else if (newState == ConnectionState.SUSPENDED) {
            System.out.println("Connection lost");
            // 可能需要暂停某些操作
        }
    }
});
```

**2.2 UnhandledErrorListener**

用于监听那些无法通过重试策略处理的异常,我们可以选择是否需要关闭CuratorFramework。

示例代码:

```
client.getUnhandledErrorListenable().addListener(new UnhandledErrorListener() {
    @Override
    public void unhandledError(String cause, Throwable error) {
        System.err.println("Unhandled error: " + cause + ", " + error);
        // 可以选择关闭CuratorFramework
        client.close();
    }
});
```

**2.3 会话事件监听(CuratorListener)**

除了连接状态,我们还可以通过`CuratorListener`监听与会话相关的事件,如会话过期(SESSION_EXPIRED)、会话重连成功(SESSION_RECONNECTED)等。

示例代码:

```
client.getCuratorListenable().addListener(new CuratorListener() {
    @Override
    public void eventReceived(CuratorFramework client, CuratorEvent event) throws Exception {
        if (event.getType() == CuratorEventType.SESSION_EXPIRED) {
            System.out.println("Session expired");
            // 需要重新建立会话并可能需要重新获取数据
        } else if (event.getType() == CuratorEventType.SESSION_RECONNECTED) {
            System.out.println("Session re-established");
            // 可能需要重新注册监听器
        }
    }
});
```

​	通过合理使用网络事件监听机制,应用程序可以对各种网络异常做出正确的响应,提高应用程序的健壮性和可靠性。例如,当连接断开时暂停某些操作,当重连成功时恢复操作;当会话过期时重新建立会话并重新获取数据等。

​	总的来说,Curator提供了全面的事件监听支持,包括数据节点监听和网络事件监听。通过实现相应的监听器接口,我们可以获取ZooKeeper中发生的各种事件通知,并根据具体情况实现自己的业务逻辑,从而构建出更加可靠和健壮的分布式系统。
