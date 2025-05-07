# 基础信息

|      |      |
|------|------|
| 名称 | FileChangeWatcher |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper/common/FileChangeWatcher.java |
| 包名 | org.apache.zookeeper.common |
| 依赖项 | ['java.io.IOException', 'java.nio.file.ClosedWatchServiceException', 'java.nio.file.FileSystem', 'java.nio.file.Path', 'java.nio.file.StandardWatchEventKinds', 'java.nio.file.WatchEvent', 'java.nio.file.WatchKey', 'java.nio.file.WatchService', 'java.util.function.Consumer', 'org.apache.zookeeper.server.ZooKeeperThread', 'org.slf4j.Logger', 'org.slf4j.LoggerFactory'] |
| 概述说明 | FileChangeWatcher类监控目录文件变化，通过回调处理事件。包含状态管理（NEW、STARTING等）、线程安全操作及后台线程逻辑，支持启动、停止及状态查询。 |

# 说明

FileChangeWatcher类用于监控目录文件变化，通过WatchService监听创建、删除、修改和溢出事件。包含NEW、STARTING、RUNNING、STOPPING、STOPPED五种状态，通过同步方法管理状态转换。内部类WatcherThread处理事件回调，支持启动和停止操作，确保线程安全。异常处理和日志记录完善，提供状态等待功能。

# 类列表 Class Summary

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| FileChangeWatcher | class | 文件监控类，跟踪目录变化并回调处理事件。包含状态管理（NEW、STARTING等）、线程控制及异常处理。支持启动、停止操作，确保线程安全。 |



## 类 FileChangeWatcher

|      |      |
|------|------|
| 访问范围 | public final |
| 类型 | class |
| 名称 | FileChangeWatcher |
| 说明 | 文件监控类，跟踪目录变化并回调处理事件。包含状态管理（NEW、STARTING等）、线程控制及异常处理。支持启动、停止操作，确保线程安全。 |


### UML类图

```mermaid
classDiagram
    class FileChangeWatcher {
        -LOG: Logger
        -watcherThread: WatcherThread
        -state: State
        +FileChangeWatcher(Path dirPath, Consumer~WatchEvent<?>~ callback) throws IOException
        +synchronized getState() State
        +synchronized waitForState(State desiredState) void throws InterruptedException
        -synchronized setState(State newState) void
        -synchronized compareAndSetState(State expected, State update) boolean
        -synchronized compareAndSetState(State[] expectedStates, State update) boolean
        +start() void
        +stop() void
    }

    class State {
        <<enumeration>>
        NEW
        STARTING
        RUNNING
        STOPPING
        STOPPED
    }

    class WatcherThread {
        -THREAD_NAME: String
        -watchService: WatchService
        -callback: Consumer~WatchEvent<?>~
        +WatcherThread(WatchService watchService, Consumer~WatchEvent<?>~ callback)
        +run() void
        -runLoop() void
    }

    class ZooKeeperThread {
        <<Interface>>
    }

    FileChangeWatcher --> State : 包含枚举
    FileChangeWatcher --> WatcherThread : 包含内部类
    WatcherThread ..|> ZooKeeperThread : 实现
    FileChangeWatcher --> Consumer~WatchEvent<?>~ : 依赖
    WatcherThread --> WatchService : 依赖
    WatcherThread --> WatchEvent : 依赖
```

```mermaid
flowchart TD
    A["创建FileChangeWatcher"] --> B["初始化WatchService"]
    B --> C["注册监听事件\n(ENTRY_CREATE, ENTRY_DELETE,\nENTRY_MODIFY, OVERFLOW)"]
    C --> D["设置状态为NEW"]
    D --> E["创建WatcherThread"]
    E --> F["设置守护线程"]

    G["调用start()"] --> H{"状态是否为NEW?"}
    H -->|是| I["设置状态为STARTING"]
    H -->|否| J["直接返回"]
    I --> K["启动WatcherThread"]

    L["调用stop()"] --> M{"状态是否为\nRUNNING/STARTING?"}
    M -->|是| N["设置状态为STOPPING"]
    M -->|否| O["直接返回"]
    N --> P["中断WatcherThread"]

    Q["WatcherThread.run()"] --> R{"状态是否为STARTING?"}
    R -->|是| S["设置状态为RUNNING"]
    R -->|否| T["检查异常状态"]
    S --> U["进入runLoop"]
    U --> V["watchService.take()"]
    V --> W{"是否中断/关闭?"}
    W -->|是| X["退出循环"]
    W -->|否| Y["处理事件"]
    Y --> Z["调用callback"]
    Z --> AA["重置WatchKey"]
    AA --> AB{"key是否有效?"}
    AB -->|是| U
    AB -->|否| AC["记录错误并退出"]
    X --> AD["关闭watchService"]
    AD --> AE["设置状态为STOPPED"]
```

