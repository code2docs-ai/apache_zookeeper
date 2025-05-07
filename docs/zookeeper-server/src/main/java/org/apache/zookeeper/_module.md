# 基础信息

|      |      |
|------|------|
| 名称 | zookeeper |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper |
| 包名 | zookeeper.docs.zookeeper-server.src.main.java.org.apache.zookeeper |
| 概述说明 | ZooKeeper核心模块提供分布式协调服务，包含服务端工具、客户端管理、安全通信、监控指标等组件。支持节点CRUD、ACL权限、SASL认证、JMX监控及事务处理，采用多线程和异步回调机制，确保高可用与数据一致性。 |

# 说明

## 概述  
该模块是ZooKeeper分布式协调服务的核心实现，提供客户端连接管理、安全认证、节点操作和集群协调功能。主要接口包括Java API（如ZooKeeperAdmin）、CLI命令和JMX管理接口，关键数据结构含Zxid排序的日志文件、并发映射管理的会话和节点树。依赖JVM系统库、Netty框架和Kerberos认证模块，例如通过FileTxnSnapLog实现WAL机制。例如SecurityUtils通过Subject实现多因子认证，类似门禁系统的权限控制。

## 主要业务场景  
支持集群选举、事务日志处理、节点CRUD等核心流程，例如QuorumVerifier实现投票仲裁。采用多线程链式处理（如PrepRequestProcessor）和异步回调机制，功能覆盖从ACL校验到JMX监控。主要用于分布式锁、配置管理等场景，提供Builder模式API与JettyAdminServer集成。第三方可通过四字母命令（如"stat"）或HTTP接口获取指标，类似Prometheus的监控方案。例如ZooKeeperMain通过JLine实现交互式命令行补全。


### 包内部结构视图

```mermaid
graph TD
    zookeeper --> util
    zookeeper --> client
    zookeeper --> metrics
    zookeeper --> compat
    zookeeper --> server
    zookeeper --> admin
    zookeeper --> cli
    zookeeper --> jmx
    zookeeper --> audit
    
    util --> CircularBlockingQueue.java
    util --> SecurityUtils.java
    util --> PemReader.java
    util --> ServiceUtils.java
    
    client --> FourLetterWordMain.java
    client --> ChrootCreateCallback.java
    client --> ChrootWatcher.java
    client --> ZooKeeperOptions.java
    client --> HostProvider.java
    client --> StaticHostProvider.java
    client --> Chroot.java
    client --> ZooKeeperBuilder.java
    client --> ZKClientConfig.java
    client --> ZooKeeperSaslClient.java
    client --> ConnectStringParser.java
    
    metrics --> impl
    metrics --> MetricsContext.java
    metrics --> SummarySet.java
    metrics --> Summary.java
    metrics --> CounterSet.java
    metrics --> Gauge.java
    metrics --> GaugeSet.java
    metrics --> MetricsProvider.java
    metrics --> MetricsProviderLifeCycleException.java
    metrics --> Counter.java
    
    impl --> DefaultMetricsProvider.java
    impl --> MetricsProviderBootstrap.java
    impl --> NullMetricsProvider.java
    
    compat --> ProtocolManager.java
    
    server --> util
    server --> embedded
    server --> persistence
    server --> quorum
    server --> auth
    server --> watch
    server --> command
    server --> controller
    server --> metric
    
    server --> ZooKeeperThread.java
    server --> DataTreeBean.java
    server --> AuthenticationHelper.java
    server --> NettyServerCnxnFactory.java
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
    
    controller --> ControllerServerConfig.java
    controller --> ControlCommand.java
    controller --> ControllableConnection.java
    controller --> CommandClient.java
    controller --> ControllableConnectionFactory.java
    controller --> ZooKeeperServerController.java
    controller --> ControllerService.java
    controller --> CommandListener.java
    
    metric --> SimpleCounter.java
    metric --> AvgMinMaxCounter.java
    metric --> AvgMinMaxPercentileCounter.java
    metric --> AvgMinMaxCounterSet.java
    metric --> AvgMinMaxPercentileCounterSet.java
    metric --> Metric.java
    metric --> SimpleCounterSet.java
    
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
    
    auth --> SaslServerCallbackHandler.java
    auth --> SASLAuthenticationProvider.java
    auth --> KerberosName.java
    auth --> DigestAuthenticationProvider.java
    auth --> AuthenticationProvider.java
    auth --> WrappedAuthenticationProvider.java
    auth --> ProviderRegistry.java
    auth --> ServerAuthenticationProvider.java
    auth --> DigestLoginModule.java
    auth --> KeyAuthenticationProvider.java
    auth --> IPAuthenticationProvider.java
    auth --> EnsembleAuthenticationProvider.java
    auth --> X509AuthenticationProvider.java
    
    cli --> ListQuotaCommand.java
    cli --> GetConfigCommand.java
    cli --> DelQuotaCommand.java
    cli --> DeleteAllCommand.java
    cli --> CloseCommand.java
    cli --> GetCommand.java
    cli --> CliWrapperException.java
    cli --> LsCommand.java
    cli --> PlainOutputFormatter.java
    cli --> CliException.java
    cli --> GetAclCommand.java
    cli --> Base64OutputFormatter.java
    cli --> CommandFactory.java
    cli --> AddAuthCommand.java
    cli --> OutputFormatter.java
    cli --> WhoAmICommand.java
    cli --> GetEphemeralsCommand.java
    cli --> CommandNotFoundException.java
    cli --> StatCommand.java
    cli --> VersionCommand.java
    cli --> MalformedCommandException.java
    cli --> MalformedPathException.java
    cli --> SetAclCommand.java
    cli --> DeleteCommand.java
    cli --> ReconfigCommand.java
    cli --> AclParser.java
    cli --> CliParseException.java
    cli --> AddWatchCommand.java
    cli --> CliCommand.java
    cli --> RemoveWatchesCommand.java
    cli --> CreateCommand.java
    cli --> StatPrinter.java
    cli --> SetCommand.java
    cli --> SetQuotaCommand.java
    cli --> CommandUsageHelper.java
    cli --> HexDumpOutputFormatter.java
    cli --> GetAllChildrenNumberCommand.java
    cli --> SyncCommand.java
    
    jmx --> MBeanRegistry.java
    jmx --> ManagedUtil.java
    jmx --> ZKMBeanInfo.java
    
    audit --> AuditHelper.java
    audit --> AuditConstants.java
    audit --> AuditLogger.java
    audit --> AuditEvent.java
    audit --> Slf4jAuditLogger.java
    audit --> ZKAuditProvider.java
```

