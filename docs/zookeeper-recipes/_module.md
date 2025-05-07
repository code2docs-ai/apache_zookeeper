# 基础信息

|      |      |
|------|------|
| 名称 | zookeeper-recipes |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-recipes |
| 包名 | zookeeper.docs.zookeeper-recipes |
| 概述说明 | 基于ZooKeeper的分布式工具集：1.领导者选举模块通过临时节点竞争主节点，支持事件回调。2.写锁工具利用顺序节点实现公平锁，含异步监听和重试机制。3.分布式队列提供线程安全操作，支持阻塞获取和持久节点存储。 |

# 说明

## 概述  
1. 该模块实现基于ZooKeeper的分布式协调功能，包含领导者选举、分布式锁和队列三类核心组件，类似分布式系统的"交通警察"。  
2. 主要接口包括事件回调机制（如LeaderElectionAware）、锁操作API（如WriteLock）和队列方法（如take/offer），例如通过临时顺序节点实现公平竞争。  
3. 关键数据结构涵盖LeaderOffer提议对象、带序号的ZNodeName节点和有序子节点列表，例如节点路径比较逻辑实现优先级排序。  
4. 深度依赖ZooKeeper服务，利用其临时节点、顺序编号和Watcher机制，例如ProtocolSupport封装会话重试策略。  

## 主要业务场景  
1. 支持集群主节点选举、临界资源锁竞争和任务队列管理，例如微服务集群的主备切换场景。  
2. 采用事件驱动和异步监听模式，类似观察者模式的状态变更通知，如LockListener回调锁获取事件。  
3. 功能完整性体现在全生命周期管理，包含自动重试（10次上限）、资源清理和会话恢复，例如队列操作自动创建父节点。  
4. 适用于需要强一致性的分布式场景，如配置更新、任务调度和资源访问控制。  
5. 提供Java类库形式的API，例如LeaderElectionSupport基类和ZooKeeperOperation可扩展接口。  
6. 可与大数据系统集成，例如作为HBase的分布式锁组件或Kafka的控制器选举模块。


### 包内部结构视图

None

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [zookeeper-recipes-queue](zookeeper-recipes-queue/src/main/java/org/_module.md) | module | DistributedQueue是基于ZooKeeper实现的分布式队列，支持offer添加元素，take/remove/poll获取并删除元素，peek/element查看队首元素，通过有序子节点管理队列顺序。 |
| [zookeeper-recipes-lock](zookeeper-recipes-lock/src/main/java/org/_module.md) | module | WriteLock类基于ZooKeeper实现分布式写锁，支持公平获取和监听回调。LockListener接口定义锁状态回调。ZooKeeperOperation接口处理可重试操作。ZNodeName类解析带序号节点名。ProtocolSupport类提供ZooKeeper操作基类，支持重试和资源管理。 |
| [zookeeper-recipes-election](zookeeper-recipes-election/src/main/java/org/_module.md) | module | LeaderElectionSupport类实现基于ZooKeeper的领导者选举，含状态管理和核心功能。LeaderOffer类存储领导节点信息并提供比较功能。LeaderElectionAware接口处理选举状态变化事件。 |


