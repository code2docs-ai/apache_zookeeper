# 基础信息

|      |      |
|------|------|
| 名称 | cli |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper/cli |
| 包名 | zookeeper.docs.zookeeper-server.src.main.java.org.apache.zookeeper.cli |
| 概述说明 | ZooKeeper CLI命令类集合，包括配额管理(ListQuota/DelQuota/SetQuota)、节点操作(Get/Set/Delete/Create)、ACL管理(GetAcl/SetAcl)、监视器(AddWatch/RemoveWatches)、配置(GetConfig/Reconfig)等，均继承CliCommand，含参数解析与执行逻辑，处理各类异常。 |

# 说明

## 概述  
该模块是ZooKeeper命令行工具集，提供完整的集群管理功能，类似数据库客户端工具。核心职责包括节点CRUD操作、ACL权限管理、配额控制及集群配置变更等。主要接口遵循CLI规范，例如通过`parse`方法解析参数，`exec`方法执行命令。关键数据结构包括StatsTrack（统计信息）、ACL列表（权限控制）及配额节点（资源限制）。外部依赖ZooKeeper服务端API，例如通过ZKUtil进行递归删除操作。  

## 主要业务场景  
支持ZooKeeper节点全生命周期管理，例如`CreateCommand`支持创建临时/顺序节点，`DeleteCommand`处理版本化删除。交互模式以同步执行为主，部分命令（如`StatCommand`）支持监听模式异步回调。功能完整性覆盖ZooKeeper核心API，例如`GetAclCommand`实现标准ACL查询。典型场景包括集群运维（`ReconfigCommand`）、数据调试（`HexDumpOutputFormatter`）等。提供命令工厂模式（`CommandFactory`）动态加载接口，第三方可通过扩展`CliCommand`集成，例如IDE插件调用`LsCommand`展示节点树。


### 包内部结构视图

```mermaid
graph TD
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
```

