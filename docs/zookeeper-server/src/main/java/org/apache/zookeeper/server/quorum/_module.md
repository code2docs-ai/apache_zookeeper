# 基础信息

|      |      |
|------|------|
| 名称 | quorum |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper/server/quorum |
| 包名 | zookeeper.docs.zookeeper-server.src.main.java.org.apache.zookeeper.server.quorum |
| 概述说明 | Zookeeper集群核心模块，包含选举、同步、认证、会话管理等组件，支持领导者选举、法定人数验证、SASL安全认证、请求处理链等功能，确保分布式一致性与高可用性。 |

# 说明

## 概述  
1. 该模块是ZooKeeper集群的核心仲裁系统，负责分布式决策的权重计算与投票仲裁，类似议会表决机制。  
2. 主要接口规范为QuorumVerifier定义的法定人数检查方法，例如`containsQuorum`验证投票有效性。  
3. 关键数据结构包括分层权重HashMap（QuorumHierarchical）和原子布尔标志（QuorumOracleMaj）。  
4. 外部依赖项限于ZooKeeper核心日志系统，例如通过SLF4J记录调试信息。  
5. 实现采用策略模式，例如QuorumMaj提供简单多数决，QuorumHierarchical支持分组加权投票。  

## 主要业务场景  
1. 支持集群提案投票验证流程，例如判断事务提案是否获得足够成员认可。  
2. 典型交互模式为同步权重计算，QuorumOracleMaj会异步读取Oracle文件辅助决策。  
3. 功能完整性体现在支持多数决、分层权重、Oracle扩展三种验证策略。  
4. 主要使用场景包括集群配置变更、Leader选举等需要法定人数的场景。  
5. 提供Java接口API，例如QuorumVerifier定义核心验证契约。  
6. 第三方集成案例为ZooKeeper服务端核心模块，例如与Zab协议协同工作。


### 包内部结构视图

```mermaid
graph TD
    quorum --> flexible
    quorum --> auth
    quorum --> FollowerBean.java
    quorum --> LearnerHandler.java
    quorum --> ServerMXBean.java
    quorum --> LeaderElectionBean.java
    quorum --> QuorumPeerConfig.java
    quorum --> ReadOnlyZooKeeperServer.java
    quorum --> SendAckRequestProcessor.java
    quorum --> LeaderSessionTracker.java
    quorum --> QuorumBean.java
    quorum --> LeaderZooKeeperServer.java
    quorum --> LocalPeerMXBean.java
    quorum --> Leader.java
    quorum --> ProposalRequestProcessor.java
    quorum --> CommitProcessor.java
    quorum --> ObserverMXBean.java
    quorum --> FastLeaderElection.java
    quorum --> LeaderElectionMXBean.java
    quorum --> LearnerSessionTracker.java
    quorum --> LeaderMXBean.java
    quorum --> QuorumZooKeeperServer.java
    quorum --> LearnerHandlerBean.java
    quorum --> Observer.java
    quorum --> BufferStats.java
    quorum --> SyncThrottleException.java
    quorum --> FollowerZooKeeperServer.java
    quorum --> PrependableSocket.java
    quorum --> QuorumCnxManager.java
    quorum --> Election.java
    quorum --> ObserverMaster.java
    quorum --> MultipleAddresses.java
    quorum --> LocalSessionTracker.java
    quorum --> Vote.java
    quorum --> QuorumStats.java
    quorum --> ReadOnlyBean.java
    quorum --> LearnerZooKeeperServer.java
    quorum --> LearnerSender.java
    quorum --> FollowerMXBean.java
    quorum --> ObserverRequestProcessor.java
    quorum --> RemotePeerBean.java
    quorum --> LeaderRequestProcessor.java
    quorum --> LocalPeerBean.java
    quorum --> Learner.java
    quorum --> LearnerHandlerMXBean.java
    quorum --> LearnerSyncRequest.java
    quorum --> SyncedLearnerTracker.java
    quorum --> LearnerSyncThrottler.java
    quorum --> QuorumMXBean.java
    quorum --> UnifiedServerSocket.java
    quorum --> AckRequestProcessor.java
    quorum --> QuorumPeerMain.java
    quorum --> LearnerMaster.java
    quorum --> LeaderBean.java
    quorum --> FollowerRequestProcessor.java
    quorum --> ObserverZooKeeperServer.java
    quorum --> QuorumPeer.java
    quorum --> UpgradeableSessionTracker.java
    quorum --> ReadOnlyRequestProcessor.java
    quorum --> RemotePeerMXBean.java
    quorum --> StateSummary.java
    quorum --> Follower.java
    quorum --> ServerBean.java
    flexible --> QuorumOracleMaj.java
    flexible --> QuorumHierarchical.java
    flexible --> QuorumVerifier.java
    flexible --> QuorumMaj.java
    auth --> SaslQuorumAuthLearner.java
    auth --> SaslQuorumServerCallbackHandler.java
    auth --> NullQuorumAuthServer.java
    auth --> QuorumAuthServer.java
    auth --> NullQuorumAuthLearner.java
    auth --> SaslQuorumAuthServer.java
    auth --> QuorumAuthLearner.java
    auth --> QuorumAuth.java
```

