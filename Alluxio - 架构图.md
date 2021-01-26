# Alluxio 架构图

[TOC]

### 1 架构

#### 1.1 概述

​       Alluxio作为大数据和机器学习生态系统中的一个新的数据访问层，配置在任何持久性存储系统(如Amazon S3、Microsoft Azure对象存储、Apache HDFS或OpenStack Swift)和计算框架(如Apache Spark、Presto或Hadoop MapReduce)之间。**请注意，Alluxio不是一个持久化存储系统。**使用Alluxio作为数据访问层有如下好处：
 1.对于用户应用程序和计算框架，Alluxio提供了快速存储，促进了作业之间的数据共享和局部性，而不管使用的是哪种计算引擎。因此，当数据位于本地时，Alluxio可以以内存速度提供数据;当数据位于Alluxio时，Alluxio可以以计算集群网络的速度提供数据。第一次访问数据时，只从存储系统上读取一次数据。为了得到更好的性能，Alluxio推荐部署在计算集群上。
 2.对于存储系统，Alluxio弥补了大数据应用与传统存储系统之间的差距，扩大了可用的数据工作负载集。当同时挂载多个数据源时，Alluxio可以作为任意数量的不同数据源的统一层。
​        Alluxio可以被分为三个部分：**masters、workers以及clients。**一个典型的设置由一个主服务器、多个备用服务器和多个worker组成。客户端用于通过Spark或MapReduce作业、Alluxio命令行或FUSE层等应用程序与Alluxio服务器通信。
 



