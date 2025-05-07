# 基础信息

|      |      |
|------|------|
| 名称 | server |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper/server |
| 包名 | zookeeper.docs.zookeeper-server.src.main.java.org.apache.zookeeper.server |
| 概述说明 | ZooKeeper服务器核心模块集，包含工具集、嵌入式服务、事务日志管理、线程处理、认证授权、连接工厂、四字母命令、度量统计、控制平面、管理接口、仲裁系统、监视器管理、请求处理器、数据树、会话跟踪等组件。提供分布式协调、速率控制、日志切割、ID转换、性能监控、集群管理等功能，支持多种认证方式和安全协议，采用线程安全设计，适用于运维调试和性能分析场景。 |

# 说明

## 概述  
该模块是ZooKeeper服务器的核心实现，提供分布式协调服务的基础设施，类似微型分布式键值存储系统。主要接口包括Java API（如ZooKeeperServerEmbedded）和HTTP管理接口（如/admin/commands），关键数据结构含Zxid排序的日志文件、ConcurrentHashMap管理的会话和节点映射。依赖JVM系统库、Netty框架和Kerberos认证模块，例如通过FileTxnSnapLog实现WAL机制。

## 主要业务场景  
支持集群选举、事务日志处理、会话管理等核心流程，例如QuorumVerifier实现投票仲裁。采用多线程交互模式（如PrepRequestProcessor链式处理），功能覆盖从ACL校验到JMX监控。主要用于分布式锁、配置管理等场景，提供Builder模式API与JettyAdminServer集成。第三方可通过四字母命令（如"stat"）或HTTP接口获取指标，类似Prometheus的监控方案。


### 包内部结构视图

