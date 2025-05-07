# 基础信息

|      |      |
|------|------|
| 名称 | common |
| 编码语言 | .java |
| 代码路径 | zookeeper/zookeeper-server/src/main/java/org/apache/zookeeper/common |
| 包名 | zookeeper.docs.zookeeper-server.src.main.java.org.apache.zookeeper.common |
| 概述说明 | StandardTypeFileKeyStoreLoader抽象类支持JKS/PKCS12/BCFKS密钥库加载。NetUtils处理网络地址格式化。ZKConfig管理ZooKeeper配置。PEMFileLoader加载PEM格式密钥库。SecretUtils安全读取文件秘密。KeyStoreFileType枚举定义密钥库类型。SSLContextAndOptions封装SSL配置。ZKTrustManager实现证书验证。StringUtils提供字符串操作。QuorumX509Util处理仲裁SSL配置。FileKeyStoreLoader为密钥库加载抽象类。PKCS12FileLoader加载PKCS12格式。Time类处理时间操作。PathUtils验证路径有效性。AtomicFileWritingIdiom原子化文件写入。BCFKSFileLoader加载BCFKS格式。NettyUtils提供Netty工具方法。FileChangeWatcher监控文件变化。KeyStoreLoader定义密钥库加载接口。X509Exception为证书异常类。PathTrie实现路径前缀树。ZKHostnameVerifier验证主机名。JKSFileLoader加载JKS格式。X509Util处理X.509证书。ClientX509Util处理客户端SSL配置。IOUtils提供IO流工具。AtomicFileOutputStream原子化文件输出。FileKeyStoreLoaderBuilderProvider根据类型返回Builder。 |

# 说明

## 概述  
1. 该模块是ZooKeeper的SSL/TLS安全通信基础设施，核心职责包括密钥库加载、证书验证和网络配置管理。例如StandardTypeFileKeyStoreLoader支持JKS/PKCS12/BCFKS格式的密钥库加载。  
2. 主要接口遵循Java安全体系规范，如KeyStoreLoader定义密钥库加载方法，X509Util提供SSLContext创建等高级功能。类似文件系统的API设计确保易用性。  
3. 关键数据结构包括PathTrie（路径前缀树）、AtomicFileOutputStream（原子文件写入流）和ZKConfig（配置键值映射）。例如PathTrie使用读写锁保证线程安全。  
4. 外部依赖包括Java Cryptography Architecture（JCA）、Netty网络框架和BouncyCastle加密库。例如PKCS12FileLoader依赖PKCS#12标准实现。  

## 主要业务场景  
1. 支持SSL/TLS通信全流程，包括密钥库加载（如PEMFileLoader）、证书验证（ZKTrustManager）和协议协商（SSLContextAndOptions）。例如QuorumX509Util专用于仲裁通信场景。  
2. 采用建造者模式（如FileKeyStoreLoader.Builder）和工具类（如NetUtils）简化配置，通过ZKConfig集中管理系统属性。类似Spring的配置管理方式。  
3. 功能覆盖FIPS模式、OCSP检查、主机名验证等企业级需求，例如X509Util支持TLSv1.1-1.3协议自动选择。  
4. 主要应用于ZooKeeper服务器间安全通信和客户端认证，如ZKHostnameVerifier实现RFC 2818主机名验证规范。  
5. 提供静态工具方法（StringUtils.split）和工厂模式（FileKeyStoreLoaderBuilderProvider），例如AtomicFileWritingIdiom封装原子文件写入操作。  
6. 与Netty深度集成（NettyUtils），支持Epoll/NIO自动选择，例如通过createThreadFactory生成带前缀的守护线程。


### 包内部结构视图