该代码实现了一个文件变化监听器，通过WatchService监控目录变化并触发回调。核心类FileChangeWatcher通过状态机管理生命周期，内部类WatcherThread继承ZooKeeperThread实现异步监听。流程图展示了从初始化、启动、运行到停止的完整流程，包含状态转换和异常处理路径。类图清晰地展示了类之间的关系和依赖，特别是枚举状态和线程实现关系。


### 内部方法调用关系图

```mermaid
graph TD
    A["类FileChangeWatcher"]
    B["枚举State: NEW, STARTING, RUNNING, STOPPING, STOPPED"]
    C["属性: WatcherThread watcherThread"]
    D["属性: State state"]
    E["构造方法: FileChangeWatcher(Path dirPath, Consumer<WatchEvent<?>> callback)"]
    F["方法: synchronized State getState()"]
    G["方法: synchronized void waitForState(State desiredState)"]
    H["方法: private synchronized void setState(State newState)"]
    I["方法: private synchronized boolean compareAndSetState(State expected, State update)"]
    J["方法: private synchronized boolean compareAndSetState(State[] expectedStates, State update)"]
    K["方法: public void start()"]
    L["方法: public void stop()"]
    M["内部类WatcherThread"]
    N["方法: public void run()"]
    O["方法: private void runLoop()"]

    A --> B
    A --> C
    A --> D
    A --> E
    A --> F
    A --> G
    A --> H
    A --> I
    A --> J
    A --> K
    A --> L
    A --> M
    M --> N
    N --> O
```

```mermaid
sequenceDiagram
    participant Client
    participant FileChangeWatcher
    participant WatcherThread
    participant WatchService

    Client->>FileChangeWatcher: new FileChangeWatcher(dirPath, callback)
    FileChangeWatcher->>WatchService: fs.newWatchService()
    FileChangeWatcher->>WatchService: dirPath.register()
    FileChangeWatcher->>WatcherThread: new WatcherThread(watchService, callback)
    FileChangeWatcher->>WatcherThread: setDaemon(true)
    Client->>FileChangeWatcher: start()
    FileChangeWatcher->>FileChangeWatcher: compareAndSetState(NEW, STARTING)
    FileChangeWatcher->>WatcherThread: start()
    WatcherThread->>WatcherThread: run()
    WatcherThread->>FileChangeWatcher: compareAndSetState(STARTING, RUNNING)
    loop runLoop
        WatcherThread->>WatchService: take()
        WatchService-->>WatcherThread: WatchKey
        WatcherThread->>WatchKey: pollEvents()
        WatcherThread->>callback: accept(event)
    end
    Client->>FileChangeWatcher: stop()
    FileChangeWatcher->>FileChangeWatcher: compareAndSetState([RUNNING,STARTING], STOPPING)
    FileChangeWatcher->>WatcherThread: interrupt()
    WatcherThread->>WatchService: close()
    WatcherThread->>FileChangeWatcher: setState(STOPPED)
```

这段代码实现了一个文件变化监视器，通过WatchService监控指定目录的文件创建、删除和修改事件。核心是WatcherThread线程类，通过状态机管理生命周期（NEW/STARTING/RUNNING等），使用同步方法保证线程安全。流程图展示了类结构和主要方法调用关系，时序图描述了从初始化、启动监控到停止的完整流程，包括事件监听循环和状态转换过程。该设计具有完善的错误处理和状态管理机制。

### 字段列表 Field List

| 名称  | 类型  | 说明 |
|-------|-------|------|
| watcherThread | WatcherThread | 私有最终监视线程实例。 |
| state | State | 私有状态变量state。 |
| LOG = LoggerFactory.getLogger(FileChangeWatcher.class) | Logger | 日志记录器初始化，用于FileChangeWatcher类的日志输出。 |

### 方法列表 Method List

| 名称  | 类型  | 说明 |
|-------|-------|------|
| getState | State | 同步方法返回当前状态对象。 |
| waitForState | void | 同步方法等待状态：循环检查当前状态是否等于目标状态，否则调用wait()等待。 |
| stop | void | 方法stop()尝试将状态从RUNNING或STARTING改为STOPPING，成功则中断watcherThread线程。 |
| start | void | 检查状态是否为NEW，是则启动线程，否则直接返回。 |
| compareAndSetState | boolean | 私有同步方法，比较并设置状态：若当前状态等于预期值，则更新状态并返回true；否则返回false。 |
| compareAndSetState | boolean | 私有同步方法compareAndSetState检查当前状态是否匹配预期状态数组，匹配则更新状态并返回true，否则返回false。 |
| setState | void | 私有同步方法setState用于更新状态并通知所有等待线程。参数为新状态newState，赋值后调用notifyAll唤醒线程。 |




