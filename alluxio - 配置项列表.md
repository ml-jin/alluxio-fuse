# 配置项列表

[TOC]

[![Slack](https://img.shields.io/badge/slack-alluxio--community-blue.svg?logo=slack)](https://www.alluxio.io/slack) [![Docker Pulls](https://img.shields.io/docker/pulls/alluxio/alluxio.svg?logo=docker&color=166FDB)](https://hub.docker.com/r/alluxio/alluxio) [![GitHub edit source](https://img.shields.io/badge/github-edit%20source-blue.svg?logo=github&color=166FDB)](https://github.com/Alluxio/alluxio/blob/branch-2.4/docs/cn/reference/Properties-List.md)

所有Alluxio配置属性都属于以下六类之一： [共有配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#common-configuration)（由Master和Worker共享）， [Master配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#master-configuration)，[Worker配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#worker-configuration)， [用户配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#user-configuration)，[集群管理配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#cluster-management)（用于在诸如Mesos和YARN的集群管理器上运行Alluxio） 以及[安全性配置项](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#security-configuration)（由Master，Worker和用户共享）。

## 共有配置项

共有配置项包含了不同组件共享的常量。

| 属性名                                                     | 默认值                                                       | 描述                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| alluxio.conf.dir                                           | ${alluxio.home}/conf                                         | 包含Alluxio配置文件的目录。                                  |
| alluxio.debug                                              | false                                                        | 设置为true后即可启用debug模式，会记录更多日志，且在Web UI中会显示更多信息。 |
| alluxio.extensions.dir                                     | ${alluxio.home}/extensions                                   | 包含Alluxio扩展的目录。                                      |
| alluxio.fuse.cached.paths.max                              | 500                                                          | 用于FUSE conversion的Alluxio缓存路径的最大数量。             |
| alluxio.fuse.debug.enabled                                 | false                                                        | 以debug模式运行FUSE，让fuse进程记录每个FS请求。              |
| alluxio.fuse.fs.name                                       | alluxio-fuse                                                 | FUSE文件系统名。                                             |
| alluxio.fuse.logging.threshold                             | 10s                                                          |                                                              |
| alluxio.fuse.maxwrite.bytes                                | 128KB                                                        | 写操作的最大粒度，内核将其上限设置为128KB max(从Linux 3.16.0开始)。 |
| alluxio.fuse.user.group.translation.enabled                | false                                                        |                                                              |
| alluxio.home                                               | /opt/alluxio                                                 | Alluxio安装目录。                                            |
| alluxio.job.master.bind.host                               | 0.0.0.0                                                      |                                                              |
| alluxio.job.master.client.threads                          | 1024                                                         |                                                              |
| alluxio.job.master.embedded.journal.addresses              |                                                              |                                                              |
| alluxio.job.master.embedded.journal.port                   | 20003                                                        |                                                              |
| alluxio.job.master.finished.job.purge.count                | -1                                                           |                                                              |
| alluxio.job.master.finished.job.retention.time             | 300sec                                                       |                                                              |
| alluxio.job.master.hostname                                | ${alluxio.master.hostname}                                   |                                                              |
| alluxio.job.master.job.capacity                            | 100000                                                       |                                                              |
| alluxio.job.master.lost.worker.interval                    | 1sec                                                         |                                                              |
| alluxio.job.master.rpc.addresses                           |                                                              |                                                              |
| alluxio.job.master.rpc.port                                | 20001                                                        |                                                              |
| alluxio.job.master.web.bind.host                           | 0.0.0.0                                                      |                                                              |
| alluxio.job.master.web.hostname                            | ${alluxio.job.master.hostname}                               |                                                              |
| alluxio.job.master.web.port                                | 20002                                                        |                                                              |
| alluxio.job.master.worker.heartbeat.interval               | 1sec                                                         |                                                              |
| alluxio.job.master.worker.timeout                          | 60sec                                                        |                                                              |
| alluxio.job.worker.bind.host                               | 0.0.0.0                                                      |                                                              |
| alluxio.job.worker.data.port                               | 30002                                                        |                                                              |
| alluxio.job.worker.hostname                                | ${alluxio.worker.hostname}                                   |                                                              |
| alluxio.job.worker.rpc.port                                | 30001                                                        |                                                              |
| alluxio.job.worker.threadpool.size                         | 10                                                           |                                                              |
| alluxio.job.worker.throttling                              | false                                                        |                                                              |
| alluxio.job.worker.web.bind.host                           | 0.0.0.0                                                      |                                                              |
| alluxio.job.worker.web.port                                | 30003                                                        |                                                              |
| alluxio.jvm.monitor.info.threshold                         | 1sec                                                         | 额外的睡眠时间超过这个阈值，记录INFO。                       |
| alluxio.jvm.monitor.sleep.interval                         | 1sec                                                         | JVM monitor线程的睡眠时间。                                  |
| alluxio.jvm.monitor.warn.threshold                         | 10sec                                                        | 额外的睡眠时间超过这个阈值，记录WARN。                       |
| alluxio.locality.compare.node.ip                           | false                                                        | 是否在本地性检查时尝试解析IP地址。                           |
| alluxio.locality.node                                      |                                                              | 用于判断node本地性。                                         |
| alluxio.locality.order                                     | node,rack                                                    | 本地性级别顺序。                                             |
| alluxio.locality.rack                                      |                                                              | 用于判断rack本地性。                                         |
| alluxio.locality.script                                    | alluxio-locality.sh                                          | 用于确定本地性检查的身份的脚本。                             |
| alluxio.logger.type                                        | Console                                                      | logger类型。                                                 |
| alluxio.logs.dir                                           | ${alluxio.work.dir}/logs                                     | 存放日志文件的路径。                                         |
| alluxio.logserver.hostname                                 |                                                              | Alluxio logserver主机名。注意:必须将此属性指定为JVM属性;它在alluxio-site.properties不被接受。 |
| alluxio.logserver.logs.dir                                 | ${alluxio.work.dir}/logs                                     | 远程日志文件默认位置。注意:必须将此属性指定为JVM属性;它在alluxio-site.properties不被接受。 |
| alluxio.logserver.port                                     | 45600                                                        | 从Alluxio server接收日志的默认端口号。注意:必须将此属性指定为JVM属性;它在alluxio-site.properties不被接受。 |
| alluxio.logserver.threads.max                              | 2048                                                         | logserver用于响应日志请求的最大线程数。                      |
| alluxio.logserver.threads.min                              | 512                                                          | logserver用于响应日志请求的最小线程数。                      |
| alluxio.metrics.conf.file                                  | ${alluxio.conf.dir}/metrics.properties                       | 度量系统配置文件路径，默认是`conf`文件夹下的`metrics.properties`文件。 |
| alluxio.network.connection.auth.timeout                    | 30sec                                                        |                                                              |
| alluxio.network.connection.health.check.timeout            | 5sec                                                         |                                                              |
| alluxio.network.connection.server.shutdown.timeout         | 60sec                                                        |                                                              |
| alluxio.network.connection.shutdown.graceful.timeout       | 45sec                                                        |                                                              |
| alluxio.network.connection.shutdown.timeout                | 15sec                                                        |                                                              |
| alluxio.network.host.resolution.timeout                    | 5sec                                                         | Alluxio在启动Master和Worker过程中，需要确保它们在监听外部可达且可解析的主机名。如果没有显式指定主机名，Alluxio会自动尝试选择一个合适的主机名。该配置项指定用于判断一个候选主机名在网络上是否可达的最长等待时间。 |
| alluxio.proxy.s3.deletetype                                | ALLUXIO_AND_UFS                                              | 通过S3 API删除桶和对象的删除类型，可选择 `ALLUXIO_AND_UFS`（删除Alluxio和UFS）或`ALLUXIO_ONLY`（只删除Alluxio名空间下） |
| alluxio.proxy.s3.multipart.temporary.dir.suffix            | _s3_multipart_tmp                                            | 在多部分上传时拥有这些部分的目录的后缀。                     |
| alluxio.proxy.s3.writetype                                 | CACHE_THROUGH                                                | 通过S3 API创建桶和对象的写类型，可选择“MUST_CACHE”(只写入Alluxio，必须存储在Alluxio中)、“CACHE_THROUGH”(尝试缓存，同步写入到UnderFS)、“THROUGH”(无缓存，同步写入到UnderFS)。 |
| alluxio.proxy.stream.cache.timeout                         | 1hour                                                        | 在代理中输入和输出流缓存回收的时限。                         |
| alluxio.proxy.web.bind.host                                | 0.0.0.0                                                      | Alluxio代理网络服务器运行的主机名。                          |
| alluxio.proxy.web.hostname                                 |                                                              | Alluxio代理的web UI绑定的主机名。                            |
| alluxio.proxy.web.port                                     | 39999                                                        | Alluxio代理的web UI绑定的端口。                              |
| alluxio.secondary.master.metastore.dir                     | ${alluxio.work.dir}/secondary-metastore                      |                                                              |
| alluxio.site.conf.dir                                      | ${alluxio.conf.dir}/,${user.home}/.alluxio/,/etc/alluxio/    | 加载配置文件时默认的搜索路径                                 |
| alluxio.table.catalog.path                                 | /catalog                                                     |                                                              |
| alluxio.table.catalog.udb.sync.timeout                     | 1h                                                           |                                                              |
| alluxio.table.enabled                                      | true                                                         |                                                              |
| alluxio.table.transform.manager.job.history.retention.time | 300sec                                                       |                                                              |
| alluxio.table.transform.manager.job.monitor.interval       | 10000                                                        |                                                              |
| alluxio.test.deprecated.key                                |                                                              |                                                              |
| alluxio.tmp.dirs                                           | /tmp                                                         | 存储Alluxio临时文件的路径，使用逗号作为分隔符。如果指定了多个路径，每个临时文件将随机选择一个路径。目前，只有上传到对象存储的文件存储在这些路径中。 |
| alluxio.underfs.allow.set.owner.failure                    | false                                                        | 是否允许UFS中的设置所有者失败。当设置为true时，文件或目录所有者可能会在Alluxio和UFS之间产生分歧。 |
| alluxio.underfs.cleanup.enabled                            | false                                                        |                                                              |
| alluxio.underfs.cleanup.interval                           | 1day                                                         |                                                              |
| alluxio.underfs.eventual.consistency.retry.base.sleep      | 50ms                                                         |                                                              |
| alluxio.underfs.eventual.consistency.retry.max.num         | 20                                                           |                                                              |
| alluxio.underfs.eventual.consistency.retry.max.sleep       | 30sec                                                        |                                                              |
| alluxio.underfs.gcs.default.mode                           | 0700                                                         |                                                              |
| alluxio.underfs.gcs.directory.suffix                       | /                                                            |                                                              |
| alluxio.underfs.gcs.owner.id.to.username.mapping           |                                                              | 可选配置项，指定一个预设的gcs拥有者ID到Alluxio用户名的静态映射，格式为“id1=user1;id2=user2”。谷歌云存储的ID可以在控制台地址https://console.cloud.google.com/storage/settings找到。请使用“Owners”选项。 |
| alluxio.underfs.hdfs.configuration                         | ${alluxio.conf.dir}/core-site.xml:${alluxio.conf.dir}/hdfs-site.xml | hdfs配置文件的位置。                                         |
| alluxio.underfs.hdfs.impl                                  | org.apache.hadoop.hdfs.DistributedFileSystem                 | 作为底层存储系统的hdfs的实现类。                             |
| alluxio.underfs.hdfs.prefixes                              | hdfs://,glusterfs:///                                        | 可选配置项，指定以哪些前缀开头的文件应该存放在Apache Hadoop底层文件系统。分隔符为任何空白符或者','。 |
| alluxio.underfs.hdfs.remote                                | true                                                         | 底层存储系统的worker节点对于Alluxio worker节点来说是否是远程的。如果该值为true，那么Alluxio将不会尝试从底层存储系统获取locality相关信息，因为这种情况下不可能存在任何locality，这样做可以提高性能。默认值为false。 |
| alluxio.underfs.kodo.connect.timeout                       | 50sec                                                        |                                                              |
| alluxio.underfs.kodo.downloadhost                          |                                                              |                                                              |
| alluxio.underfs.kodo.endpoint                              |                                                              |                                                              |
| alluxio.underfs.kodo.requests.max                          | 64                                                           |                                                              |
| alluxio.underfs.listing.length                             | 1000                                                         | 底层文件系统在一次查询中可以列出的目录条目的最大数量。如果条目数量大于指定长度，则需要多次查询才能列出。 |
| alluxio.underfs.object.store.breadcrumbs.enabled           | true                                                         |                                                              |
| alluxio.underfs.object.store.mount.shared.publicly         | false                                                        | 是否对所有Alluxio用户共享挂载的对象存储系统。 注意，该配置对HDFS或者本地文件系统没有任何影响。默认值是false。 |
| alluxio.underfs.object.store.multi.range.chunk.size        | ${alluxio.user.block.size.bytes.default}                     |                                                              |
| alluxio.underfs.object.store.service.threads               | 20                                                           | 存储底层文件系统操作的并行对象在执行程序池中的线程数。       |
| alluxio.underfs.oss.connection.max                         | 1024                                                         | OSS连接最大数目。                                            |
| alluxio.underfs.oss.connection.timeout                     | 50sec                                                        | OSS连接时限。                                                |
| alluxio.underfs.oss.connection.ttl                         | -1                                                           | OSS连接的TTL（ms）。                                         |
| alluxio.underfs.oss.socket.timeout                         | 50sec                                                        | OSS套接字时限。                                              |
| alluxio.underfs.s3.admin.threads.max                       | 20                                                           | 与S3通信时进行元数据操作所使用的最大线程数目，这些操作会并发且频繁执行，但不会花费太多时间。默认值为20。 |
| alluxio.underfs.s3.connection.ttl                          | -1                                                           |                                                              |
| alluxio.underfs.s3.default.mode                            | 0700                                                         |                                                              |
| alluxio.underfs.s3.directory.suffix                        | /                                                            |                                                              |
| alluxio.underfs.s3.disable.dns.buckets                     | false                                                        | 可选配置项，指定所有S3请求路径样式。                         |
| alluxio.underfs.s3.endpoint                                |                                                              | 可选配置项，在组织AWS服务请求的时候可以指定某个区域地址来降低数据延迟或者访问某些隔离在不同AWS Region的资源。 一个endpoint是某个服务的一个入口地址。举例，s3.cn-north-1.amazonaws.com.cn 就是一个北京区域的亚马逊S3服务的一个endpoing。 |
| alluxio.underfs.s3.inherit.acl                             | true                                                         |                                                              |
| alluxio.underfs.s3.intermediate.upload.clean.age           | 3day                                                         |                                                              |
| alluxio.underfs.s3.list.objects.v1                         | false                                                        |                                                              |
| alluxio.underfs.s3.max.error.retry                         |                                                              |                                                              |
| alluxio.underfs.s3.owner.id.to.username.mapping            |                                                              | 可选配置项，指定一个预设的s3规范ID到Alluxio用户名的静态映射，格式为“id1=user1;id2=user2”。AWS的s3规范ID可以在控制台地址https://console.aws.amazon.com/iam/home?#security_credential找到。请展开“Account Identifiers”选项卡，并参考“Canonical User ID”。 |
| alluxio.underfs.s3.proxy.host                              |                                                              | 可选配置项，指定与S3通信的代理主机。                         |
| alluxio.underfs.s3.proxy.port                              |                                                              | 可选配置项，指定与S3通信的代理端口。                         |
| alluxio.underfs.s3.request.timeout                         | 1min                                                         |                                                              |
| alluxio.underfs.s3.secure.http.enabled                     | false                                                        |                                                              |
| alluxio.underfs.s3.server.side.encryption.enabled          | false                                                        |                                                              |
| alluxio.underfs.s3.signer.algorithm                        |                                                              |                                                              |
| alluxio.underfs.s3.socket.timeout                          | 50sec                                                        |                                                              |
| alluxio.underfs.s3.streaming.upload.enabled                | false                                                        |                                                              |
| alluxio.underfs.s3.streaming.upload.partition.size         | 64MB                                                         |                                                              |
| alluxio.underfs.s3.threads.max                             | 40                                                           | 与S3通信时使用的最大线程数目和最大并发连接数。包括数据上载以及元数据操作线程。该数目至少要等于最大管理线程与最大上传线程数目之和。默认值为40,即默认管理线程与默认上传线程池大小之和。 |
| alluxio.underfs.s3.upload.threads.max                      | 20                                                           | 进行多部分上传时上传数据到S3所使用的最大线程数目，这些操作会相对耗时，然而由于某些线程会引起错误，过多线程会导致带宽饥饿。默认值为2。 |
| alluxio.underfs.web.connnection.timeout                    | 60s                                                          |                                                              |
| alluxio.underfs.web.header.last.modified                   | EEE, dd MMM yyyy HH:mm:ss zzz                                |                                                              |
| alluxio.underfs.web.parent.names                           | Parent Directory,..,../                                      |                                                              |
| alluxio.underfs.web.titles                                 | Index of,Directory listing for                               |                                                              |
| alluxio.web.cors.enabled                                   | false                                                        |                                                              |
| alluxio.web.file.info.enabled                              | true                                                         |                                                              |
| alluxio.web.refresh.interval                               | 15s                                                          |                                                              |
| alluxio.web.threads                                        | 1                                                            | web服务器的线程数目。                                        |
| alluxio.web.ui.enabled                                     | true                                                         |                                                              |
| alluxio.work.dir                                           | ${alluxio.home}                                              | Alluxio的工作目录。默认情况下，journal、logs以及底层文件系统的数据（如果使用本地文件系统）都会写在该目录下。 |
| alluxio.zookeeper.address                                  |                                                              | ZooKeeper地址。                                              |
| alluxio.zookeeper.auth.enabled                             | true                                                         |                                                              |
| alluxio.zookeeper.connection.timeout                       | 15s                                                          | 连接到Zookeeper时的连接时限。                                |
| alluxio.zookeeper.election.path                            | /alluxio/election                                            | ZooKeeper的选举文件夹。                                      |
| alluxio.zookeeper.enabled                                  | false                                                        | 若为true，则使用zooKeeper启动master容错机制。                |
| alluxio.zookeeper.job.election.path                        | /job_election                                                |                                                              |
| alluxio.zookeeper.job.leader.path                          | /job_leader                                                  |                                                              |
| alluxio.zookeeper.leader.connection.error.policy           | SESSION                                                      |                                                              |
| alluxio.zookeeper.leader.inquiry.retry                     | 10                                                           | 从ZooKeeper申请leader的最大请求次数。                        |
| alluxio.zookeeper.leader.path                              | /alluxio/leader                                              | ZooKeeper的leader文件夹。                                    |
| alluxio.zookeeper.session.timeout                          | 60s                                                          | 连接到Zookeeper时的会话时限。                                |
| aws.accessKeyId                                            |                                                              |                                                              |
| aws.secretKey                                              |                                                              |                                                              |
| fs.cos.access.key                                          |                                                              |                                                              |
| fs.cos.app.id                                              |                                                              |                                                              |
| fs.cos.connection.max                                      | 1024                                                         |                                                              |
| fs.cos.connection.timeout                                  | 50sec                                                        |                                                              |
| fs.cos.region                                              |                                                              |                                                              |
| fs.cos.secret.key                                          |                                                              |                                                              |
| fs.cos.socket.timeout                                      | 50sec                                                        |                                                              |
| fs.gcs.accessKeyId                                         |                                                              |                                                              |
| fs.gcs.secretAccessKey                                     |                                                              |                                                              |
| fs.kodo.accesskey                                          |                                                              |                                                              |
| fs.kodo.secretkey                                          |                                                              |                                                              |
| fs.oss.accessKeyId                                         |                                                              |                                                              |
| fs.oss.accessKeySecret                                     |                                                              |                                                              |
| fs.oss.endpoint                                            |                                                              |                                                              |
| fs.swift.auth.method                                       |                                                              | 选择身份验证方法：[tempauth (default), swiftauth, keystone, keystonev3] |
| fs.swift.auth.url                                          |                                                              | REST服务器的认证URL，例如http://server:8090/auth/v1.0        |
| fs.swift.password                                          |                                                              | user:tenant认证的密码                                        |
| fs.swift.region                                            |                                                              | Keystone认证的服务地区                                       |
| fs.swift.simulation                                        |                                                              | 是否模拟单节点Swift后台用于验证，默认为否                    |
| fs.swift.tenant                                            |                                                              | Swift user用于认证                                           |
| fs.swift.user                                              |                                                              | Swift tenant用于认证                                         |

## Master配置项

Master配置项指定master节点的信息，例如地址和端口号。

| 属性名                                                       | 默认值                                                       | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| alluxio.master.audit.logging.enabled                         | false                                                        |                                                              |
| alluxio.master.audit.logging.queue.capacity                  | 10000                                                        |                                                              |
| alluxio.master.backup.abandon.timeout                        | 1min                                                         |                                                              |
| alluxio.master.backup.connect.interval.max                   | 30sec                                                        |                                                              |
| alluxio.master.backup.connect.interval.min                   | 1sec                                                         |                                                              |
| alluxio.master.backup.delegation.enabled                     | false                                                        |                                                              |
| alluxio.master.backup.directory                              | /alluxio_backups                                             |                                                              |
| alluxio.master.backup.entry.buffer.count                     | 10000                                                        |                                                              |
| alluxio.master.backup.heartbeat.interval                     | 2sec                                                         |                                                              |
| alluxio.master.backup.state.lock.exclusive.duration          | 0ms                                                          |                                                              |
| alluxio.master.backup.state.lock.forced.duration             | 15min                                                        |                                                              |
| alluxio.master.backup.state.lock.interrupt.cycle.enabled     | true                                                         |                                                              |
| alluxio.master.backup.state.lock.interrupt.cycle.interval    | 30sec                                                        |                                                              |
| alluxio.master.backup.transport.timeout                      | 30sec                                                        |                                                              |
| alluxio.master.bind.host                                     | 0.0.0.0                                                      | Alluxio master绑定的主机名。参考[多宿主网络](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#configure-multihomed-networks) |
| alluxio.master.daily.backup.enabled                          | false                                                        |                                                              |
| alluxio.master.daily.backup.files.retained                   | 3                                                            |                                                              |
| alluxio.master.daily.backup.state.lock.grace.mode            | FORCED                                                       |                                                              |
| alluxio.master.daily.backup.state.lock.sleep.duration        | 10m                                                          |                                                              |
| alluxio.master.daily.backup.state.lock.timeout               | 12h                                                          |                                                              |
| alluxio.master.daily.backup.state.lock.try.duration          | 30s                                                          |                                                              |
| alluxio.master.daily.backup.time                             | 05:00                                                        |                                                              |
| alluxio.master.embedded.journal.addresses                    |                                                              |                                                              |
| alluxio.master.embedded.journal.appender.batch.size          | 512KB                                                        |                                                              |
| alluxio.master.embedded.journal.bind.host                    |                                                              |                                                              |
| alluxio.master.embedded.journal.catchup.retry.wait           | 1s                                                           |                                                              |
| alluxio.master.embedded.journal.election.timeout             | 10s                                                          |                                                              |
| alluxio.master.embedded.journal.heartbeat.interval           | 3s                                                           |                                                              |
| alluxio.master.embedded.journal.port                         | 19200                                                        |                                                              |
| alluxio.master.embedded.journal.shutdown.timeout             | 10sec                                                        |                                                              |
| alluxio.master.embedded.journal.snapshot.replication.chunk.size | 4MB                                                          |                                                              |
| alluxio.master.embedded.journal.storage.level                | DISK                                                         |                                                              |
| alluxio.master.embedded.journal.transport.max.inbound.message.size | 100MB                                                        |                                                              |
| alluxio.master.embedded.journal.transport.request.timeout.ms | 5sec                                                         |                                                              |
| alluxio.master.embedded.journal.triggered.snapshot.wait.timeout | 2hour                                                        |                                                              |
| alluxio.master.embedded.journal.write.local.first.enabled    | true                                                         |                                                              |
| alluxio.master.embedded.journal.write.timeout                | 30sec                                                        |                                                              |
| alluxio.master.file.access.time.journal.flush.interval       | 1h                                                           |                                                              |
| alluxio.master.file.access.time.update.precision             | 1d                                                           |                                                              |
| alluxio.master.file.access.time.updater.shutdown.timeout     | 1sec                                                         |                                                              |
| alluxio.master.filesystem.liststatus.result.message.length   | 10000                                                        |                                                              |
| alluxio.master.format.file.prefix                            | _format_                                                     | 当journal被格式化时，在joural文件夹下生成的文件的文件名前缀。当判断journal是否被格式化时master会查找文件名以该前缀开头的文件。 |
| alluxio.master.heartbeat.timeout                             | 10min                                                        |                                                              |
| alluxio.master.hostname                                      |                                                              | Alluxio master主机名。                                       |
| alluxio.master.journal.checkpoint.period.entries             | 2000000                                                      | 在创建一个新journal检查点之前写入的journal数。               |
| alluxio.master.journal.flush.batch.time                      | 5ms                                                          | 等待批处理日志写入的时间。                                   |
| alluxio.master.journal.flush.timeout                         | 5min                                                         | 在放弃和关闭master之前保持重试日志写入的时间量。             |
| alluxio.master.journal.folder                                | ${alluxio.work.dir}/journal                                  | 存储master journal日志的路径。                               |
| alluxio.master.journal.gc.period                             | 2min                                                         | 扫描和删除陈旧的journal检查点的频率。                        |
| alluxio.master.journal.gc.threshold                          | 5min                                                         | 垃圾收集检查点的最小年龄。                                   |
| alluxio.master.journal.init.from.backup                      |                                                              |                                                              |
| alluxio.master.journal.log.size.bytes.max                    | 10MB                                                         | 如果一个日志文件大小超过该值，会产生下一个文件。             |
| alluxio.master.journal.retry.interval                        | 1sec                                                         |                                                              |
| alluxio.master.journal.tailer.shutdown.quiet.wait.time       | 5sec                                                         | 在备用master停止监听线程之前，在该配置项指定的时间内不应对leader master的journal作任何更新。 |
| alluxio.master.journal.tailer.sleep.time                     | 1sec                                                         | 指定当备用master无法检测到leader master journal的更新时，其睡眠时间。 |
| alluxio.master.journal.temporary.file.gc.threshold           | 30min                                                        | 临时文件垃圾收集检查点的最小年龄。                           |
| alluxio.master.journal.type                                  | EMBEDDED                                                     | 使用journal类型，UFS（存储journal在UFS中）和NOOP（不使用journal）。 |
| alluxio.master.journal.ufs.option                            |                                                              | journal操作使用的配置。                                      |
| alluxio.master.jvm.monitor.enabled                           | true                                                         | 是否在master上启动JVM monitor线程。                          |
| alluxio.master.keytab.file                                   |                                                              | Alluxio master的Kerberos密钥表文件。                         |
| alluxio.master.lock.pool.concurrency.level                   | 100                                                          |                                                              |
| alluxio.master.lock.pool.high.watermark                      | 1000000                                                      |                                                              |
| alluxio.master.lock.pool.initsize                            | 1000                                                         |                                                              |
| alluxio.master.lock.pool.low.watermark                       | 500000                                                       |                                                              |
| alluxio.master.log.config.report.heartbeat.interval          | 1h                                                           |                                                              |
| alluxio.master.lost.worker.file.detection.interval           | 10sec                                                        |                                                              |
| alluxio.master.metadata.sync.concurrency.level               | 6                                                            |                                                              |
| alluxio.master.metadata.sync.executor.pool.size              | The total number of threads which can concurrently execute metadata sync operations. |                                                              |
| alluxio.master.metadata.sync.ufs.prefetch.pool.size          | The number of threads which can concurrently fetch metadata from UFSes during a metadata sync operations |                                                              |
| alluxio.master.metastore                                     | HEAP                                                         |                                                              |
| alluxio.master.metastore.dir                                 | ${alluxio.work.dir}/metastore                                |                                                              |
| alluxio.master.metastore.inode.cache.evict.batch.size        | 1000                                                         |                                                              |
| alluxio.master.metastore.inode.cache.high.water.mark.ratio   | 0.85                                                         |                                                              |
| alluxio.master.metastore.inode.cache.low.water.mark.ratio    | 0.8                                                          |                                                              |
| alluxio.master.metastore.inode.cache.max.size                | 10000000                                                     |                                                              |
| alluxio.master.metastore.inode.enumerator.buffer.count       | 10000                                                        |                                                              |
| alluxio.master.metastore.inode.inherit.owner.and.group       | true                                                         |                                                              |
| alluxio.master.metastore.inode.iteration.crawler.count       | Use {CPU core count} for enumeration                         |                                                              |
| alluxio.master.metastore.iterator.readahead.size             | 64MB                                                         |                                                              |
| alluxio.master.metrics.service.threads                       | 5                                                            |                                                              |
| alluxio.master.metrics.time.series.interval                  | 5min                                                         |                                                              |
| alluxio.master.mount.table.root.alluxio                      | /                                                            | Alluxio mount根节点。                                        |
| alluxio.master.mount.table.root.option                       |                                                              | Alluxio mount根节点UFS配置。                                 |
| alluxio.master.mount.table.root.readonly                     | false                                                        | Alluxio mount根节点是否只读。                                |
| alluxio.master.mount.table.root.shared                       | true                                                         | Alluxio mount根节点是否共享。                                |
| alluxio.master.mount.table.root.ufs                          | ${alluxio.work.dir}/underFSStorage                           | 挂载到Alluxio mount根节点的UFS。                             |
| alluxio.master.network.max.inbound.message.size              | 100MB                                                        |                                                              |
| alluxio.master.periodic.block.integrity.check.interval       | 1hr                                                          | 块完整性检查的间隔，如果小于0则不启用。                      |
| alluxio.master.periodic.block.integrity.check.repair         | false                                                        | 完整性检查时是否要删除孤儿块。这是个实验性的属性。           |
| alluxio.master.persistence.blacklist                         |                                                              |                                                              |
| alluxio.master.persistence.checker.interval                  | 1s                                                           |                                                              |
| alluxio.master.persistence.initial.interval                  | 1s                                                           |                                                              |
| alluxio.master.persistence.max.interval                      | 1hr                                                          |                                                              |
| alluxio.master.persistence.max.total.wait.time               | 1day                                                         |                                                              |
| alluxio.master.persistence.scheduler.interval                | 1s                                                           |                                                              |
| alluxio.master.principal                                     |                                                              | Alluxio master的Kerberos主体。                               |
| alluxio.master.replication.check.interval                    | 1min                                                         |                                                              |
| alluxio.master.rpc.addresses                                 |                                                              |                                                              |
| alluxio.master.rpc.executor.core.pool.size                   | 0                                                            |                                                              |
| alluxio.master.rpc.executor.keepalive                        | 60sec                                                        |                                                              |
| alluxio.master.rpc.executor.max.pool.size                    | 500                                                          |                                                              |
| alluxio.master.rpc.executor.min.runnable                     | 1                                                            |                                                              |
| alluxio.master.rpc.executor.parallelism                      | 2 * {CPU core count}                                         |                                                              |
| alluxio.master.rpc.port                                      | 19998                                                        | Alluxio master的运行端口。                                   |
| alluxio.master.shell.backup.state.lock.grace.mode            | TIMEOUT                                                      |                                                              |
| alluxio.master.shell.backup.state.lock.sleep.duration        | 0                                                            |                                                              |
| alluxio.master.shell.backup.state.lock.timeout               | 1m                                                           |                                                              |
| alluxio.master.shell.backup.state.lock.try.duration          | 1m                                                           |                                                              |
| alluxio.master.standby.heartbeat.interval                    | 2min                                                         | Alluxio master进程间的心跳间隔时间。                         |
| alluxio.master.startup.block.integrity.check.enabled         | true                                                         | 是否应该在启动时检查系统孤立的块(由于各种系统故障而没有相应文件但仍然占用系统资源的块)。如果此属性为真，则在主启动期间将删除孤立的块。此属性自1.7.1开始可用。 |
| alluxio.master.tieredstore.global.level0.alias               | MEM                                                          | 整个系统中最高存储层的名称。                                 |
| alluxio.master.tieredstore.global.level1.alias               | SSD                                                          | 整个系统中第二存储层的名称。                                 |
| alluxio.master.tieredstore.global.level2.alias               | HDD                                                          | 整个系统中第三存储层的名称。                                 |
| alluxio.master.tieredstore.global.levels                     | 3                                                            | 系统中存储层的总数目。                                       |
| alluxio.master.tieredstore.global.mediumtype                 | MEM, SSD, HDD                                                |                                                              |
| alluxio.master.ttl.checker.interval                          | 1hour                                                        | 清除过期ttl值的文件任务的时间间隔。                          |
| alluxio.master.ufs.active.sync.event.rate.interval           | 60sec                                                        |                                                              |
| alluxio.master.ufs.active.sync.interval                      | 30sec                                                        |                                                              |
| alluxio.master.ufs.active.sync.max.activities                | 10                                                           |                                                              |
| alluxio.master.ufs.active.sync.max.age                       | 10                                                           |                                                              |
| alluxio.master.ufs.active.sync.poll.batch.size               | 1024                                                         |                                                              |
| alluxio.master.ufs.active.sync.poll.timeout                  | 10sec                                                        |                                                              |
| alluxio.master.ufs.active.sync.retry.timeout                 | 10sec                                                        |                                                              |
| alluxio.master.ufs.active.sync.thread.pool.size              | The number of threads used by the active sync provider process active sync events. A higher number allow the master to use more CPU to process events from an event stream in parallel. If this value is too low, Alluxio may fall behind processing events. Defaults to # of processors / 2 |                                                              |
| alluxio.master.ufs.block.location.cache.capacity             | 1000000                                                      | UFS块缓存的容量。这个cache缓存UFS块位置，适用于要保存但不在Alluxio空间中的文件，以便这些文件的列表状态不需要反复询问UFS的块位置。如果将此设置为0，则缓存将被禁用。 |
| alluxio.master.ufs.path.cache.capacity                       | 100000                                                       | UFS路径缓存的容量。此缓存用来近似`一次性`元数据加载行为。（查看 `alluxio.user.file.metadata.load.type`)。更大的缓存将耗费更大的内存，但是能够更好地近似`一次性`行为。 |
| alluxio.master.ufs.path.cache.threads                        | 64                                                           | 线程池（可异步处理路径，用于缓存UFS路径）的最大容积。更多的线程数将减少异步缓存中的staleness数量，但可能会影响性能。 如果设置为0，缓存将被禁用，而alluxio.user.file.metadata.load.type = Once将表现为“Always”。 |
| alluxio.master.unsafe.direct.persist.object.enabled          | true                                                         |                                                              |
| alluxio.master.update.check.enabled                          | true                                                         |                                                              |
| alluxio.master.update.check.interval                         | 7day                                                         |                                                              |
| alluxio.master.web.bind.host                                 | 0.0.0.0                                                      | Alluxio master web UI绑定的主机名。参考[多宿主网络](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#configure-multihomed-networks) |
| alluxio.master.web.hostname                                  |                                                              | 提供Alluxio Master web UI的主机名。                          |
| alluxio.master.web.port                                      | 19999                                                        | Alluxio web UI运行端口。                                     |
| alluxio.master.whitelist                                     | /                                                            | 以该配置中的前缀开头的路径是可缓存的，这些前缀用分号隔开。Alluxio在第一次读这些文件时会尝试缓存这些可缓存的文件。 |
| alluxio.master.worker.connect.wait.time                      | 5sec                                                         | 在开始接受client请求之前，Alluxio master会等待一段时间，让所有worker注册。此属性决定等待时间。 |
| alluxio.master.worker.info.cache.refresh.time                | 10sec                                                        |                                                              |
| alluxio.master.worker.timeout                                | 5min                                                         | Alluxio master与worker之间响应的最大超时时间，超过该时间表明该worker失效。 |

## Worker配置项

Worker配置项指定worker节点的信息，例如地址和端口号。

| 属性名                                                     | 默认值                                                       | 描述                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| alluxio.worker.allocator.class                             | alluxio.worker.block.allocator.MaxFreeAllocator              | worker在特定存储层上分配不同存储目录空间的策略，有效值包括：`alluxio.worker.block.allocator.MaxFreeAllocator`, `alluxio.worker.block.allocator.GreedyAllocator`, `alluxio.worker.block.allocator.RoundRobinAllocator`。 |
| alluxio.worker.bind.host                                   | 0.0.0.0                                                      | Alluxio worker节点绑定的主机名，参考[多宿主网络](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#configure-multihomed-networks) |
| alluxio.worker.block.annotator.class                       | alluxio.worker.block.annotator.LRUAnnotator                  |                                                              |
| alluxio.worker.block.annotator.lrfu.attenuation.factor     | 2.0                                                          |                                                              |
| alluxio.worker.block.annotator.lrfu.step.factor            | 0.25                                                         |                                                              |
| alluxio.worker.block.heartbeat.interval                    | 1sec                                                         | worker心跳时间间隔。                                         |
| alluxio.worker.block.heartbeat.timeout                     | ${alluxio.worker.master.connect.retry.timeout}               | worker心跳超时时间。                                         |
| alluxio.worker.block.master.client.pool.size               | 11                                                           | block master在Alluxio worker上的client池容量。               |
| alluxio.worker.container.hostname                          |                                                              |                                                              |
| alluxio.worker.data.folder                                 | /alluxioworker/                                              | 每个存储目录中的一个相对路径，该路径被Alluxio worker用作层次化存储中存放数据的文件夹。 |
| alluxio.worker.data.folder.permissions                     | rwxrwxrwx                                                    |                                                              |
| alluxio.worker.data.folder.tmp                             | .tmp_blocks                                                  | 相对于 alluxio.worker.data.folder 的路径, 用于存放临时数据.  |
| alluxio.worker.data.server.class                           | alluxio.worker.grpc.GrpcDataServer                           | 选择运行worker的网络栈，可选值为：`alluxio.worker.netty.NettyDataServer`。 |
| alluxio.worker.data.server.domain.socket.address           |                                                              | domain socket 路径。如果设置，Alluxio worker 通过这个路径读写数据。 |
| alluxio.worker.data.server.domain.socket.as.uuid           | false                                                        | 如果为真，则属性worker.data.server.domain.socket是域套接字的主目录的路径，也是唯一标识符用作域套接字名称。此外，客户端忽略alluxio.user.hostname在检测本地工作人员进行短路操作时。如果为false，则该属性是UNIX域套接字的绝对路径。 |
| alluxio.worker.data.tmp.subdir.max                         | 1024                                                         | 在 alluxio.worker.data.folder.tmp 中可以创建的文件夹的最大数目. |
| alluxio.worker.evictor.class                               |                                                              | 当某个存储层空间不足时，worker剔除块文件的策略。可选值包括`alluxio.worker.block.evictor.LRFUEvictor`、 `alluxio.worker.block.evictor.GreedyEvictor`、 `alluxio.worker.block.evictor.LRUEvictor`。 |
| alluxio.worker.file.buffer.size                            | 1MB                                                          | worker将数据写入分层存储的缓冲区大小。                       |
| alluxio.worker.free.space.timeout                          | 10sec                                                        | worker等待驱逐来为客户端写请求提供空间的持续时间。           |
| alluxio.worker.hostname                                    |                                                              | Alluxio worker的主机名。                                     |
| alluxio.worker.jvm.monitor.enabled                         | true                                                         | 是否在worker上启用JVM monitor线程。                          |
| alluxio.worker.keytab.file                                 |                                                              | Alluxio worker的Kerberos密钥对文件。                         |
| alluxio.worker.management.backoff.strategy                 | ANY                                                          |                                                              |
| alluxio.worker.management.block.transfer.concurrency.limit | Use {CPU core count}/2 threads block transfer                |                                                              |
| alluxio.worker.management.load.detection.cool.down.time    | 10sec                                                        |                                                              |
| alluxio.worker.management.task.thread.count                | Use {CPU core count} threads for all management tasks        |                                                              |
| alluxio.worker.management.tier.align.enabled               | true                                                         |                                                              |
| alluxio.worker.management.tier.align.range                 | 100                                                          |                                                              |
| alluxio.worker.management.tier.align.reserved.bytes        | 1GB                                                          |                                                              |
| alluxio.worker.management.tier.promote.enabled             | true                                                         |                                                              |
| alluxio.worker.management.tier.promote.quota.percent       | 90                                                           |                                                              |
| alluxio.worker.management.tier.promote.range               | 100                                                          |                                                              |
| alluxio.worker.management.tier.swap.restore.enabled        | true                                                         |                                                              |
| alluxio.worker.master.connect.retry.timeout                | 1hour                                                        |                                                              |
| alluxio.worker.master.periodical.rpc.timeout               | 5min                                                         |                                                              |
| alluxio.worker.network.async.cache.manager.threads.max     | 8                                                            |                                                              |
| alluxio.worker.network.block.reader.threads.max            | 2048                                                         |                                                              |
| alluxio.worker.network.block.writer.threads.max            | 1024                                                         |                                                              |
| alluxio.worker.network.flowcontrol.window                  | 2MB                                                          |                                                              |
| alluxio.worker.network.keepalive.time                      | 30sec                                                        |                                                              |
| alluxio.worker.network.keepalive.timeout                   | 30sec                                                        |                                                              |
| alluxio.worker.network.max.inbound.message.size            | 4MB                                                          |                                                              |
| alluxio.worker.network.netty.boss.threads                  | 1                                                            | 收到新的请求时启用的线程数目。                               |
| alluxio.worker.network.netty.channel                       | EPOLL                                                        | netty通道类型：NIO或EPOLL。                                  |
| alluxio.worker.network.netty.shutdown.quiet.period         | 2sec                                                         | 沉默期时间长度。当netty服务器正终止时，要确保在该时间段内不会产生RPC调用。如果出现了RPC调用，那么在该netty服务器终止时会该沉默期会重新开始。 |
| alluxio.worker.network.netty.watermark.high                | 32KB                                                         | 在切换到不可写状态之前，写队列中可存放的最大字节数。         |
| alluxio.worker.network.netty.watermark.low                 | 8KB                                                          | 一旦写队列中的high watermark达到了，该队列在切换到可写状态之前必须刷新到该配置项指定的low watermark。 |
| alluxio.worker.network.netty.worker.threads                | 0                                                            | 处理请求的线程数目，0表示#cpuCores * 2                       |
| alluxio.worker.network.reader.buffer.size                  | 4MB                                                          |                                                              |
| alluxio.worker.network.reader.max.chunk.size.bytes         | 2MB                                                          |                                                              |
| alluxio.worker.network.shutdown.timeout                    | 15sec                                                        |                                                              |
| alluxio.worker.network.writer.buffer.size.messages         | 8                                                            |                                                              |
| alluxio.worker.network.zerocopy.enabled                    | true                                                         |                                                              |
| alluxio.worker.principal                                   |                                                              | Alluxio worker的Kerberos主体。                               |
| alluxio.worker.ramdisk.size                                | 2/3 of total system memory, or 1GB if system memory size cannot be determined | 每个worker节点的内存容量。                                   |
| alluxio.worker.rpc.port                                    | 29999                                                        | Alluxio worker节点运行端口。                                 |
| alluxio.worker.session.timeout                             | 1min                                                         | worker和client连接的超时时间，超时后表明该会话失效。         |
| alluxio.worker.storage.checker.enabled                     | true                                                         |                                                              |
| alluxio.worker.tieredstore.block.lock.readers              | 1000                                                         | 一个Alluxio数据块锁最大允许的并行读数目。                    |
| alluxio.worker.tieredstore.block.locks                     | 1000                                                         | 一个Alluxio数据块worker的数据块锁数目。较大值会达到更好的锁粒度，但会使用更多空间。 |
| alluxio.worker.tieredstore.free.ahead.bytes                | 0                                                            |                                                              |
| alluxio.worker.tieredstore.level0.alias                    | MEM                                                          | 在worker上最高存储层的别名，该值一定要对应master配置项中全局存储层之一。禁止将全局继承结构中较低级别存储层的别名放在worker中较高级别，因此默认情况下，在任何worker上SSD都不能在MEM之前。 |
| alluxio.worker.tieredstore.level0.dirs.mediumtype          | ${alluxio.worker.tieredstore.level0.alias}                   |                                                              |
| alluxio.worker.tieredstore.level0.dirs.path                | /mnt/ramdisk on Linux, /Volumes/ramdisk on OSX               | 顶层存储层在存储目录中的路径。注意对于MacoS该值应为`/Volumes/`。 |
| alluxio.worker.tieredstore.level0.dirs.quota               | ${alluxio.worker.ramdisk.size}                               | 顶层存储层容量。                                             |
| alluxio.worker.tieredstore.level0.watermark.high.ratio     | 0.95                                                         | 在顶层存储层中的高水位比例 （取值为0到1之间）。              |
| alluxio.worker.tieredstore.level0.watermark.low.ratio      | 0.7                                                          | 在顶层存储层中的低水位比例 （取值为0到1之间）。              |
| alluxio.worker.tieredstore.level1.alias                    |                                                              |                                                              |
| alluxio.worker.tieredstore.level1.dirs.mediumtype          | ${alluxio.worker.tieredstore.level1.alias}                   |                                                              |
| alluxio.worker.tieredstore.level1.dirs.path                |                                                              |                                                              |
| alluxio.worker.tieredstore.level1.dirs.quota               |                                                              |                                                              |
| alluxio.worker.tieredstore.level1.watermark.high.ratio     | 0.95                                                         |                                                              |
| alluxio.worker.tieredstore.level1.watermark.low.ratio      | 0.7                                                          |                                                              |
| alluxio.worker.tieredstore.level2.alias                    |                                                              |                                                              |
| alluxio.worker.tieredstore.level2.dirs.mediumtype          | ${alluxio.worker.tieredstore.level2.alias}                   |                                                              |
| alluxio.worker.tieredstore.level2.dirs.path                |                                                              |                                                              |
| alluxio.worker.tieredstore.level2.dirs.quota               |                                                              |                                                              |
| alluxio.worker.tieredstore.level2.watermark.high.ratio     | 0.95                                                         |                                                              |
| alluxio.worker.tieredstore.level2.watermark.low.ratio      | 0.7                                                          |                                                              |
| alluxio.worker.tieredstore.levels                          | 1                                                            | worker上的存储层数目。                                       |
| alluxio.worker.ufs.block.open.timeout                      | 5min                                                         | 从UFS打开一个块的时限。                                      |
| alluxio.worker.ufs.instream.cache.enabled                  | true                                                         | 在存储输入流下启用缓存，以便以后在同一个文件上查找操作可重用缓存的输入流。这将提高位置读取性能，因为一些文件系统的打开操作是昂贵的。当UFS文件被修改时，缓存的输入流将过时，而不通知Alluxio。 |
| alluxio.worker.ufs.instream.cache.expiration.time          | 5min                                                         | 缓存的UFS输入流过期时间。                                    |
| alluxio.worker.ufs.instream.cache.max.size                 | 5000                                                         | UFS输入流缓存中最大输入数。                                  |
| alluxio.worker.web.bind.host                               | 0.0.0.0                                                      | Alluxio worker web服务绑定的主机名，参考See [多宿主网络](https://docs.alluxio.io/os/user/stable/cn/reference/Properties-List.html#configure-multihomed-networks) |
| alluxio.worker.web.hostname                                |                                                              | Alluxio worker web UI绑定的主机名。                          |
| alluxio.worker.web.port                                    | 30000                                                        | Alluxio worker web UI运行的端口号。                          |

## 用户配置项

用户配置项指定了文件系统访问的相关信息。

| 属性名                                                       | 默认值                                            | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------ |
| alluxio.user.app.id                                          |                                                   |                                                              |
| alluxio.user.block.avoid.eviction.policy.reserved.size.bytes | 0MB                                               |                                                              |
| alluxio.user.block.master.client.pool.gc.interval            | 120sec                                            |                                                              |
| alluxio.user.block.master.client.pool.gc.threshold           | 120sec                                            |                                                              |
| alluxio.user.block.master.client.pool.size.max               | 10                                                |                                                              |
| alluxio.user.block.master.client.pool.size.min               | 0                                                 |                                                              |
| alluxio.user.block.read.retry.max.duration                   | 2min                                              |                                                              |
| alluxio.user.block.read.retry.sleep.base                     | 250ms                                             |                                                              |
| alluxio.user.block.read.retry.sleep.max                      | 2sec                                              |                                                              |
| alluxio.user.block.remote.read.buffer.size.bytes             | 8MB                                               | 从远程Alluxio worker读取数据时的缓冲区大小。它决定了一个Alluxio client和一个Alluxio worker之间Thrift connections的最大数量 |
| alluxio.user.block.size.bytes.default                        | 64MB                                              | Alluxio文件的默认大小。                                      |
| alluxio.user.block.worker.client.pool.gc.threshold           | 300sec                                            | 数据块worker client如果闲置超过这个时间会被关闭。            |
| alluxio.user.block.worker.client.pool.max                    | 1024                                              |                                                              |
| alluxio.user.block.write.location.policy.class               | alluxio.client.block.policy.LocalFirstPolicy      | 选择worker进行写文件数据块时的默认定位机制。                 |
| alluxio.user.client.cache.async.restore.enabled              | true                                              |                                                              |
| alluxio.user.client.cache.async.write.enabled                | true                                              |                                                              |
| alluxio.user.client.cache.async.write.threads                | 16                                                |                                                              |
| alluxio.user.client.cache.dir                                | /tmp/alluxio_cache                                |                                                              |
| alluxio.user.client.cache.enabled                            | false                                             |                                                              |
| alluxio.user.client.cache.evictor.class                      | alluxio.client.file.cache.evictor.LRUCacheEvictor |                                                              |
| alluxio.user.client.cache.evictor.lfu.logbase                | 2.0                                               |                                                              |
| alluxio.user.client.cache.local.store.file.buckets           | 1000                                              |                                                              |
| alluxio.user.client.cache.page.size                          | 1MB                                               |                                                              |
| alluxio.user.client.cache.size                               | 512MB                                             |                                                              |
| alluxio.user.client.cache.store.type                         | LOCAL                                             |                                                              |
| alluxio.user.conf.cluster.default.enabled                    | true                                              |                                                              |
| alluxio.user.conf.sync.interval                              | 1min                                              |                                                              |
| alluxio.user.date.format.pattern                             | MM-dd-yyyy HH:mm:ss:SSS                           | 以指定的日期格式，在Cli命令和Web页面中显示日期。             |
| alluxio.user.file.buffer.bytes                               | 8MB                                               | 在文件系统中进行读写操作时使用的缓冲区大小。                 |
| alluxio.user.file.copyfromlocal.block.location.policy.class  | alluxio.client.block.policy.RoundRobinPolicy      |                                                              |
| alluxio.user.file.create.ttl                                 | -1                                                |                                                              |
| alluxio.user.file.create.ttl.action                          | DELETE                                            |                                                              |
| alluxio.user.file.delete.unchecked                           | false                                             | 在尝试以递归方式删除持久化目录之前，检查底层文件系统中的内容是否与Alluxio同步。 |
| alluxio.user.file.master.client.pool.gc.interval             | 120sec                                            |                                                              |
| alluxio.user.file.master.client.pool.gc.threshold            | 120sec                                            |                                                              |
| alluxio.user.file.master.client.pool.size.max                | 10                                                |                                                              |
| alluxio.user.file.master.client.pool.size.min                | 0                                                 |                                                              |
| alluxio.user.file.metadata.load.type                         | ONCE                                              | 从UFS中加载元数据的行为。当访问关于路径的信息，但该路径在Alluxio中不存在时，元数据能够从UFS中加载。合法的选项有`Always`，`Never`，`Once`。`Always`将总是访问UFS来看路径是否存在于UFS中。`Never`表示从来不会访问UFS。`Once`表示在”首次“的时候会访问UFS（根据缓存），但是以后都不会在访问。默认值为`Once`。 |
| alluxio.user.file.metadata.sync.interval                     | -1                                                | 在调用路径上的操作之前同步UFS元数据的时间间隔。-1表示不会发生同步。0意味着在操作之前，代理总是会同步路径的元数据。如果指定了一个时间间隔，就可以在该时间间隔内(尽可能)不重新同步路径。同步路径的元数据必须与UFS交互，所以这是一个昂贵的操作。如果对一个操作执行同步，则配置为“alluxio.user.file.metadata.load”将被忽略。 |
| alluxio.user.file.passive.cache.enabled                      | true                                              | 当从Alluxio远程worker读文件时，是否缓存文件到Alluxio的本地worker。当从UFS读文件时，是否缓存到本地worker与这个选项无关。 |
| alluxio.user.file.persist.on.rename                          | false                                             |                                                              |
| alluxio.user.file.persistence.initial.wait.time              | 0                                                 |                                                              |
| alluxio.user.file.readtype.default                           | CACHE                                             | 创建Alluxio文件时的默认读类型。可选值为`CACHE_PROMOTE` (如果数据已经在Alluxio存储内，将其移动到最高存储层，如果数据需要从底层存储进行读取，将其写到本地Alluxio的最高存储层)、`CACHE` (如果数据需要从底层存储进行读取，将其写到本地Alluxio的最高存储层), `NO_CACHE` (数据不与Alluxio交互，如果是从Alluxio中进行读取，将不会发生数据块迁移或者剔除)。 |
| alluxio.user.file.replication.durable                        | 1                                                 |                                                              |
| alluxio.user.file.replication.max                            | -1                                                |                                                              |
| alluxio.user.file.replication.min                            | 0                                                 |                                                              |
| alluxio.user.file.reserved.bytes                             | ${alluxio.user.block.size.bytes.default}          |                                                              |
| alluxio.user.file.sequential.pread.threshold                 | 2MB                                               |                                                              |
| alluxio.user.file.target.media                               |                                                   |                                                              |
| alluxio.user.file.ufs.tier.enabled                           | false                                             |                                                              |
| alluxio.user.file.waitcompleted.poll                         | 1sec                                              | 当使用waitCompleted机制时，查询文件完成状态的时间间隔。      |
| alluxio.user.file.write.tier.default                         | 0                                                 | 数据块写入的默认存储层。可选值为整型数值。非负值代表从高层到底层的存储层（0代表第一层存储层，１代表第二层存储层，以此类推）。如果给定值大于存储层数量,这个数字代表最底层的存储层。负值代表从底层到高层的存储层（-1代表最底层存储层，-2代表次底层存储层，以此类推）如果给定值的绝对值大于存储层数量，这个数字代表最高层存储层。 |
| alluxio.user.file.writetype.default                          | ASYNC_THROUGH                                     | 创建Alluxio文件时的默认写类型。可选值为`MUST_CACHE` (数据仅仅存储在Alluxio中，并且必须存储在其中), `CACHE_THROUGH` (尽量缓冲数据，同时同步写入到底层文件系统), `THROUGH` (不缓冲数据，同步写入到底层文件系统)。 |
| alluxio.user.hostname                                        |                                                   | 给alluxio客户端使用的主机名。                                |
| alluxio.user.local.reader.chunk.size.bytes                   | 8MB                                               |                                                              |
| alluxio.user.local.writer.chunk.size.bytes                   | 64KB                                              |                                                              |
| alluxio.user.logging.threshold                               | 10s                                               |                                                              |
| alluxio.user.logs.dir                                        | ${alluxio.logs.dir}/user                          |                                                              |
| alluxio.user.master.polling.timeout                          | 30sec                                             |                                                              |
| alluxio.user.metadata.cache.enabled                          | false                                             |                                                              |
| alluxio.user.metadata.cache.expiration.time                  | 10min                                             |                                                              |
| alluxio.user.metadata.cache.max.size                         | 100000                                            |                                                              |
| alluxio.user.metrics.collection.enabled                      | false                                             |                                                              |
| alluxio.user.metrics.heartbeat.interval                      | 10sec                                             |                                                              |
| alluxio.user.network.data.timeout                            |                                                   |                                                              |
| alluxio.user.network.flowcontrol.window                      |                                                   |                                                              |
| alluxio.user.network.keepalive.time                          |                                                   |                                                              |
| alluxio.user.network.keepalive.timeout                       |                                                   |                                                              |
| alluxio.user.network.max.inbound.message.size                |                                                   |                                                              |
| alluxio.user.network.netty.channel                           |                                                   | netty网络通道类型。                                          |
| alluxio.user.network.netty.worker.threads                    |                                                   | 远程数据块worker client从远程数据块worker读取数据使用的线程数目。 |
| alluxio.user.network.reader.buffer.size.messages             |                                                   |                                                              |
| alluxio.user.network.reader.chunk.size.bytes                 |                                                   |                                                              |
| alluxio.user.network.rpc.flowcontrol.window                  | 2MB                                               |                                                              |
| alluxio.user.network.rpc.keepalive.time                      | 9223372036854775807                               |                                                              |
| alluxio.user.network.rpc.keepalive.timeout                   | 30sec                                             |                                                              |
| alluxio.user.network.rpc.max.connections                     | 1                                                 |                                                              |
| alluxio.user.network.rpc.max.inbound.message.size            | 100MB                                             |                                                              |
| alluxio.user.network.rpc.netty.channel                       | EPOLL                                             |                                                              |
| alluxio.user.network.rpc.netty.worker.threads                | 0                                                 |                                                              |
| alluxio.user.network.streaming.flowcontrol.window            | 2MB                                               |                                                              |
| alluxio.user.network.streaming.keepalive.time                | 9223372036854775807                               |                                                              |
| alluxio.user.network.streaming.keepalive.timeout             | 30sec                                             |                                                              |
| alluxio.user.network.streaming.max.connections               | 64                                                |                                                              |
| alluxio.user.network.streaming.max.inbound.message.size      | 100MB                                             |                                                              |
| alluxio.user.network.streaming.netty.channel                 | EPOLL                                             |                                                              |
| alluxio.user.network.streaming.netty.worker.threads          | 0                                                 |                                                              |
| alluxio.user.network.writer.buffer.size.messages             |                                                   |                                                              |
| alluxio.user.network.writer.chunk.size.bytes                 |                                                   |                                                              |
| alluxio.user.network.writer.close.timeout                    |                                                   |                                                              |
| alluxio.user.network.writer.flush.timeout                    |                                                   |                                                              |
| alluxio.user.network.zerocopy.enabled                        |                                                   |                                                              |
| alluxio.user.rpc.retry.base.sleep                            | 50ms                                              | 在遇到一些错误的时候，Alluxio客户端的RPC会基于指数级的延迟进行重试。这个配置决定了这个指数级重试的基数。 |
| alluxio.user.rpc.retry.max.duration                          | 2min                                              | 在遇到一些错误的时候，Alluxio客户端的RPC会基于指数级的延迟进行重试。这个配置决定了放弃前重试的最大时延。 |
| alluxio.user.rpc.retry.max.sleep                             | 3sec                                              | 在遇到一些错误的时候，Alluxio客户端的RPC会基于指数级的延迟进行重试。这个配置决定了这个重试延迟的最大值。 |
| alluxio.user.short.circuit.enabled                           | true                                              | 是否允许用户绕过Alluxio读取数据。                            |
| alluxio.user.short.circuit.preferred                         | false                                             |                                                              |
| alluxio.user.streaming.data.timeout                          | 30sec                                             |                                                              |
| alluxio.user.streaming.reader.buffer.size.messages           | 16                                                |                                                              |
| alluxio.user.streaming.reader.chunk.size.bytes               | 1MB                                               |                                                              |
| alluxio.user.streaming.writer.buffer.size.messages           | 16                                                |                                                              |
| alluxio.user.streaming.writer.chunk.size.bytes               | 1MB                                               |                                                              |
| alluxio.user.streaming.writer.close.timeout                  | 30min                                             |                                                              |
| alluxio.user.streaming.writer.flush.timeout                  | 30min                                             |                                                              |
| alluxio.user.streaming.zerocopy.enabled                      | true                                              |                                                              |
| alluxio.user.ufs.block.location.all.fallback.enabled         | true                                              |                                                              |
| alluxio.user.ufs.block.read.concurrency.max                  | 2147483647                                        | 一个Block Worker上的一个UFS块并发访问的最大个数。            |
| alluxio.user.ufs.block.read.location.policy                  | alluxio.client.block.policy.LocalFirstPolicy      | 当Alluxio client从UFS读取文件时，它将读取委托给Alluxio worker。client使用此策略来选择要阅读哪个worker。 内置选择有：[[alluxio.client.block.policy.DeterministicHashPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/block/policy/DeterministicHashPolicy.html), [alluxio.client.block.policy.LocalFirstAvoidEvictionPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/file/policy/LocalFirstAvoidEvictionPolicy.html), [alluxio.client.block.policy.LocalFirstPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/file/policy/LocalFirstPolicy.html), [alluxio.client.block.policy.MostAvailableFirstPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/file/policy/MostAvailableFirstPolicy.html), [alluxio.client.block.policy.RoundRobinPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/file/policy/RoundRobinPolicy.html), [alluxio.client.block.policy.SpecificHostPolicy](https://www.alluxio.org/javadoc/master/alluxio/client/file/policy/SpecificHostPolicy.html)] |
| alluxio.user.ufs.block.read.location.policy.deterministic.hash.shards | 1                                                 | 当alluxio.user.ufs.block.read.location.policy设为alluxio.client.block.policy.DeterministicHashPolicy，这设定了hash shards的数量。 |
| alluxio.user.worker.list.refresh.interval                    | 2min                                              |                                                              |

## 集群管理配置项

如果使用诸如Mesos和YARN的集群管理器运行Alluxio，还有额外的配置项。

| 属性名                                        | 默认值 | 描述                                                         |
| --------------------------------------------- | ------ | ------------------------------------------------------------ |
| alluxio.integration.master.resource.cpu       | 1      | 运行Alluxio master所需要的cpu核数。                          |
| alluxio.integration.master.resource.mem       | 1024MB | 运行Alluxio master所需要的内存大小。                         |
| alluxio.integration.worker.resource.cpu       | 1      | 运行Alluxio worker所需要的cpu核数。                          |
| alluxio.integration.worker.resource.mem       | 1024MB | 运行Alluxio worker所需要的内存大小，该部分内存不包含为层次化存储配置的内容。 |
| alluxio.integration.yarn.workers.per.host.max | 1      |                                                              |

## 安全性配置项

安全性配置项指定了安全性相关的信息，如安全认证和文件权限。 安全认证相关的配置同时适用于master、worker和用户。 文件权限相关的配置只对master起作用。 更多安全性相关的信息详见[系统安全文档](https://docs.alluxio.io/os/user/stable/cn/operation/Security.html)。

| 属性名                                                | 默认值                                                      | 描述                                                         |
| ----------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| alluxio.security.authentication.custom.provider.class |                                                             | 当alluxio.security.authentication.type被设为CUSTOM时，实现安全认证功能的类。 该类必须实现'alluxio.security.authentication.AuthenticationProvider'接口。 |
| alluxio.security.authentication.type                  | SIMPLE                                                      | 安全认证模式。目前支持三种模式：NOSASL、SIMPLE和CUSTOM。 默认为SIMPLE，即不启用安全认证功能。 |
| alluxio.security.authorization.permission.enabled     | true                                                        | 是否启用文件权限访问控制功能。                               |
| alluxio.security.authorization.permission.supergroup  | supergroup                                                  | Alluxio文件系统的超级用户组。所有该组下的用户拥有超级权限。  |
| alluxio.security.authorization.permission.umask       | 022                                                         | 创建文件和目录时的文件权限掩码。初始化的创建权限为777，目录和文件之间相差111。 因此，当默认文件权限掩码为022时，新建目录的权限为755，新建文件的权限为644。 |
| alluxio.security.group.mapping.cache.timeout          | 1min                                                        | 缓存的组映射过期时间。                                       |
| alluxio.security.group.mapping.class                  | alluxio.security.group.provider.ShellBasedUnixGroupsMapping | 提供“用户-用户组”映射服务的实现类。Master可以通过该服务得到指定用户所在组的组成员。 该类必须实现'alluxio.security.group.GroupMappingService'接口。 默认的实现类通过执行'groups'命令得到指定用户所在组的组成员。 |
| alluxio.security.login.impersonation.username         | _HDFS_USER_                                                 |                                                              |
| alluxio.security.login.username                       |                                                             | 当alluxio.security.authentication.type被设为SIMPLE或CUSTOM时，用户应用使用此配置项 作为连接Alluxio的用户名。若未设置该项，则使用系统登录用户名。 |
| alluxio.security.stale.channel.purge.interval         | 3day                                                        |                                                              |