```mermaid
graph TD
    common --> StandardTypeFileKeyStoreLoader.java
    common --> NetUtils.java
    common --> ZKConfig.java
    common --> PEMFileLoader.java
    common --> SecretUtils.java
    common --> KeyStoreFileType.java
    common --> SSLContextAndOptions.java
    common --> ZKTrustManager.java
    common --> StringUtils.java
    common --> QuorumX509Util.java
    common --> FileKeyStoreLoader.java
    common --> PKCS12FileLoader.java
    common --> Time.java
    common --> PathUtils.java
    common --> AtomicFileWritingIdiom.java
    common --> BCFKSFileLoader.java
    common --> NettyUtils.java
    common --> FileChangeWatcher.java
    common --> KeyStoreLoader.java
    common --> X509Exception.java
    common --> PathTrie.java
    common --> ZKHostnameVerifier.java
    common --> JKSFileLoader.java
    common --> X509Util.java
    common --> ClientX509Util.java
    common --> IOUtils.java
    common --> AtomicFileOutputStream.java
    common --> FileKeyStoreLoaderBuilderProvider.java
```

该流程图展示了Zookeeper项目中common目录下的所有文件层级关系。根节点为common文件夹，直接包含28个Java源文件，涵盖网络工具、安全认证、文件操作等核心功能模块。所有文件均位于同一层级，没有子目录结构，完整呈现了Zookeeper通用工具类的组织方式。

# 文件列表 File List