该流程图展示了ZooKeeper项目的核心目录结构，从顶层zookeeper节点开始，向下展开为util、client、metrics等主要模块。每个模块进一步细分为子模块和具体实现类，如server模块包含quorum、persistence等关键子系统，其中quorum又分为flexible选举算法和auth认证等组件。整体结构清晰呈现了ZooKeeper的分布式协调服务架构，包括服务端核心逻辑、客户端交互、监控指标、命令行工具等完整功能体系，层级深度达5层，完整覆盖了源码中所有重要组件及其关联关系。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [CreateMode.java](CreateMode.md) | file | CreateMode枚举定义了ZooKeeper节点类型：持久、临时、容器及带TTL的变体，支持顺序编号，提供标志转换方法。 |
| [common](common/_module.md) | package | StandardTypeFileKeyStoreLoader抽象类支持JKS/PKCS12/BCFKS密钥库加载。NetUtils处理网络地址格式化。ZKConfig管理ZooKeeper配置。PEMFileLoader加载PEM格式密钥库。SecretUtils安全读取文件秘密。KeyStoreFileType枚举定义密钥库类型。SSLContextAndOptions封装SSL配置。ZKTrustManager实现证书验证。StringUtils提供字符串操作。QuorumX509Util处理仲裁SSL配置。FileKeyStoreLoader为密钥库加载抽象类。PKCS12FileLoader加载PKCS12格式。Time类处理时间操作。PathUtils验证路径有效性。AtomicFileWritingIdiom原子化文件写入。BCFKSFileLoader加载BCFKS格式。NettyUtils提供Netty工具方法。FileChangeWatcher监控文件变化。KeyStoreLoader定义密钥库加载接口。X509Exception为证书异常类。PathTrie实现路径前缀树。ZKHostnameVerifier验证主机名。JKSFileLoader加载JKS格式。X509Util处理X.509证书。ClientX509Util处理客户端SSL配置。IOUtils提供IO流工具。AtomicFileOutputStream原子化文件输出。FileKeyStoreLoaderBuilderProvider根据类型返回Builder。 |
| [DeleteContainerRequest.java](DeleteContainerRequest.md) | file | 删除容器请求类，包含路径属性，提供序列化和反序列化方法。 |
| [ClientCnxnSocket.java](ClientCnxnSocket.md) | file | ClientCnxnSocket是ZooKeeper客户端网络通信抽象类，管理连接状态、数据包收发、超时检测及会话ID。包含缓冲区管理、计数器统计、连接初始化及清理方法，支持SASL认证和异步传输。 |
| [jmx](jmx/_module.md) | package | MBeanRegistry管理MBean注册，线程安全，支持路径映射和测试替换。ManagedUtil提供Log4j JMX管理，检查启用状态并注册MBean。ZKMBeanInfo接口控制MBean可见性，定义名称和隐藏属性。 |
| [AsyncCallback.java](AsyncCallback.md) | file | AsyncCallback接口定义了多种异步回调方法，用于处理ZooKeeper节点操作结果，包括状态、数据、ACL、子节点等，支持成功和失败处理。 |
| [ZooDefs.java](ZooDefs.md) | file | ZooDefs类定义Zookeeper常量：节点路径、操作码、权限和ID。包含CONFIG_NODE、ZOOKEEPER_NODE_SUBTREE路径；OpCode定义25种操作如create、delete；Perms定义5种权限如READ、WRITE；Ids定义ACL如OPEN_ACL_UNSAFE、CREATOR_ALL_ACL；AddWatchModes定义两种监视模式。 |
| [ClientCnxn.java](ClientCnxn.md) | file | ClientCnxn是ZooKeeper客户端核心类，负责管理连接、会话、请求队列和事件处理。主要功能包括：维护连接状态（CONNECTED/READONLY等）、处理认证信息、分批次重注册监听器（SET_WATCHES_MAX_LENGTH=128KB）、管理请求队列（pendingQueue/outgoingQueue）、通过SendThread/EventThread异步处理网络IO和事件回调。关键特性：支持SASL认证、会话超时控制、读写分离模式、请求超时机制。 |
| [ClientWatchManager.java](ClientWatchManager.md) | file | 客户端观察管理器接口，定义materialize方法，返回需通知的观察者集合，调用方负责实际通知，内部状态会更新。参数包括状态、类型和路径，返回非空集合。 |
| [StatsTrack.java](StatsTrack.md) | file | StatsTrack类用于统计配额数据，支持节点数和字节数的软硬限制设置与获取，通过字符串或字节数组初始化，提供toString方法生成兼容旧版本的统计字符串。 |
| [Login.java](Login.md) | file | Kerberos登录类，管理TGT刷新线程，支持票据缓存和定时续订，确保认证不过期。包含登录、注销、续订逻辑，处理时钟同步和异常情况。 |
| [Watcher.java](Watcher.md) | file | Watcher接口定义了ZooKeeper事件状态(KeeperState)、事件类型(EventType)和监视器类型(WatcherType)的枚举，并提供整数转换方法。核心功能是处理WatchedEvent事件。 |
| [CreateOptions.java](CreateOptions.md) | file | CreateOptions类用于创建节点配置，包含创建模式、ACL列表和TTL属性，通过Builder模式构建实例并验证参数。 |
| [ServerAdminClient.java](ServerAdminClient.md) | file | ServerAdminClient类提供与服务器交互的功能，包括ruok、stat、dump、kill、setTraceMask和getTraceMask操作，通过Socket连接执行命令并返回结果。 |
| [ZKWatchManager.java](ZKWatchManager.md) | file | ZKWatchManager管理ZooKeeper客户端监听器，包含数据、子节点、持久化等五种监听类型，支持添加、移除和验证监听器，并在事件触发时生成对应监听器集合。 |
| [Environment.java](Environment.md) | file | Java类Environment用于收集系统环境信息，包括Java版本、主机名、操作系统、用户信息和JVM内存等，并提供日志记录功能。 |
| [WatchDeregistration.java](WatchDeregistration.md) | file | WatchDeregistration类用于取消ZooKeeper路径上的监听器注册，包含路径、监听器、类型等属性，提供取消注册和获取路径的方法。 |
| [Shell.java](Shell.md) | file | Shell类提供Unix命令执行功能，包括用户、组、权限管理命令，支持超时控制和环境设置，可执行命令并处理输出。 |
| [Version.java](Version.md) | file | 这是一个ZooKeeper版本信息类，提供获取版本号、修订哈希、构建日期的方法，支持命令行参数输出不同格式的版本信息。 |
| [Transaction.java](Transaction.md) | file | Transaction类用于ZooKeeper事务操作，支持创建、删除、检查和设置数据操作，提供同步和异步提交方法。 |
| [DigestWatcher.java](DigestWatcher.md) | file | 摘要：DigestWatcher接口定义方法process，在zxid摘要不匹配时触发，参数为不匹配的zxid。 |
| [ClientCnxnSocketNIO.java](ClientCnxnSocketNIO.md) | file | ClientCnxnSocketNIO类实现NIO客户端连接，处理读写、连接管理及SASL认证，包含socket创建、IO操作、数据包发送和清理功能。 |
| [Testable.java](Testable.md) | file | Testable接口定义了测试ZooKeeper的方法：注入会话过期、插入事件到队列、关闭客户端连接套接字。 |
| [ZKUtil.java](ZKUtil.md) | file | ZKUtil类提供ZooKeeper工具方法，包括递归删除节点、批量删除、BFS/DFS遍历子树、权限字符串转换及ACL格式化功能。支持同步/异步操作，批量删除优化性能。 |
| [SaslServerPrincipal.java](SaslServerPrincipal.md) | file | SaslServerPrincipal类用于获取SASL客户端的服务器主体名称，支持配置和主机名规范化，包含测试用的地址包装类。 |
| [Op.java](Op.md) | file | 抽象类Op定义ZooKeeper操作，包含创建、删除、更新、检查、获取子节点和数据等方法，支持事务和读取操作类型。 |
| [ZookeeperBanner.java](ZookeeperBanner.md) | file | 这是一个Java类ZookeeperBanner，包含静态字符串数组BANNER存储ASCII艺术横幅，提供printBanner方法通过Logger逐行打印横幅内容。 |
| [ZooKeeperTestable.java](ZooKeeperTestable.md) | file | ZooKeeperTestable类实现Testable接口，包含注入会话过期、事件入队和关闭Socket功能，通过clientCnxn操作事件线程和状态。 |
| [KeeperException.java](KeeperException.md) | file | KeeperException是ZooKeeper的公共异常基类，包含多种错误代码和对应子类，用于处理系统错误、API错误和会话问题等。支持多请求结果存储和错误路径记录。 |
| [WatchedEvent.java](WatchedEvent.md) | file | WatchedEvent类用于封装监视事件，包含状态、类型、路径和事务ID，提供构造、转换和获取方法。 |
| [ZooKeeper.java](ZooKeeper.md) | file | ZooKeeper是一个分布式协调服务客户端类，提供节点创建、删除、数据读写、ACL管理等操作。支持同步/异步调用，可设置监听器感知节点变化。通过会话机制维持连接，支持自动重连和只读模式。包含多种构造方法，支持自定义配置和主机选择策略。 |
| [Quotas.java](Quotas.md) | file | Quotas类定义了ZooKeeper配额管理的常量和方法，包括配额路径、限制节点路径、状态节点路径的生成与解析。 |
| [ZooKeeperMain.java](ZooKeeperMain.md) | file | ZooKeeperMain是ZooKeeper命令行工具主类，提供连接管理、命令执行、历史记录等功能，支持交互式操作和脚本模式。 |
| [AddWatchMode.java](AddWatchMode.md) | file | AddWatchMode枚举定义两种ZooKeeper监听模式：PERSISTENT持久监听指定路径的数据和子节点事件，需手动移除；PERSISTENT_RECURSIVE递归监听该路径及其所有子节点，同样需手动移除但性能略低。两种模式都通过removeWatches()移除。 |
| [MultiResponse.java](MultiResponse.md) | file | MultiResponse类实现Record和Iterable接口，管理OpResult列表，支持序列化和反序列化操作响应，包括创建、删除、获取数据等类型，并重写equals和hashCode方法。 |
| [MultiOperationRecord.java](MultiOperationRecord.md) | file | MultiOperationRecord类实现Record和Iterable接口，管理Op操作列表，确保操作类型一致，支持序列化和反序列化，提供迭代、添加、大小查询及相等性检查功能。 |
| [OpResult.java](OpResult.md) | file | OpResult抽象类定义了操作结果基类，包含类型字段和getType方法。其子类包括CreateResult（含路径和状态）、DeleteResult（无额外数据）、SetDataResult（含状态）、CheckResult（无额外数据）、GetChildrenResult（含子节点列表）、GetDataResult（含数据和状态）、ErrorResult（含错误码）。每个子类实现equals和hashCode方法。 |
| [JLineZNodeCompleter.java](JLineZNodeCompleter.md) | file | JLineZNodeCompleter类实现ZooKeeper节点路径和命令的自动补全功能，通过解析输入路径或命令前缀匹配候选列表。 |
| [ClientCnxnSocketNetty.java](ClientCnxnSocketNetty.md) | file | ClientCnxnSocketNetty是ZooKeeper客户端基于Netty的网络连接实现，包含连接管理、数据包收发及SSL支持。核心功能包括连接建立、清理、数据读写及SASL认证处理，通过锁机制确保线程安全，支持异步IO操作。 |
| [SaslClientCallbackHandler.java](SaslClientCallbackHandler.md) | file | SaslClientCallbackHandler处理SASL回调，支持Name、Password、Realm和Authorize回调。若无密码会记录警告，提示Kerberos配置问题。 |
| [audit](audit/_module.md) | package | AuditHelper管理ZooKeeper审计日志，记录操作类型、路径等信息。AuditConstants定义操作类型常量。AuditLogger接口提供日志框架。AuditEvent存储日志条目。Slf4jAuditLogger用SLF4J记录日志。ZKAuditProvider管理审计功能，默认禁用，可配置。 |
| [cli](cli/_module.md) | package | ZooKeeper CLI命令类集合，包括配额管理(ListQuota/DelQuota/SetQuota)、节点操作(Get/Set/Delete/Create)、ACL管理(GetAcl/SetAcl)、监视器(AddWatch/RemoveWatches)、配置(GetConfig/Reconfig)等，均继承CliCommand，含参数解析与执行逻辑，处理各类异常。 |
| [admin](admin/_module.md) | package | ZooKeeperAdmin是ZooKeeper的扩展类，用于动态重配置操作，支持添加/移除服务器，提供同步和异步方法，可处理连接字符串、会话超时和监听器等参数。 |
| [server](server/_module.md) | package | ZooKeeper服务器核心模块集，包含工具集、嵌入式服务、事务日志管理、线程处理、认证授权、连接工厂、四字母命令、度量统计、控制平面、管理接口、仲裁系统、监视器管理、请求处理器、数据树、会话跟踪等组件。提供分布式协调、速率控制、日志切割、ID转换、性能监控、集群管理等功能，支持多种认证方式和安全协议，采用线程安全设计，适用于运维调试和性能分析场景。 |
| [compat](compat/_module.md) | package | ProtocolManager类处理ZooKeeper连接请求和响应的序列化与反序列化，支持新旧版本兼容性检查，特别处理readOnly字段。 |
| [metrics](metrics/_module.md) | package | Zookeeper指标采集系统，统一管理性能指标，支持动态扩展。核心包括MetricsProvider接口、线程安全指标容器及反射加载机制。涵盖指标全生命周期管理，提供SPI接口和测试隔离方案，用于服务端监控。 |
| [client](client/_module.md) | package | FourLetterWordMain类发送四字母命令到主机端口。ChrootCreateCallback处理回调路径。ChrootWatcher转换监视事件路径。ZooKeeperOptions封装客户端配置。HostProvider管理主机连接。StaticHostProvider实现主机列表管理。Chroot处理路径前缀。ZooKeeperBuilder构建客户端实例。ZKClientConfig配置客户端参数。ZooKeeperSaslClient实现SASL认证。ConnectStringParser解析连接字符串。 |
| [util](util/_module.md) | package | CircularBlockingQueue是线程安全循环队列，满时自动移除旧元素。SecurityUtils支持SASL客户端和服务器的GSSAPI和DIGEST-MD5认证。PemReader处理PEM格式证书和密钥，支持多种密钥类型。ServiceUtils提供JVM关闭策略，支持系统退出或仅记录日志。 |