```mermaid
graph TD
    server --> util
    server --> embedded
    server --> persistence
    server --> ZooKeeperThread.java
    server --> DataTreeBean.java
    server --> AuthenticationHelper.java
    server --> command
    server --> metric
    server --> controller
    server --> admin
    server --> ServerWatcher.java
    server --> BlueThrottle.java
    server --> quorum
    server --> auth
    server --> watch
    server --> PrepRequestProcessor.java
    server --> ZooKeeperCriticalThread.java
    server --> EphemeralType.java
    server --> ZooKeeperServerMXBean.java
    server --> ServerStats.java
    server --> ZKDatabase.java
    server --> DigestCalculator.java
    server --> NIOServerCnxnFactory.java
    server --> SyncRequestProcessor.java
    server --> ZooKeeperServerShutdownHandler.java
    server --> Stats.java
    server --> ContainerManager.java
    server --> DataNode.java
    server --> ServerCnxnHelper.java
    server --> ZooKeeperServerBean.java
    server --> EphemeralTypeEmulate353.java
    server --> ZooKeeperServerListenerImpl.java
    server --> ByteBufferInputStream.java
    server --> ServerMetrics.java
    server --> RequestProcessor.java
    server --> PurgeTxnLog.java
    server --> DumbWatcher.java
    server --> ServerConfig.java
    server --> NodeHashMap.java
    server --> ConnectionBean.java
    server --> ZooKeeperServerMain.java
    server --> ObserverBean.java
    server --> NettyServerCnxn.java
    server --> ConnectionMXBean.java
    server --> ExitCode.java
    server --> SessionTracker.java
    server --> Request.java
    server --> ZooKeeperSaslServer.java
    server --> WorkerService.java
    server --> SimpleRequestRecord.java
    server --> SnapshotRecursiveSummary.java
    server --> SnapshotFormatter.java
    server --> SessionTrackerImpl.java
    server --> ResponseCache.java
    server --> RateLogger.java
    server --> ByteBufferRequestRecord.java
    server --> NIOServerCnxn.java
    server --> ExpiryQueue.java
    server --> NodeHashMapImpl.java
    server --> ServerCnxnFactory.java
    server --> ZooTrace.java
    server --> TxnLogEntry.java
    server --> ClientCnxnLimitException.java
    server --> DataTree.java
    server --> RequestRecord.java
    server --> DataTreeMXBean.java
    server --> TraceFormatter.java
    server --> ZooKeeperServer.java
    server --> FinalRequestProcessor.java
    server --> ZooKeeperServerConf.java
    server --> DatadirCleanupManager.java
    server --> UnimplementedRequestProcessor.java
    server --> RequestThrottler.java
    server --> ServerCnxn.java
    server --> TxnLogProposalIterator.java
    server --> ReferenceCountedACLCache.java
    server --> SnapshotComparer.java
    server --> ZooKeeperServerListener.java

    util --> RateLimiter.java
    util --> LogChopper.java
    util --> ZxidUtils.java
    util --> KerberosUtil.java
    util --> AdHash.java
    util --> BitHashSet.java
    util --> ConfigUtils.java
    util --> OSMXBean.java
    util --> AuthUtil.java
    util --> JvmPauseMonitor.java
    util --> QuotaMetricsUtils.java
    util --> VerifyingFileFactory.java
    util --> BitMap.java
    util --> SerializeUtils.java
    util --> CircularBuffer.java
    util --> MessageTracker.java
    util --> RequestPathMetricsCollector.java

    embedded --> ExitHandler.java
    embedded --> ZooKeeperServerEmbedded.java
    embedded --> ZooKeeperServerEmbeddedImpl.java

    persistence --> FileTxnSnapLog.java
    persistence --> SnapStream.java
    persistence --> TxnLog.java
    persistence --> SnapShot.java
    persistence --> FileSnap.java
    persistence --> TxnLogToolkit.java
    persistence --> FileTxnLog.java
    persistence --> SnapshotInfo.java
    persistence --> FilePadding.java
    persistence --> Util.java

    command --> FourLetterCommands.java
    command --> CommandExecutor.java
    command --> SetTraceMaskCommand.java
    command --> EnvCommand.java
    command --> StatResetCommand.java
    command --> StatCommand.java
    command --> NopCommand.java
    command --> DigestCommand.java
    command --> ConsCommand.java
    command --> TraceMaskCommand.java
    command --> AbstractFourLetterCommand.java
    command --> DumpCommand.java
    command --> DirsCommand.java
    command --> MonitorCommand.java
    command --> IsroCommand.java
    command --> RuokCommand.java
    command --> CnxnStatResetCommand.java
    command --> WatchCommand.java
    command --> ConfCommand.java

    metric --> SimpleCounter.java
    metric --> AvgMinMaxCounter.java
    metric --> AvgMinMaxPercentileCounter.java
    metric --> AvgMinMaxCounterSet.java
    metric --> AvgMinMaxPercentileCounterSet.java
    metric --> Metric.java
    metric --> SimpleCounterSet.java

    controller --> ControllerServerConfig.java
    controller --> ControlCommand.java
    controller --> ControllableConnection.java
    controller --> CommandClient.java
    controller --> ControllableConnectionFactory.java
    controller --> ZooKeeperServerController.java
    controller --> ControllerService.java
    controller --> CommandListener.java

    admin --> GetCommand.java
    admin --> AuthRequest.java
    admin --> CommandBase.java
    admin --> ReadAheadEndpoint.java
    admin --> CommandOutputter.java
    admin --> Commands.java
    admin --> DummyAdminServer.java
    admin --> StreamOutputter.java
    admin --> AdminServer.java
    admin --> CommandResponse.java
    admin --> UnifiedConnectionFactory.java
    admin --> AdminServerFactory.java
    admin --> JettyAdminServer.java
    admin --> Command.java
    admin --> JsonOutputter.java
    admin --> PostCommand.java

    quorum --> flexible
    quorum --> FollowerBean.java
    quorum --> LearnerHandler.java
    quorum --> ServerMXBean.java
    quorum --> auth
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

    watch --> WatcherOrBitSet.java
    watch --> WatcherMode.java
    watch --> WatchManagerOptimized.java
    watch --> WatchesPathReport.java
    watch --> WatchManager.java
    watch --> WatchManagerFactory.java
    watch --> IDeadWatcherListener.java
    watch --> WatcherCleaner.java
    watch --> WatchesSummary.java
    watch --> WatchStats.java
    watch --> IWatchManager.java
    watch --> PathParentIterator.java
    watch --> WatchesReport.java
```

