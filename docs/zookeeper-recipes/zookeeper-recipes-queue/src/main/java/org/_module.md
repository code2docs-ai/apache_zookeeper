# 基础信息

|      |      |
|------|------|
| 名称 | org |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-recipes/zookeeper-recipes-queue/src/main/java/org |
| 包名 | zookeeper.docs.zookeeper-recipes.zookeeper-recipes-queue.src.main.java.org |
| 概述说明 | DistributedQueue是基于ZooKeeper实现的分布式队列，支持offer添加元素，take/remove/poll获取并删除元素，peek/element查看队首元素，通过有序子节点管理队列顺序。 |

# 说明

DistributedQueue是一个基于ZooKeeper实现的分布式队列类，提供线程安全的队列操作。核心功能包括：通过orderedChildren方法获取有序子节点列表，smallestChildName查找最小序号节点。支持标准队列操作：element查看队首元素，remove删除并返回队首，take阻塞式获取队首，offer添加元素，peek非空安全查看队首，poll非空安全移除队首。使用持久顺序节点存储数据，通过重试机制处理并发修改，利用LatchChildWatcher实现阻塞等待。包含日志记录和异常处理，确保在节点不存在时自动创建父目录。


### 包内部结构视图

```mermaid
graph TD
    java --> org
    org --> apache
    apache --> zookeeper
    zookeeper --> recipes
    recipes --> queue
    queue --> DistributedQueue.java
```

该流程图展示了Zookeeper队列模块的层级结构，从根目录java开始逐级展开，经过org、apache、zookeeper、recipes多级目录后，最终到达queue目录下的DistributedQueue.java实现文件。整个结构清晰地呈现了分布式队列功能的包路径组织方式，共包含6个层级节点。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [apache](apache/_module.md) | package | DistributedQueue是基于ZooKeeper实现的分布式队列，支持offer添加元素，take/remove/poll获取并删除元素，peek/element查看队首元素，通过有序子节点管理队列顺序。 |