该流程图展示了Zookeeper项目中cli目录下的所有Java文件结构。根节点为cli文件夹，直接包含34个不同类型的命令类、异常类和工具类文件，涵盖配额管理、ACL操作、数据同步等核心功能模块。所有文件均以.java结尾，呈现扁平化结构，无次级目录层级关系。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [GetConfigCommand.java](GetConfigCommand.md) | file | GetConfigCommand是CLI命令类，支持选项s（统计）、w（监控）、c（客户端连接字符串）。解析参数后从zk获取配置数据，根据选项输出配置或统计信息。监控模式下返回true。 |
| [ListQuotaCommand.java](ListQuotaCommand.md) | file | ListQuotaCommand是用于查询ZooKeeper配额信息的命令行工具，解析路径参数后获取配额数据和统计信息并输出。处理异常包括路径错误、节点不存在及ZK异常。 |
| [CliException.java](CliException.md) | file | CliException是自定义异常类，继承Exception，包含退出码属性，默认值为1，提供多种构造方法支持消息、原因及退出码设置，可获取退出码。 |
| [PlainOutputFormatter.java](PlainOutputFormatter.md) | file | PlainOutputFormatter类实现OutputFormatter接口，提供字节数组到UTF-8字符串的简单格式化功能，并通过单例模式提供全局实例。 |
| [CliWrapperException.java](CliWrapperException.md) | file | CliWrapperException继承自CliException，封装KeeperException异常，根据不同类型返回对应错误信息，如节点不存在、无权限、参数无效等。 |
| [CloseCommand.java](CloseCommand.md) | file | CloseCommand是CliCommand的子类，用于关闭zk连接。构造函数初始化命令名，parse方法直接返回自身，exec方法执行关闭操作并处理异常。 |
| [DeleteAllCommand.java](DeleteAllCommand.md) | file | DeleteAllCommand类继承CliCommand，用于递归删除ZooKeeper节点路径。支持-b参数指定批量大小，默认1000。解析参数后调用ZKUtil.deleteRecursive执行删除，处理异常并返回结果。 |
| [DelQuotaCommand.java](DelQuotaCommand.md) | file | DelQuotaCommand类用于删除ZooKeeper节点配额，支持软硬配额类型选项，通过StatsTrack对象标记需删除的配额类型，执行配额删除或全部清除操作。 |
| [StatCommand.java](StatCommand.md) | file | StatCommand是CLI命令类，支持-w选项监控路径状态，兼容旧版语法，执行时检查路径是否存在并打印状态信息。 |
| [CommandNotFoundException.java](CommandNotFoundException.md) | file | 定义CommandNotFoundException类，继承CliException，构造时传入未找到的命令名并设置错误码127。 |
| [GetEphemeralsCommand.java](GetEphemeralsCommand.md) | file | GetEphemeralsCommand是CliCommand子类，用于获取ZooKeeper临时节点。解析参数后，若无路径参数则获取所有临时节点，否则获取指定路径下的临时节点。执行结果输出到控制台。 |
| [WhoAmICommand.java](WhoAmICommand.md) | file | 这是一个名为WhoAmICommand的Java类，继承自CliCommand，用于实现whoami命令行功能。它通过zk.whoAmI()获取客户端信息并输出认证方案和用户信息，异常时抛出CliWrapperException。 |
| [OutputFormatter.java](OutputFormatter.md) | file | 接口OutputFormatter定义格式化方法，输入字节数组返回字符串。 |
| [AddAuthCommand.java](AddAuthCommand.md) | file | 这是一个Java类AddAuthCommand，继承自CliCommand，用于解析和执行添加认证信息的命令行指令。它通过parse方法解析参数，若参数不足则抛出异常；exec方法调用zk.addAuthInfo添加认证信息。 |
| [CommandFactory.java](CommandFactory.md) | file | CommandFactory类定义了枚举Command，包含多种CLI命令类型，并通过getInstance方法创建对应的CliCommand实例。 |
| [Base64OutputFormatter.java](Base64OutputFormatter.md) | file | Base64OutputFormatter类实现OutputFormatter接口，提供静态实例INSTANCE，通过format方法将字节数组转为Base64字符串。 |
| [AddWatchCommand.java](AddWatchCommand.md) | file | AddWatchCommand是CLI命令类，支持添加监视路径，可选模式参数-m，默认持久递归模式，解析参数后执行zk.addWatch操作。 |
| [CliParseException.java](CliParseException.md) | file | CliParseException继承自CliException，提供两种构造方法：接收ParseException或字符串消息。用于命令行解析异常处理。 |
| [AclParser.java](AclParser.md) | file | AclParser类解析ACL字符串为列表，格式为scheme:id:perm，支持r、w、c、d、a权限。 |
| [ReconfigCommand.java](ReconfigCommand.md) | file | ReconfigCommand是用于ZooKeeper集群动态重配置的CLI命令类，支持增量和非增量模式，通过选项控制添加、移除服务器或指定新成员列表，同时校验配置版本以确保一致性。 |
| [DeleteCommand.java](DeleteCommand.md) | file | DeleteCommand类实现CLI删除命令，支持-v指定版本号，兼容旧语法但提示更新，通过ZooKeeper执行删除操作，处理异常并返回结果。 |
| [SetAclCommand.java](SetAclCommand.md) | file | SetAclCommand是用于设置ZooKeeper节点ACL权限的CLI命令，支持递归操作、版本控制和状态显示选项。 |
| [MalformedPathException.java](MalformedPathException.md) | file | 定义MalformedPathException类，继承CliException，通过构造函数传递错误信息。 |
| [MalformedCommandException.java](MalformedCommandException.md) | file | 定义MalformedCommandException类，继承CliException，通过构造函数传递错误信息。 |
| [HexDumpOutputFormatter.java](HexDumpOutputFormatter.md) | file | HexDumpOutputFormatter类实现OutputFormatter接口，提供字节数组的十六进制格式化输出，使用ByteBufUtil工具类转换数据。 |
| [CommandUsageHelper.java](CommandUsageHelper.md) | file | CommandUsageHelper类提供静态方法getUsage，用于生成命令使用说明，包含语法和可选参数信息。 |
| [SetQuotaCommand.java](SetQuotaCommand.md) | file | SetQuotaCommand是ZooKeeper CLI命令，用于设置路径配额，支持软硬限制和字节数限制，检查父子节点配额冲突并创建配额节点。 |
| [SetCommand.java](SetCommand.md) | file | SetCommand类继承CliCommand，用于设置znode数据。支持选项：-s打印状态，-v指定版本，-b base64数据。解析参数并执行setData操作，处理异常。 |
| [StatPrinter.java](StatPrinter.md) | file | StatPrinter类用于打印Stat对象属性，包括zxid、时间戳、版本号、数据长度和子节点数量等，输出到指定PrintStream。 |
| [CreateCommand.java](CreateCommand.md) | file | CreateCommand类处理ZooKeeper节点创建，支持选项-e（临时）、-s（顺序）、-c（容器）、-t（TTL），验证参数组合合法性并执行创建操作。 |
| [RemoveWatchesCommand.java](RemoveWatchesCommand.md) | file | RemoveWatchesCommand是用于移除ZooKeeper节点监听的命令行工具，支持多种监听类型选项（-c子节点、-d数据、-p持久等）和本地移除选项-l。 |
| [SyncCommand.java](SyncCommand.md) | file | SyncCommand是CLI命令类，用于同步路径，解析参数并调用zk.sync方法，超时30秒，处理成功或失败结果及异常。 |
| [GetAllChildrenNumberCommand.java](GetAllChildrenNumberCommand.md) | file | 这是一个Java类，继承自CliCommand，用于获取ZooKeeper路径下所有子节点数量。包含解析参数和执行命令的方法，处理异常并输出结果。 |
| [CliCommand.java](CliCommand.md) | file | 抽象类CliCommand定义CLI命令结构，含ZooKeeper实例、输入输出流、命令字符串和选项，提供解析、执行及工具方法。 |
| [VersionCommand.java](VersionCommand.md) | file | 这是一个ZooKeeper CLI的版本命令类，继承自CliCommand，用于解析并执行version命令，输出ZooKeeper的完整版本信息。 |
| [GetAclCommand.java](GetAclCommand.md) | file | GetAclCommand是获取ZooKeeper节点ACL权限的命令行工具，支持-s选项显示节点状态，解析路径参数后输出权限列表及可选状态信息。 |
| [LsCommand.java](LsCommand.md) | file | LsCommand类实现ls命令，支持选项-s（统计）、-w（监视）、-R（递归），解析参数并执行对应操作，包括列出子节点、递归遍历等，兼容旧版命令格式。 |
| [GetCommand.java](GetCommand.md) | file | GetCommand类继承CliCommand，用于获取znode数据。支持选项：-s显示状态，-w监听变更，-b输出base64，-x输出hexdump。解析参数并处理兼容性问题，执行获取数据操作，按需格式化输出。 |