![img](https:////upload-images.jianshu.io/upload_images/2818100-c22f0f1ad903b151.png?imageMogr2/auto-orient/strip|imageView2/2/w/902/format/webp)

image.png



#### 1.2 Master

​       Alluxio主服务可以部署为一个主master和几个备用master，以实现容错。当主master奔溃时，备用master可以被选为新的主master。





![img](https:////upload-images.jianshu.io/upload_images/2818100-c8d715123ad25dae.png?imageMogr2/auto-orient/strip|imageView2/2/w/620/format/webp)

image.png

##### 1.2.1 主master

​       Alluxio中只有一个master进程为主master。主master用于管理全局的元数据。这里面包含文件系统元数据（文件系统节点树）、数据块元数据（数据块位置）、以及worker的容量元数据（空闲或已占用空间）。Alluxio clients与主master通信用来读取或修改元数据。所有的worker都会定期的向主master发送心跳。主master会在一个分布式的持久化系统上记录所有的文件系统事务，这样可以恢复主master的信息。这组日志被称为journal。

##### 1.2.2 备用master

​       备用master读取主master写入的journal日志，以保持与主master的状态同步。它们会对journal日志写入检查点，用于快速恢复。它们不处理来自Alluxio组件的任何请求。

#### 1.3 Worker

​       Alluxio的worker用于管理用户为Alluxio定义的本地资源（内存、SSD、HDD）。Alluxio的worker将数据存储为块，并通过在其本地资源上读或者创建新的数据块来响应client请求。Workers只用于管理数据块；文件到数据块的映射存储在master中。Workers在其底层存储上进行数据操作。这带来两个重要的优势：
 1.从底层存储系统读取的数据能被存储在worker中，这样别的client可以立即使用。
 2.client可以是轻量级的，不依赖于底层存储的连接器。
​        因为RAM的容量有限，所以当空间满了的时候block会被清理。Workers使用清理策略决定什么数据留在Alluxio中。

### 2 数据流

​       这章描述了Alluxio读或写的场景，基于以下的配置：Alluxio与计算集群部署在一起，持久化存储系统可以为远程存储系统或云存储。

#### 2.1 读

​       在底层存储和计算集群间，Alluxio是作为一个缓存层存在的。这小节描述了不同的缓存场景以及其对性能的影响。

##### 2.1.1 本地缓存命中

​       本地缓存命中发生在请求数据位于本地Alluxio worker。举例说明，如果一个应用通过Alluxio client请求数据，client向Alluxio master请求数据所在的worker。如果数据在本地可用，Alluxio client使用“短路”读取来绕过Alluxio worker，并直接通过本地文件系统读取文件。短路读取避免通过TCP套接字传输数据，并提供数据的直接访问。
​        还要注意，Alluxio除了内存之外还可以管理其他存储介质(例如SSD、HDD)，因此本地数据访问速度可能会因本地存储介质的不同而有所不同。





![img](https:////upload-images.jianshu.io/upload_images/2818100-428f5e331c20daf3.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-local-cache-hit.gif

##### 2.1.2 远程缓存命中

​       当请求的数据存储在Alluxio中，而不是存储在client的本地worker上时，client将对具有数据的worker进行远程读取。client完成读取后，会要求本地的worker（如果存在）创建一个copy，这样以后读取的时候可以在本地读取相同的数据。远程缓存击中提供了网络级别速度的数据读取。Alluxio优先从远程worker读取数据，而不是从底层存储，因为Alluxio worker间的速度一般会快过Alluxio workers和底层存储的速度。





![img](https:////upload-images.jianshu.io/upload_images/2818100-5e82e019ccd2adc7.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-remote-cache-hit.gif

##### 2.1.3 缓存Miss

​       如果数据在Alluxio中找不到，则会发生缓存丢失，应用将不得不从底层存储读取数据。Alluxio client会将数据读取请求委托给worker（有限本地worker）。这个worker会从底层存储读取数据并缓存。缓存丢失通常会导致最大的延迟，因为数据必须从底层存储获取。
​        当client只读取块的一部分或不按照顺序读取块时，client将指示worker异步缓存整个块。异步缓存不会阻塞client，但是如果Alluxio和底层存储系统之间的网络带宽是瓶颈，那么异步缓存仍然可能影响性能。





![img](https:////upload-images.jianshu.io/upload_images/2818100-c39208187a411d09.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-cache-miss.gif

#### 2.2 Write

​       用户可以通过选择不同的写类型来配置应该如何写数据。写类型可以通过Alluxio API设置，也可以通过在客户机中配置属性Alluxio .user.file.writetype.default来设置。本节描述不同写类型的行为以及对应用程序的性能影响。

##### 2.2.1 只写入到Alluxio（MAST_CACHE）

​       当写类型设置为MUST_CACHE，Alluxio client将数据写入本地Alluxio worker，而不会写入到底层存储。如果“短路”写可用，Alluxio client直接写入到本地RAM的文件，绕过Alluxio worker，避免网络传输。由于数据没有持久存储在under storage中，因此如果机器崩溃或需要释放数据以进行更新的写操作，数据可能会丢失。当可以容忍数据丢失时，MUST_CACHE设置对于写临时数据非常有用。





![img](https:////upload-images.jianshu.io/upload_images/2818100-730434cf37c52131.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-must-cache.gif

##### 2.2.2 写到UFS（CACHE_THROUGH）





​        使用CACHE_THROUGH写类型，数据被同步地写到一个Alluxio worker和下一个底层存储。Alluxio client将写操作委托给本地worker，而worker同时将对本地内存和底层存储进行写操作。由于底层存储的写入速度通常比本地存储慢，所以client的写入速度将与底层存储的速度相匹配。当需要数据持久化时，建议使用CACHE_THROUGH写类型。在本地还存了一份副本，以便可以直接从本地内存中读取数据。



![img](https:////upload-images.jianshu.io/upload_images/2818100-b3aa259bed810580.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-cache-through.gif

##### 2.2.3 写回UFS（ASYNC_THROUGH）

​       Alluxio提供了一个叫做ASYNC_THROUGH的写类型。数据被同步地写入到一个Alluxio worker，并异步地写入到底层存储。ASYNC_THROUGH可以在持久化数据的同时以内存速度提供数据写入。





![img](https:////upload-images.jianshu.io/upload_images/2818100-1f5fca9b7ae39623.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

dataflow-async-through.gif