| 名称   | 类型  | 说明 |
|-------|------|-------------|
| [StandardTypeFileKeyStoreLoader.java](StandardTypeFileKeyStoreLoader.md) | file | StandardTypeFileKeyStoreLoader继承FileKeyStoreLoader，支持JKS、PKCS12、BCFKS格式密钥库，提供加载密钥库和信任库的方法，密码可转为字符数组。 |
| [SSLContextAndOptions.java](SSLContextAndOptions.md) | file | SSLContextAndOptions类封装SSL配置，提供创建和配置SSLSocket及SSLServerSocket的方法，支持协议、加密套件和客户端认证设置。 |
| [KeyStoreFileType.java](KeyStoreFileType.md) | file | KeyStoreFileType枚举定义密钥存储文件类型，包含JKS、PEM、PKCS12和BCFKS四种格式，提供根据属性值或文件名自动检测类型的方法。 |
| [SecretUtils.java](SecretUtils.md) | file | SecretUtils工具类提供静态方法readSecret，从指定路径读取文件内容并转为字符数组，自动处理末尾换行符，异常时记录日志并抛出IllegalStateException。 |
| [PEMFileLoader.java](PEMFileLoader.md) | file | PEMFileLoader类继承FileKeyStoreLoader，通过PEM文件加载密钥库和信任库，支持密码可选，使用构建器模式创建实例。 |
| [ZKConfig.java](ZKConfig.md) | file | ZKConfig类用于管理ZooKeeper配置，支持从文件和系统属性加载配置，提供获取和设置属性方法，包括字符串、布尔值和整数值，并处理SSL和向后兼容性。 |
| [NetUtils.java](NetUtils.md) | file | NetUtils类提供两个方法：formatInetAddr格式化网络地址，优先使用主机名，IPv6地址加方括号；getIPV6HostAndPort解析IPv6主机端口字符串，返回主机和端口数组。 |
| [QuorumX509Util.java](QuorumX509Util.md) | file | QuorumX509Util继承X509Util，重写方法设置ZooKeeper SSL配置前缀为"zookeeper.ssl.quorum."，并强制验证客户端主机名。 |
| [StringUtils.java](StringUtils.md) | file | StringUtils工具类：提供字符串分割、拼接及判空方法，分割时自动去空格和空值，拼接支持null安全，判空包含null和空白检查。 |
| [NettyUtils.java](NettyUtils.md) | file | NettyUtils工具类提供创建Netty线程工厂、事件循环组及选择Epoll/NIO通道的方法，并检测客户端可达的本地网络地址数量。 |
| [BCFKSFileLoader.java](BCFKSFileLoader.md) | file | BCFKSFileLoader继承StandardTypeFileKeyStoreLoader，通过Builder构建，支持BCFKS格式密钥库和信任库加载。 |
| [AtomicFileWritingIdiom.java](AtomicFileWritingIdiom.md) | file | 原子文件写入类，支持OutputStream和Writer两种方式，确保操作原子性，异常时清理临时文件。 |
| [PathUtils.java](PathUtils.md) | file | PathUtils类提供路径处理功能：验证znode路径合法性（非空、首字符为/、无连续/等），转换Windows路径为Unix格式，提取路径顶层命名空间。 |
| [Time.java](Time.md) | file | Java类Time提供时间单位常量、获取当前时间方法及时间字符串解析功能。包含毫秒、秒、分、时、日转换，支持系统时间和稳定计时器，可将时间间隔字符串转为毫秒值。 |
| [PKCS12FileLoader.java](PKCS12FileLoader.md) | file | PKCS12FileLoader继承StandardTypeFileKeyStoreLoader，通过Builder构建，支持PKCS12格式密钥库和信任库加载。 |
| [ClientX509Util.java](ClientX509Util.md) | file | ClientX509Util扩展X509Util，提供客户端和服务端SSL上下文创建功能，支持密钥库、信任库配置，协议和加密套件设置，以及主机名验证。 |
| [JKSFileLoader.java](JKSFileLoader.md) | file | JKSFileLoader继承StandardTypeFileKeyStoreLoader，通过私有构造器和静态Builder类创建实例，支持JKS格式密钥库加载。 |
| [ZKHostnameVerifier.java](ZKHostnameVerifier.md) | file | ZKHostnameVerifier类实现主机名验证，支持IPv4、IPv6和DNS类型，通过匹配证书主题备用名或通用名确保安全连接。 |
| [PathTrie.java](PathTrie.md) | file | PathTrie类实现线程安全路径树，含根节点、读写锁及TrieNode内部类，支持增删查路径、查找最大前缀和清空操作。 |
| [X509Exception.java](X509Exception.md) | file | X509Exception及其子类KeyManagerException、TrustManagerException、SSLContextException，用于处理X509相关异常，支持消息和原因构造。 |
| [KeyStoreLoader.java](KeyStoreLoader.md) | file | 接口KeyStoreLoader定义了两个方法：loadKeyStore加载含私钥和X509证书链的密钥库，loadTrustStore加载含CA信任证书链的密钥库，均可能抛出IO或安全异常。 |
| [FileKeyStoreLoaderBuilderProvider.java](FileKeyStoreLoaderBuilderProvider.md) | file | FileKeyStoreLoaderBuilderProvider类提供根据KeyStoreFileType返回对应文件加载器Builder的方法，支持JKS、PEM、PKCS12和BCFKS类型。 |
| [AtomicFileOutputStream.java](AtomicFileOutputStream.md) | file | AtomicFileOutputStream类扩展FilterOutputStream，提供原子文件写入功能。通过临时文件写入，关闭时重命名确保原子性。支持高效写入和异常处理，失败时可中止操作并清理临时文件。 |
| [IOUtils.java](IOUtils.md) | file | IOUtils类提供静态方法处理流操作：closeStream关闭流忽略异常；cleanup关闭多个流并记录异常；copyBytes在流间复制数据，可选关闭流。 |
| [X509Util.java](X509Util.md) | file | X509Util是一个抽象类，用于管理TLS/SSL配置，包括密钥库、信任库、协议版本、密码套件等。它支持自动重新加载证书文件，提供默认TLS协议选择（TLSv1.2或TLSv1.3），并包含客户端认证选项（NONE/WANT/NEED）。类实现了Closeable和AutoCloseable接口，确保资源正确释放。 |
| [FileChangeWatcher.java](FileChangeWatcher.md) | file | FileChangeWatcher类监控目录文件变化，通过回调处理事件。包含状态管理（NEW、STARTING等）、线程安全操作及后台线程逻辑，支持启动、停止及状态查询。 |
| [FileKeyStoreLoader.java](FileKeyStoreLoader.md) | file | 抽象类FileKeyStoreLoader实现密钥库加载，包含路径和密码字段。其静态抽象Builder类支持链式调用设置参数并构建子类实例。 |
| [ZKTrustManager.java](ZKTrustManager.md) | file | ZKTrustManager扩展X509ExtendedTrustManager，提供客户端和服务端主机名验证功能，支持通过Socket和SSLEngine进行验证。 |