该流程图展示了Zookeeper服务器模块的完整目录结构，从顶层server节点开始，向下展开为util、embedded、persistence等20个主要子模块。每个子模块进一步细化到具体实现类，如util模块包含RateLimiter.java等17个工具类，quorum模块包含flexible子模块和FollowerBean.java等58个集群相关类。整体结构层次清晰，严格遵循路径关系，共呈现了服务器模块的200+个关键组件及其从属关系。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [util](util/_module.md) | package | RateLimiter控制请求速率；LogChopper处理事务日志；ZxidUtils解析zxid；KerberosUtil获取Kerberos领域；AdHash维护64位哈希；BitHashSet高效存储整数；ConfigUtils处理配置；OSMXBean获取系统信息；AuthUtil处理认证；JvmPauseMonitor监控JVM暂停；QuotaMetricsUtils处理配额指标；VerifyingFileFactory验证文件路径；BitMap管理位图；SerializeUtils序列化数据；CircularBuffer实现循环缓冲区；MessageTracker跟踪消息；RequestPathMetricsCollector分析请求路径。 |
| [DataTreeBean.java](DataTreeBean.md) | file | DataTreeBean类实现DataTreeMXBean和ZKMBeanInfo接口，封装DataTree操作，提供节点数、数据大小、临时节点数、监视数等统计信息，并返回名称、可见性和最后处理的Zxid。 |
| [ZooKeeperThread.java](ZooKeeperThread.md) | file | ZooKeeperThread继承Thread类，自定义未捕获异常处理，记录线程名和异常信息。 |
| [NettyServerCnxnFactory.java](NettyServerCnxnFactory.md) | file | NettyServerCnxnFactory是ZooKeeper的Netty网络连接工厂，支持SSL/TLS和明文连接，提供连接管理、流量控制和证书验证功能。 |
| [AuthenticationHelper.java](AuthenticationHelper.md) | file | AuthenticationHelper类用于管理ZooKeeper认证配置，检查连接认证状态，支持SASL等认证方案，未认证则关闭连接。 |
| [metric](metric/_module.md) | package | SimpleCounter是线程安全计数器类。AvgMinMaxCounter提供统计功能，记录总和、最小、最大值。AvgMinMaxPercentileCounter扩展统计功能，支持百分位数。AvgMinMaxCounterSet管理多个AvgMinMaxCounter实例。AvgMinMaxPercentileCounterSet管理多个AvgMinMaxPercentileCounter实例。Metric是度量功能基类。SimpleCounterSet管理一组计数器。 |
| [command](command/_module.md) | package | Zookeeper四字母命令实现类集合，包括命令管理、执行器及多个具体命令类如状态查询、环境变量、监控等，通过抽象基类统一处理流程，支持白名单控制。 |
| [controller](controller/_module.md) | package | ControllerServerConfig类继承QuorumPeerConfig，用于配置控制器和ZooKeeper服务器参数。ControlCommand定义12种控制器操作枚举及URI解析方法。ControllableConnection继承NIOServerCnxn，受控管理连接行为。CommandClient是线程安全客户端，支持发送命令并处理响应。ControllableConnectionFactory扩展NIOServerCnxnFactory，模拟网络异常测试场景。ZooKeeperServerController管理服务器核心功能，支持多种控制命令。ControllerService独立管理服务生命周期。CommandListener监听处理HTTP命令请求。 |
| [BlueThrottle.java](BlueThrottle.md) | file | BlueThrottle类实现连接限流功能，支持令牌桶算法和BLUE随机限流，可配置不同类型会话的权重和限流参数。 |
| [ServerWatcher.java](ServerWatcher.md) | file | 接口ServerWatcher扩展Watcher，定义方法process，接收WatchedEvent和ACL列表参数。 |
| [auth](auth/_module.md) | package | ZooKeeper认证模块包含多个实现：SASL处理回调、Kerberos解析、摘要认证、IP认证、X509证书验证等。各类通过AuthenticationProvider接口提供统一认证方案，支持超级用户、ACL匹配和日志记录。 |
| [PrepRequestProcessor.java](PrepRequestProcessor.md) | file | PrepRequestProcessor是ZooKeeper的请求预处理线程，继承ZooKeeperCriticalThread并实现RequestProcessor接口。主要功能包括：处理各类操作请求（创建/删除节点、设置数据等），维护变更记录，支持ACL验证和事务处理，处理会话创建/关闭，支持多操作事务，以及摘要计算功能。通过LinkedBlockingQueue接收请求，处理后交给下一处理器。关键方法包括run()循环处理请求、pRequest()核心处理逻辑、pRequest2Txn()将请求转为事务。 |
| [ZooKeeperCriticalThread.java](ZooKeeperCriticalThread.md) | file | ZooKeeperCriticalThread是ZooKeeperThread的子类，用于处理关键线程异常。当未捕获异常发生时，记录错误日志、通知监听器停止服务并增加错误计数。 |
| [DigestCalculator.java](DigestCalculator.md) | file | DigestCalculator类用于计算节点摘要，包含路径、数据和状态字段，使用CRC32校验和。支持版本控制，对特定路径禁用摘要检查。 |
| [ServerStats.java](ServerStats.md) | file | ServerStats类用于统计服务器性能数据，包括收发数据包、请求延迟、连接数、认证失败等指标，并提供获取和重置方法。 |
| [ZooKeeperServerMXBean.java](ZooKeeperServerMXBean.md) | file | ZooKeeper服务器监控接口，提供端口、版本、启动时间、请求延迟、连接数、会话超时、流量控制等关键指标及配置方法。 |
| [EphemeralType.java](EphemeralType.md) | file | EphemeralType枚举定义节点临时类型：VOID非临时，NORMAL标准临时，CONTAINER容器节点，TTL带TTL的节点（最长34年）。支持扩展类型检查、服务器ID验证及TTL校验。 |
| [DataNode.java](DataNode.md) | file | DataNode类表示数据节点，包含数据、ACL、状态和子节点集合。提供同步方法管理子节点，支持序列化和反序列化操作。摘要缓存优化性能。 |
| [ContainerManager.java](ContainerManager.md) | file | ContainerManager类管理ZK容器，定时检查并删除空容器，支持设置检查间隔、每分钟最大删除数和未使用容器保留时间。提供启动、停止和手动检查功能。 |
| [Stats.java](Stats.md) | file | Stats接口提供连接统计信息，包括建立时间、请求数、收发包数、延迟数据、最后操作及响应时间等，支持重置计数器。 |
| [ZooKeeperServerListenerImpl.java](ZooKeeperServerListenerImpl.md) | file | ZooKeeperServerListenerImpl类实现监听器接口，包含zkServer实例，在线程停止时记录日志并更新zkServer状态为ERROR。 |
| [RequestProcessor.java](RequestProcessor.md) | file | 接口RequestProcessor定义请求处理器，包含处理请求方法processRequest和关闭方法shutdown，处理时可能抛出RequestProcessorException异常。 |
| [ByteBufferInputStream.java](ByteBufferInputStream.md) | file | ByteBufferInputStream继承InputStream，封装ByteBuffer实现读取、跳过字节功能，提供剩余字节数查询，支持将ByteBuffer数据反序列化为Record对象。 |
| [DumbWatcher.java](DumbWatcher.md) | file | DumbWatcher类继承ServerCnxn，记录最近事件路径、类型和zxid，提供空实现方法，用于测试或简单监控场景。 |
| [NodeHashMap.java](NodeHashMap.md) | file | NodeHashMap接口提供节点管理功能，包括增删查改、清空、获取大小和条目集合，支持带/不带摘要更新，提供变更前后钩子及摘要获取。 |
| [ZooKeeperSaslServer.java](ZooKeeperSaslServer.md) | file | ZooKeeperSaslServer类实现SASL服务器功能，包含创建SASL服务器、响应评估及状态检查方法，使用同步机制确保线程安全。 |
| [Request.java](Request.md) | file | ZooKeeper请求类，包含会话ID、请求类型、事务处理、连接状态检查及序列化功能，支持多种操作类型验证和延迟监控。 |
| [SessionTracker.java](SessionTracker.md) | file | SessionTracker接口用于管理会话，包括创建、跟踪、提交、关闭和检查会话状态，支持本地和全局会话，提供会话超时和所有权设置功能。 |
| [ExitCode.java](ExitCode.md) | file | ExitCode枚举定义了程序退出代码：0正常结束，1意外错误，2无效参数，3无法访问数据目录，4启动管理服务器失败，10快照IO错误，12事务提交不匹配，13仲裁包错误，14无法绑定仲裁端口。 |
| [ConnectionMXBean.java](ConnectionMXBean.md) | file | ConnectionMXBean接口提供客户端连接管理功能，包括获取IP、会话ID、起止时间、收发数据包数、延迟统计及终止会话或连接的方法。 |
| [ObserverBean.java](ObserverBean.md) | file | ObserverBean是ZooKeeperServerBean的子类，实现ObserverMXBean接口，用于管理Observer实例。提供获取待验证请求数、仲裁地址和主节点信息的方法，并支持设置主节点。 |
| [SnapshotFormatter.java](SnapshotFormatter.md) | file | SnapshotFormatter类用于处理ZooKeeper快照文件，支持命令行参数-d和-json分别输出节点数据和JSON格式信息，包含节点详情、会话统计及数据校验功能。 |
| [SnapshotRecursiveSummary.java](SnapshotRecursiveSummary.md) | file | 公开类SnapshotRecursiveSummary用于递归统计Zookeeper快照数据。通过指定起始节点和最大深度，输出节点路径、子节点数和数据量。使用方式：SnapshotRecursiveSummary 快照文件 起始节点 最大深度。 |
| [ResponseCache.java](ResponseCache.md) | file | 响应缓存类，默认大小400，支持LRU策略，线程安全，可存储路径、数据和状态，状态变更时自动失效。 |
| [ClientCnxnLimitException.java](ClientCnxnLimitException.md) | file | ClientCnxnLimitException是继承Exception的自定义异常，用于连接被限流拒绝时抛出，包含默认错误信息。 |
| [RequestRecord.java](RequestRecord.md) | file | 接口RequestRecord提供静态方法从ByteBuffer、字节数组或Record创建实例，支持读取记录或字节数组，并获取数据长度限制。 |
| [ZooKeeperServerConf.java](ZooKeeperServerConf.md) | file | ZooKeeperServerConf类定义了ZooKeeper服务器配置，包含客户端端口、数据目录、会话超时等参数，并提供获取方法和转换为Map的功能。 |
| [RequestThrottler.java](RequestThrottler.md) | file | RequestThrottler是ZooKeeper的请求限流线程，通过队列管理请求提交，支持最大请求数、等待时间和丢弃过期请求等配置，确保系统稳定运行。 |
| [UnimplementedRequestProcessor.java](UnimplementedRequestProcessor.md) | file | 未实现的请求处理器类，处理请求时设置未实现异常并发送响应，若失败则抛出异常，最后关闭会话。shutdown方法为空。 |
| [SnapshotComparer.java](SnapshotComparer.md) | file | SnapshotComparer类用于比较两个Zookeeper快照文件，支持字节和节点数阈值过滤，提供交互式或批量分析模式，输出差异节点信息。 |
| [ReferenceCountedACLCache.java](ReferenceCountedACLCache.md) | file | ReferenceCountedACLCache类实现ACL列表与长整型ID的双向映射，支持引用计数、序列化及清理未使用项。使用同步方法确保线程安全，包含OPEN_UNSAFE_ACL_ID特殊处理。 |
| [TxnLogProposalIterator.java](TxnLogProposalIterator.md) | file | TxnLogProposalIterator是用于迭代事务日志提案的类，实现Iterator接口。主要功能包括检查是否有下一个提案(hasNext)、获取下一个提案(next)并关闭资源(close)。提案的请求部分为null，不用于跟随者同步。处理IO异常并记录错误。不支持remove操作。构造函数初始化迭代器状态。 |
| [ZooKeeperServerListener.java](ZooKeeperServerListener.md) | file | ZooKeeperServerListener接口定义了notifyStopping方法，用于在关键线程因致命错误停止时通知服务器，参数包括线程名和错误码。 |
| [ServerCnxn.java](ServerCnxn.md) | file | ServerCnxn是ZooKeeper服务器连接抽象类，管理连接状态、请求处理、响应缓存及统计信息，支持多种断开原因枚举，提供会话超时、认证、流量控制等功能。 |
| [DatadirCleanupManager.java](DatadirCleanupManager.md) | file | DatadirCleanupManager类管理数据目录清理任务，通过定时器定期删除旧快照和日志，保留指定数量的最新快照。提供启动、关闭和状态查询功能。 |
| [FinalRequestProcessor.java](FinalRequestProcessor.md) | file | FinalRequestProcessor是ZooKeeper的请求处理器，处理各类操作如创建、删除节点等，包含会话管理、ACL检查、性能统计等功能，确保请求正确执行并返回响应。 |
| [ZooKeeperServer.java](ZooKeeperServer.md) | file | ZooKeeperServer是ZooKeeper核心服务类，负责会话管理、请求处理、ACL检查、配额控制等核心功能。支持动态配置、JMX监控、快照恢复，内置多种请求处理器链。关键特性包括：会话追踪、事务处理、请求限流、SASL认证、数据树维护。 |
| [TraceFormatter.java](TraceFormatter.md) | file | Java类TraceFormatter用于解析和格式化跟踪文件，读取二进制数据并输出包含时间、ID、操作类型等信息的日志。需传入一个文件路径参数，否则报错退出。 |
| [DataTreeMXBean.java](DataTreeMXBean.md) | file | DataTreeMXBean接口提供数据树节点数、最新zxid、监视数、数据大小和临时节点数的方法。 |
| [DataTree.java](DataTree.md) | file | DataTree是ZooKeeper的核心数据结构，用于存储所有节点数据。主要功能包括：节点增删改查、ACL管理、配额控制、会话管理、数据变更监听。关键特性：支持临时节点、容器节点、TTL节点；通过哈希表快速查找节点；维护节点大小统计；提供数据校验机制；支持事务处理与快照序列化。 |
| [TxnLogEntry.java](TxnLogEntry.md) | file | TxnLogEntry类包含交易记录、头部和摘要，提供对应getter方法。 |
| [ZooTrace.java](ZooTrace.md) | file | ZooTrace类定义跟踪掩码常量，包含已废弃字段，提供设置、检查及日志记录功能，支持不同级别的跟踪输出。 |
| [ServerCnxnFactory.java](ServerCnxnFactory.md) | file | ServerCnxnFactory是ZooKeeper服务器连接工厂抽象类，管理连接数限制、会话映射、SSL配置及SASL认证，提供连接创建、配置和关闭功能。 |
| [NodeHashMapImpl.java](NodeHashMapImpl.md) | file | NodeHashMapImpl实现基于ConcurrentHashMap的节点存储，支持摘要计算功能，提供增删改查及摘要更新方法，排除特定路径的摘要处理。 |
| [ExpiryQueue.java](ExpiryQueue.md) | file | ExpiryQueue是一个基于时间间隔的过期队列，使用两个ConcurrentHashMap分别存储元素与过期时间、过期时间与元素集合。提供添加、移除、更新元素过期时间功能，并通过轮询机制清理过期元素。 |
| [NIOServerCnxn.java](NIOServerCnxn.md) | file | NIOServerCnxn是ZooKeeper的NIO连接处理类，管理客户端通信，包括读写请求、会话超时和连接关闭。核心功能包括异步IO处理、四字命令响应、会话管理和安全控制。 |
| [ByteBufferRequestRecord.java](ByteBufferRequestRecord.md) | file | ByteBufferRequestRecord类实现RequestRecord接口，封装ByteBuffer请求数据，提供读取记录、字节数组和缓冲区大小的方法，支持懒加载和线程安全。 |
| [RateLogger.java](RateLogger.md) | file | RateLogger类用于限速日志记录，支持消息和值的记录，按时间间隔合并重复日志，提供flush方法强制输出。 |
| [SessionTrackerImpl.java](SessionTrackerImpl.md) | file | SessionTrackerImpl是ZooKeeper的会话跟踪实现，管理会话生命周期，包括创建、更新、过期和移除。使用ConcurrentHashMap存储会话，ExpiryQueue处理过期。支持会话ID生成、超时设置和状态检查。 |
| [SimpleRequestRecord.java](SimpleRequestRecord.md) | file | SimpleRequestRecord类实现了RequestRecord接口，封装Record对象并提供读取记录、字节数组及限制大小的方法。通过序列化Record生成字节数组，处理IO异常并返回结果。 |
| [WorkerService.java](WorkerService.md) | file | WorkerService管理可配置数量的工作线程，支持直接执行或线程池调度任务。提供任务调度、优雅停止及超时控制功能，支持可分配线程或共享线程池模式。 |
| [NettyServerCnxn.java](NettyServerCnxn.md) | file | NettyServerCnxn是ZooKeeper的Netty连接实现类，管理客户端连接、消息处理、流量控制及会话状态。核心功能包括消息接收/发送、连接关闭、四字命令处理、SSL握手状态管理，并集成到NettyServerCnxnFactory进行统一管理。 |
| [ZooKeeperServerMain.java](ZooKeeperServerMain.md) | file | ZooKeeperServerMain是ZooKeeper服务器的主类，负责启动和管理服务器。支持加密和非加密连接，处理配置、数据目录访问等异常，包含连接工厂、容器管理和监控功能。提供正常和异常关闭逻辑。 |
| [ConnectionBean.java](ConnectionBean.md) | file | ConnectionBean类实现ConnectionMXBean和ZKMBeanInfo接口，管理ZooKeeper服务器连接，包含会话ID、客户端IP、统计数据和操作控制方法。 |
| [ServerConfig.java](ServerConfig.md) | file | ServerConfig类用于配置ZooKeeper服务器参数，包括端口地址、数据目录、会话超时、JVM监控阈值等。提供解析命令行参数和配置文件的方法，并支持从QuorumPeerConfig读取配置。包含多个getter方法获取配置值。 |
| [PurgeTxnLog.java](PurgeTxnLog.md) | file | PurgeTxnLog类用于清理事务日志和快照，保留指定数量（至少3个）的最新文件。提供命令行工具，需指定日志目录、快照目录及保留数量。 |
| [ServerMetrics.java](ServerMetrics.md) | file | ServerMetrics类用于跟踪服务器端性能指标，包含各类计数器、时间统计和状态监控，如读写延迟、快照时间、连接数等，支持测试实例和动态初始化。 |
| [EphemeralTypeEmulate353.java](EphemeralTypeEmulate353.md) | file | EphemeralTypeEmulate353枚举定义四种节点类型：非临时(VOID)、标准临时(NORMAL)、容器(CONTAINER)和TTL节点。提供方法根据所有者ID判断类型，及TTL值转换方法。包含常量MAX_TTL和掩码TTL_MASK。 |
| [ZooKeeperServerBean.java](ZooKeeperServerBean.md) | file | ZooKeeperServerBean类实现ZooKeeperServerMXBean和ZKMBeanInfo接口，提供服务器状态监控和管理功能，包括连接、请求延迟、会话超时、数据目录大小等统计信息，支持配置调整如TickTime、客户端连接数限制等。 |
| [ServerCnxnHelper.java](ServerCnxnHelper.md) | file | ServerCnxnHelper类提供获取ZooKeeper最大连接数的方法，优先检查普通连接工厂，其次安全连接工厂，默认返回预设值。 |
| [ZooKeeperServerShutdownHandler.java](ZooKeeperServerShutdownHandler.md) | file | ZooKeeperServerShutdownHandler类通过CountDownLatch在服务器状态变为ERROR或SHUTDOWN时触发关闭操作。 |
| [SyncRequestProcessor.java](SyncRequestProcessor.md) | file | SyncRequestProcessor是ZooKeeper的请求处理线程，负责日志同步和快照生成。它管理请求队列，根据日志条目数或大小触发快照，并控制批量写入磁盘。支持延迟刷新和批量处理优化，确保数据一致性。 |
| [NIOServerCnxnFactory.java](NIOServerCnxnFactory.md) | file | NIOServerCnxnFactory是ZooKeeper的NIO连接工厂类，负责管理客户端连接。核心功能包括：1. 使用AcceptThread接收新连接并分配给SelectorThread；2. SelectorThread处理I/O事件；3. 支持连接数限制和超时管理；4. 可配置线程数和缓冲区大小。 |
| [ZKDatabase.java](ZKDatabase.md) | file | ZKDatabase是ZooKeeper的核心数据库类，负责管理数据树、会话和事务日志。主要功能包括初始化、清理、加载/保存快照、处理事务、维护提交日志、管理ACL和监视器。支持事务日志同步、快速同步和截断操作，并提供数据节点、会话和子节点查询接口。通过FileTxnSnapLog处理磁盘持久化，确保数据一致性和高效恢复。 |
| [watch](watch/_module.md) | package | WatcherOrBitSet类封装Watcher集合或位集合操作。WatcherMode枚举定义三种监视模式。WatchManagerOptimized高效管理监视器，线程安全。WatchesPathReport管理路径与会话映射。WatchManager实现监视器管理接口。WatchManagerFactory创建监视管理器实例。IDeadWatcherListener处理死监视器。WatcherCleaner异步清理死监视器。WatchesSummary统计监视数据。WatchStats管理监听状态。IWatchManager定义核心监视功能。PathParentIterator遍历路径及父路径。WatchesReport管理会话与路径映射。 |
| [quorum](quorum/_module.md) | package | Zookeeper集群核心模块，包含选举、同步、认证、会话管理等组件，支持领导者选举、法定人数验证、SASL安全认证、请求处理链等功能，确保分布式一致性与高可用性。 |
| [admin](admin/_module.md) | package | GetCommand抽象类继承CommandBase，支持多种初始化方式。AuthRequest类封装权限验证信息。CommandBase是命令基类，提供核心属性和方法。ReadAheadEndpoint实现预读功能。CommandOutputter接口处理命令输出。Commands类管理命令注册与执行。DummyAdminServer是空实现。StreamOutputter处理二进制流输出。AdminServer接口定义管理服务器功能。CommandResponse封装响应数据。UnifiedConnectionFactory处理SSL和非SSL连接。AdminServerFactory创建AdminServer实例。JettyAdminServer基于Jetty实现管理服务器。Command接口定义命令模式结构。JsonOutputter将响应转为JSON。PostCommand抽象类继承CommandBase。 |
| [persistence](persistence/_module.md) | package | FileTxnSnapLog管理ZK事务日志和快照，含恢复、重放、截断功能。SnapStream处理压缩校验流。TxnLog定义日志操作接口。SnapShot定义快照反序列化等核心功能。FileSnap实现快照管理。TxnLogToolkit提供日志修复截断工具。FileTxnLog实现日志读写。SnapshotInfo存储快照元数据。FilePadding处理文件预分配。Util提供文件管理工具方法。 |
| [embedded](embedded/_module.md) | package | ExitHandler枚举定义退出行为：EXIT终止进程，LOG_ONLY仅记录错误（测试用）。ZooKeeperServerEmbedded接口提供嵌入式服务器功能，含构建器配置目录、属性和退出处理器，支持启动、连接获取和关闭。ZooKeeperServerEmbeddedImpl实现类支持集群/单机模式，处理配置、启动、清理和关闭。 |