该流程图展示了Zookeeper服务器中quorum模块的完整结构，包含flexible和auth两个子目录以及大量核心类文件。flexible目录下主要存放选举算法实现类，auth目录包含认证相关组件，其余直接位于quorum下的文件涵盖了服务器状态管理、请求处理、选举机制等核心功能模块，完整呈现了Zookeeper集群协调服务的类层次结构。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [ServerMXBean.java](ServerMXBean.md) | file | ServerMXBean接口定义获取服务器MBean名称和启动时间的方法。 |
| [LearnerHandler.java](LearnerHandler.md) | file | LearnerHandler是ZooKeeper中处理学习者（Follower/Observer）与Leader同步的线程类，主要功能包括：管理Socket连接、处理同步请求（DIFF/TRUNC/SNAP）、转发提案包、监控ACK超时。关键属性：Socket连接、待发送包队列、同步状态检查、学习者ID。核心逻辑：通过队列机制高效转发数据包，支持强制快照同步，确保集群数据一致性。 |
| [FollowerBean.java](FollowerBean.md) | file | FollowerBean类继承ZooKeeperServerBean，实现FollowerMXBean接口，提供获取和设置Follower节点信息的方法，包括选举耗时、同步限制等。 |
| [auth](auth/_module.md) | package | SaslQuorumAuthLearner处理ZooKeeper仲裁学习者的SASL认证，含登录状态、强制认证标志和服务主体字段。SaslQuorumServerCallbackHandler处理SASL回调，支持DIGEST-MD5。NullQuorumAuthServer和NullQuorumAuthLearner为空实现，跳过认证。QuorumAuthServer和QuorumAuthLearner为认证接口。SaslQuorumAuthServer处理SASL服务器认证流程。QuorumAuth定义认证常量和方法。 |
| [QuorumPeerConfig.java](QuorumPeerConfig.md) | file | QuorumPeerConfig类用于配置ZooKeeper集群节点参数，包含端口、数据目录、会话超时、选举算法等核心配置项，支持动态配置和SSL认证，并处理服务器角色（参与者/观察者）及SASL安全验证。 |
| [LeaderElectionBean.java](LeaderElectionBean.md) | file | LeaderElectionBean类实现LeaderElectionMXBean和ZKMBeanInfo接口，记录启动时间并提供名称、隐藏状态和启动时间查询功能。 |
| [LeaderSessionTracker.java](LeaderSessionTracker.md) | file | LeaderSessionTracker类继承UpgradeableSessionTracker，管理本地和全局会话，提供会话创建、移除、检查等功能，支持会话超时和所有权设置，确保会话一致性。 |
| [SendAckRequestProcessor.java](SendAckRequestProcessor.md) | file | SendAckRequestProcessor类处理非同步请求，向Learner发送ACK响应，异常时关闭连接，提供flush和空shutdown方法。 |
| [ReadOnlyZooKeeperServer.java](ReadOnlyZooKeeperServer.md) | file | ReadOnlyZooKeeperServer扩展ZooKeeperServer，实现只读模式，处理请求、会话跟踪及JMX注册，支持启动关闭和配置转储。 |
| [ObserverMXBean.java](ObserverMXBean.md) | file | ObserverMXBean接口扩展ZooKeeperServerMXBean，提供获取待验证数、仲裁地址和主节点地址的方法，并支持设置新主节点地址。 |
| [ProposalRequestProcessor.java](ProposalRequestProcessor.md) | file | ProposalRequestProcessor是ZooKeeper请求处理器，处理Leader的同步请求和事务提议。根据配置决定是否转发Learner请求，包含初始化、处理和关闭逻辑。 |
| [LocalPeerMXBean.java](LocalPeerMXBean.md) | file | LocalPeerMXBean接口扩展ServerMXBean，提供获取和设置本地对等节点配置的方法，包括超时、同步限制、选举地址、状态等关键信息。 |
| [LeaderZooKeeperServer.java](LeaderZooKeeperServer.md) | file | LeaderZooKeeperServer是ZooKeeper的领导者服务器实现，负责处理请求链、容器管理、会话跟踪和JMX注册。包含请求处理器初始化、指标监控、启动关闭逻辑及会话验证功能。 |
| [QuorumBean.java](QuorumBean.md) | file | QuorumBean类实现QuorumMXBean和ZKMBeanInfo接口，封装QuorumPeer功能，提供集群大小、同步限制、SSL设置等管理方法。 |
| [LeaderMXBean.java](LeaderMXBean.md) | file | LeaderMXBean接口扩展ZooKeeperServerMXBean，提供集群当前zxid、跟随者信息、选举耗时、提案大小统计及并发同步配置等功能。 |
| [LearnerSessionTracker.java](LearnerSessionTracker.md) | file | LearnerSessionTracker类继承UpgradeableSessionTracker，管理本地和全局会话，支持会话创建、移除、触摸及状态检查，确保会话一致性并处理竞态条件。 |
| [LeaderElectionMXBean.java](LeaderElectionMXBean.md) | file | 公开接口LeaderElectionMXBean定义了获取选举开始时间的方法getStartTime()。 |
| [LearnerHandlerBean.java](LearnerHandlerBean.md) | file | LearnerHandlerBean类实现LearnerHandlerMXBean和ZKMBeanInfo接口，管理LearnerHandler连接，记录远程地址和服务器ID，提供终止连接方法。 |
| [Election.java](Election.md) | file | 选举接口定义两个方法：查找领导者（可能阻塞）和关闭选举。 |
| [PrependableSocket.java](PrependableSocket.md) | file | PrependableSocket扩展Socket类，允许将已读字节重新放回输入流。通过prependToInputStream方法实现，仅能调用一次且字节长度非零。使用PushbackInputStream处理字节回推。 |
| [FollowerZooKeeperServer.java](FollowerZooKeeperServer.md) | file | FollowerZooKeeperServer是ZooKeeper的从节点实现，处理同步请求、事务提交和请求处理链，支持JMX监控和性能指标注册。 |
| [SyncThrottleException.java](SyncThrottleException.md) | file | SyncThrottleException是异常类，当并发同步数超过阈值时抛出，包含当前同步数、阈值和同步类型信息。 |
| [BufferStats.java](BufferStats.md) | file | BufferStats类用于跟踪缓冲区使用情况，记录最新、最小和最大缓冲区大小，提供获取和重置功能。 |
| [Observer.java](Observer.md) | file | Observer类用于ZooKeeper观察者节点，处理与领导者的连接、同步及数据包处理。包含重连延迟和选举延迟配置，支持动态切换主节点，记录同步状态和性能指标。 |
| [LearnerSender.java](LearnerSender.md) | file | LearnerSender是ZooKeeper线程类，负责处理QuorumPacket队列，发送消息给Leader，支持刷新缓冲、异常处理和优雅关闭。 |
| [LearnerZooKeeperServer.java](LearnerZooKeeperServer.md) | file | LearnerZooKeeperServer是QuorumZooKeeperServer的抽象子类，用于处理学习者节点的会话跟踪、JMX注册和请求处理。包含会话快照获取、服务器ID获取、会话验证和组件关闭等功能。 |
| [ReadOnlyBean.java](ReadOnlyBean.md) | file | ReadOnlyBean继承ZooKeeperServerBean，构造方法接收ZooKeeperServer参数，getName方法返回"ReadOnlyServer"。 |
| [QuorumStats.java](QuorumStats.md) | file | QuorumStats类用于管理集群状态，提供服务器状态和节点列表，支持领导者、跟随者等角色，并输出相关信息。 |
| [Vote.java](Vote.md) | file | Vote类表示选举投票，包含id、zxid、选举和peer纪元、状态等属性，提供构造方法和equals逻辑，支持不同版本比较。 |
| [LocalSessionTracker.java](LocalSessionTracker.md) | file | LocalSessionTracker继承SessionTrackerImpl，管理本地会话，提供会话创建、查询及超时处理功能，不支持全局会话。 |
| [MultipleAddresses.java](MultipleAddresses.md) | file | MultipleAddresses类管理多个InetSocketAddress，支持并发操作，提供地址增删查、可达性检查、DNS重解析及超时设置功能。 |
| [LeaderRequestProcessor.java](LeaderRequestProcessor.md) | file | LeaderRequestProcessor处理ZooKeeper请求，先检查ACL权限，处理会话升级，再转发请求至下一处理器。 |
| [RemotePeerBean.java](RemotePeerBean.md) | file | RemotePeerBean类实现远程管理接口，封装QuorumPeer节点信息，提供名称、地址、选举地址、客户端地址及角色查询功能。 |
| [ObserverRequestProcessor.java](ObserverRequestProcessor.md) | file | ObserverRequestProcessor是ZooKeeper观察者服务器的请求处理器，继承ZooKeeperCriticalThread。它通过队列处理请求，检查ACL后转发给下一处理器或领导者，支持多种操作类型如创建、删除等，并处理会话和异常情况。 |
| [FollowerMXBean.java](FollowerMXBean.md) | file | FollowerMXBean接口扩展ZooKeeperServerMXBean，提供获取仲裁地址、最后队列zxid、待验证计数、选举耗时、主包队列大小限制及设置、并发快照和差异同步数量及设置的方法。 |
| [SyncedLearnerTracker.java](SyncedLearnerTracker.md) | file | SyncedLearnerTracker类管理QuorumVerifierAcksetPair列表，提供添加验证器、记录确认、检查成员存在性、验证法定人数及输出确认集功能。 |
| [LearnerSyncRequest.java](LearnerSyncRequest.md) | file | LearnerSyncRequest类继承Request，包含LearnerHandler和父类参数，用于同步学习请求。 |
| [LearnerHandlerMXBean.java](LearnerHandlerMXBean.md) | file | LearnerHandlerMXBean接口提供终止连接功能，断开后学习者会尝试重新连接领导者或下一个ObserverMaster。 |
| [LocalPeerBean.java](LocalPeerBean.md) | file | LocalPeerBean类扩展ServerBean，实现LocalPeerMXBean接口，封装QuorumPeer功能，提供名称、状态、超时设置、选举地址等集群管理信息。 |
| [LearnerSyncThrottler.java](LearnerSyncThrottler.md) | file | LearnerSyncThrottler类用于限制并发同步操作数量，支持DIFF和SNAP两种同步类型，提供开始、结束同步及并发数设置功能。 |
| [AckRequestProcessor.java](AckRequestProcessor.md) | file | AckRequestProcessor类实现RequestProcessor接口，将请求作为ACK转发给Leader处理，包含processRequest和shutdown方法。 |
| [QuorumMXBean.java](QuorumMXBean.md) | file | QuorumMXBean接口提供仲裁组管理功能，包括名称、成员数、同步限制设置、SSL通信状态及观察者选举延迟配置。支持获取和修改关键参数。 |
| [QuorumPeerMain.java](QuorumPeerMain.md) | file | QuorumPeerMain是ZooKeeper的主类，用于启动和管理分布式集群。它解析配置文件，初始化数据清理任务，并根据配置决定以集群或单机模式运行。支持多种异常处理和日志记录，确保服务稳定运行。 |
| [UpgradeableSessionTracker.java](UpgradeableSessionTracker.md) | file | 可升级会话跟踪器，管理本地和全局会话，支持会话升级、超时处理和状态检查，使用并发映射存储会话数据。 |
| [ObserverZooKeeperServer.java](ObserverZooKeeperServer.md) | file | ObserverZooKeeperServer是ZooKeeper的观察者服务器实现，继承自LearnerZooKeeperServer。主要功能包括处理来自Leader的INFORM请求、提交请求到处理器链、支持同步请求处理器写入事务日志和定期快照。观察者行为与Follower类似，但优化了请求处理流程。 |
| [FollowerRequestProcessor.java](FollowerRequestProcessor.md) | file | FollowerRequestProcessor是ZooKeeper的请求处理器，负责处理跟随者服务器的请求队列，筛选并转发请求至下一处理器或领导者，支持会话升级和异常处理。 |
| [ServerBean.java](ServerBean.md) | file | 抽象类ServerBean实现ServerMXBean和ZKMBeanInfo接口，包含启动时间和默认非隐藏状态。 |
| [StateSummary.java](StateSummary.md) | file | StateSummary类记录当前周期和最后事务ID，提供比较、相等判断和哈希计算功能。 |
| [RemotePeerMXBean.java](RemotePeerMXBean.md) | file | RemotePeerMXBean接口定义了获取远程节点信息的方法，包括名称、仲裁地址、选举地址、客户端地址、学习类型及是否为主节点。 |
| [Follower.java](Follower.md) | file | Follower类继承Learner，实现跟随Leader的逻辑，包括连接Leader、同步数据、处理消息包等。维护最后队列的zxid，支持ObserverMaster管理观察者。关键方法有followLeader和processPacket，处理不同消息类型如PING、PROPOSAL、COMMIT等。 |
| [ReadOnlyRequestProcessor.java](ReadOnlyRequestProcessor.md) | file | 这是一个ZooKeeper的只读请求处理器类，继承自ZooKeeperCriticalThread并实现RequestProcessor接口。主要功能是过滤非读请求并转发合法请求到下一处理器。包含请求队列、异常处理和关闭逻辑。 |
| [QuorumPeer.java](QuorumPeer.md) | file | QuorumPeer是ZooKeeper的核心类，负责集群选举、状态管理及配置处理。主要功能包括：维护集群成员视图、处理动态配置更新、管理选举算法（如FastLeaderElection）、协调服务器角色（Leader/Follower/Observer切换）。关键属性含myid、选举类型、会话超时设置，支持JMX监控和SASL认证。通过原子操作确保配置一致性，处理网络通信（QuorumCnxManager）及数据持久化（ZKDatabase）。 |
| [LeaderBean.java](LeaderBean.md) | file | LeaderBean继承ZooKeeperServerBean，实现LeaderMXBean接口，封装Leader相关操作，包括获取选举耗时、提案大小统计、并发同步设置及处理跟随者信息等。 |
| [LearnerMaster.java](LearnerMaster.md) | file | LearnerMaster类管理ZooKeeper学习者的同步和请求处理，包含快照和差异同步的限流控制，处理学习者连接、会话验证、请求转发及领导者选举相关操作。 |
| [UnifiedServerSocket.java](UnifiedServerSocket.md) | file | UnifiedServerSocket支持TLS和明文连接，根据allowInsecureConnection参数决定是否接受明文连接。UnifiedSocket在首次读写时检测连接模式，自动升级TLS或保持明文，避免在accept线程阻塞。 |
| [Learner.java](Learner.md) | file | Learner类实现ZooKeeper学习者节点功能，包含与Leader节点通信、数据同步、会话验证等核心逻辑。主要功能：1. 通过Socket连接Leader；2. 处理提案/提交事务；3. 支持同步/异步消息发送；4. 实现快照同步和日志截断；5. 维护待验证会话列表。关键属性：Socket连接、输入输出流、消息跟踪器、配置参数等。 |
| [ObserverMaster.java](ObserverMaster.md) | file | ObserverMaster是ZooKeeper中管理观察者节点的类，继承LearnerMaster并实现Runnable。主要功能包括处理观察者连接、同步事务包、维护会话验证队列、管理网络通信及JMX监控。通过线程和定时任务维护活跃连接，支持事务包缓存与转发，确保观察者与集群同步。 |
| [QuorumCnxManager.java](QuorumCnxManager.md) | file | QuorumCnxManager是ZooKeeper中管理集群节点间网络通信的核心类，负责建立连接、消息收发及线程池管理。主要功能包括：处理节点间异步连接请求（支持SSL和SASL认证）、维护发送/接收队列、管理SendWorker和RecvWorker线程、支持多地址选举协议（PROTOCOL_VERSION_V2），并提供连接重试、流量控制及异常处理机制。关键参数含RECV_CAPACITY(100)、SEND_CAPACITY(1)、PACKETMAXSIZE(512KB)。通过ConcurrentHashMap维护各节点的消息队列和工作线程。 |
| [QuorumZooKeeperServer.java](QuorumZooKeeperServer.md) | file | QuorumZooKeeperServer扩展ZooKeeperServer，支持会话升级、本地会话管理及集群配置。包含会话检查、状态设置、指标注册和监控信息输出功能。 |
| [FastLeaderElection.java](FastLeaderElection.md) | file | FastLeaderElection是ZooKeeper的快速领导者选举实现，基于TCP通信，包含通知和确认机制，通过逻辑时钟和投票集合确定领导者，支持最小200ms和最大60秒的通知间隔，处理LOOKING、FOLLOWING和LEADING状态转换，确保集群一致性。 |
| [CommitProcessor.java](CommitProcessor.md) | file | CommitProcessor是ZooKeeper核心线程，负责处理读写请求队列，支持多线程工作池配置，通过批处理优化性能，确保请求按序提交和处理，同时提供监控指标和优雅停机机制。 |
| [Leader.java](Leader.md) | file | Leader类实现ZooKeeper集群的领导者功能，负责处理提案提交、追随者同步、选举确认等核心逻辑。关键点包括：提案管理（Proposal类）、追随者状态跟踪（LearnerHandler）、TCP连接优化（nodelay配置）、ACK延迟日志记录、动态重配置支持、领导者选举超时控制。通过QuorumVerifier验证法定人数，使用LearnerCnxAcceptor线程处理追随者连接，确保数据一致性和集群可用性。 |
| [flexible](flexible/_module.md) | package | QuorumOracleMaj扩展QuorumMaj，添加Oracle机制，含路径属性和决策方法。QuorumHierarchical管理分层法定人数验证，支持权重分组和过半判定。QuorumVerifier是接口，定义法定人数验证方法。QuorumMaj实现多数投票机制，维护成员映射和阈值计算。 |


