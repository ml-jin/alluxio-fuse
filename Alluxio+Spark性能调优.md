# Alluxio + Spark 性能调优

[TOC]

\#refer: http://www.raincent.com/content-85-12931-1.html

由于统一访问对象存储(如S3)和HDFS数据的场景的出现和普及，Apache Spark结合Alluxio的大数据栈越来越受欢迎。此外，越来越流行的计算与存储分离的架构导致计算端查询延迟增大。因此，Alluxio常被用作贴近计算端的热数据存储以提高性能。为了能够获得较佳性能，用户需要像使用其他技术栈组合一样遵循较佳的实战经验。本文介绍了在Alluxio上运行Spark时,对于实际工作负载性能调优的十大技巧。

> **关于数据本地性**

数据本地性就是尽量将计算移到数据所在的节点上进行，避免数据在网络上的传输。分布式数据并行环境下，数据的本地性非常重要。提高数据本地性能够极大地提升Spark作业的性能。如果需要计算的数据存储在节点本地，那么Spark任务可以直接以内存速度(当配置ramdisk时)从本地Alluxio worker中读取Alluxio中的数据，而不必通过网络进行数据传输。首先我们要介绍的几个调优技巧是关于数据本地性方面的。

## 1、检查Spark作业读取Alluxio时的数据本地性**

当Spark worker与Alluxio worker同置部署(co-locate)在同一节点上时，Alluxio能够通过支持短路读写(见文末链接1)为Spark计算任务提供较佳的性能。用户有多种方法检查I/O请求是否实际由Alluxio短路读/写提供数据访问服务，具体方法如下：

方法1：当运行Spark任务时，观察监控页面Alluxio metrics UI page(见文末链接2)上的Short-circuit reads和From RemoteInstance的两个指标。此外，还可以观察cluster.BytesReadAlluxioThroughput和cluster.BytesReadLocalThroughput两个指标的值进行判断。如果数据的本地读写吞吐量为零或远低于总吞吐量，那么说明该Spark作业可能没有本地的Alluxio worker进行数据交互。

方法2：利用dstat(见文末链接3)等工具检测网络流量，这样我们就可以看到短路读取是否发生以及实际的网络吞吐量。YARN用户还可以在/var/log/Hadoop-yarn/userlogs日志中查找如下信息：INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 0ms，观察远程读取是否发生，或者都是短路读取。运行Spark作业时，用户可以通过配置YARN相关参数(例如--master yarn--num-executors 4 --executor-cores 4)来开启收集这些日志。

如果用户通过上述方法发现Alluxio对Spark作业只提供了一小部分短路读取或写入，请阅读下面几条技巧以提升数据本地性。

## 2、确保Spark Executor本地性**

Spark中的数据本地性分为两种：executor 层面的本地性(executor locality)、task 层面的本地性(task locality) 。Executor级别的本地性意味着当Spark作业由其他资源管理框架(如Mesos和YARN)部署时，Spark Executor会被分配在同时运行Alluxio worker的节点上，从而和Alluxio worker运行于统一的节点。如果Executor的本地性没有达到，Alluxio就无法为Spark作业提供本地的数据服务。我们经常会发现，相比于Spark Standalone模式部署，当Spark通过Spark on Yarn或Spark on Mesos等资源管理框架的模式进行部署时，executor本地性经常难以保证。

较新版本的Spark在YARN和Mesos框架调度分发executors时能够考虑数据本地性因素，此外还有方法可以强制在所需节点上启动executors。因此，一个简单的策略是在每个节点上都启动executor，这样能够保证在每个所需节点上都至少有一个executor运行着。

然后，由于实际情况下的资源限制，在通常生产环境中的每个节点上都部署运行Alluxioworker还比较困难。为了能够将Alluxioworker与计算节点同置部署(co-locate)，用户可以利用类似于资源管理框架YARN中的节点标签(见文末链接4)功能。具体地，首先，将包含Alluxioworker的NodeManagers节点标签标记为alluxio(具体标签取名不重要)。然后，用户将Spark作业提交到该alluxio标签指定的节点。最后，系统会在这些机器节点上启动Spark作业的executors并与Alluxio workers进行协同工作。

## 3、确保Spark Task本地性**

Task层面的数据本地性是属于下一层次的本地性，它是由Spark本身决定的。达到Task本地性表示一旦启动executor，Spark能够将task调度到满足数据本地性的executor上执行(在与Alluxio结合的场景下，即在将task调度到通过Alluxio提供本地数据的executor上执行)。为了达到该目标，Spark任务调度器需要首先从Alluxio收集所有数据的位置信息。该位置信息以Alluxio worker的主机名列表进行表示。然后，尝试匹配Spark executor主机名进行调度。用户可以从Alluxio master web UI中查看到Alluxio worker的主机名，也可以通过Spark driver web UI找到Spark executor的主机名。

然而，有时由于不同网络环境或配置方面的原因，在同一台机器上运行的Alluxio worker和Spark executor提供的主机名可能并不匹配。在这种情况下，Spark 的任务调度器将会混淆主机名的匹配从而无法确保数据本地性。因此，Alluxio worker的主机名与Spark executor的主机名采用相同的“主机名名称空间”非常重要。用户经常会遇到的一个问题是：Alluxio worker和Spark executor一个使用IP地址标示，而另一个使用主机名标示。如果发生了这种情况，用户仍然可以在Spark中手动设置Alluxio-client属性alluxio.user.hostname(见文末链接5)，并将Alluxio worker中的alluxio.worker.hostname属性设置为相同值来解决该问题。此外，用户也可以查阅JIRASPARK-10149(见文末链接6)以获取Spark开源社区的解决方案。

## 4、优先考虑Spark调度器中的本地性因素**

关于本地性， Spark客户端有一些配置参数可供调优。具体地，spark.locality.wait：即数据本地性降级的等待时间，默认是3000毫秒，如果超时就启动下一本地优先级别的task。该配置参数同样可以应用到不同优先级的本地性之间(PROCESS_LOCAL，NODE_LOCAL，RACK_LOCAL，ANY)。我们也可以通过将参数spark.locality.wait.node设置为特定的本地性级别，从而实现自定义每个节点的本地性等待时间。

> **负载均衡**

负载均衡同样非常重要，它能够确保Spark作业的执行能够均匀地分布在不同可用节点上。以下的几个技巧介绍如何避免由于Alluxio输入数据倾斜导致负载或者任务调度不均衡。

## 5、使用DeterministicHashPolicy通过Alluxio从UFS冷读数据**

同一个Spark作业的多个task经常会读取相同的输入文件。当该文件没有加载到Alluxio中时，默认情况下，不同task的本地Alluxio worker将从UFS读取相同的文件。这可能导致如下后果：

多个Alluxio worker同时竞争相同数据的Alluxio-UFS连接。如果从Alluxio到UFS的连接速度很慢，每个worker都会因为这些不必要的竞争而造成数据读取速度变慢。

同一份数据会在Alluxio缓存空间中存储多个副本，造成大量的空间浪费，并间接造成那些对其他查询有用的数据被剔出出Alluxio缓存空间。

解决此问题的一种方法是将alluxio.user.ufs.block.read.location.policy设置为DeterministicHashPolicy以协调Alluxio Worker从UFS读取数据的过程，避免产生不必要的竞争。例如，编辑spark/conf/spark-defaults.conf以确保最多允许四个随机Alluxio Worker读取给定数据块。(具体数目由alluxio.user.ufs.block.read.location.policy.deterministic.hash.shards参数决定)

spark.driver.extraJavaOptions= 
-Dalluxio.user.ufs.block.read.location.policy=alluxio.client.block.policy.DeterministicHashPolicy

spark.executor.extraJavaOptions=
-Dalluxio.user.ufs.block.read.location.policy.deterministic.hash.shards=4

注意，通过上述设置，系统将为不同的数据块随机选择一组不同的四个worker与之对应。

## 6、采用更小的Alluxio数据块获得更高的并行度**

数据块(block)是Alluxio系统中最小的存储单元。当Alluxio数据块大小(默认为512MB)相较于一个文件本身很大时，就意味着该文件只包含少数几个数据块。因此，也就只有少数几个Alluxio worker能够为并行地读取文件提供服务。具体地，为了获得数据NODE_LOCAL本地性，在Spark的“文件扫描”阶段，task可能只分配给少数几个服务器处理。这会导致大多数服务器闲置，从而导致集群负载不平衡，处理并发度低下。在这种情况下，使用较小的块来组织存储Alluxio的文件(例如，将alluxio.user.block.size.bytes.default设置为128MB或更小)，能够增加文件的数据块数量，使得文件的数据分散地存储在更多的机器中，从而提升文件数据读取的并行度。。请注意，当UFS是HDFS时，自定义调整Alluxio块大小的方法会不适用，因为Alluxio的数据块大小会被强制设置为HDFS块大小。

## 7、调整优化executor数目以及每个executor的task数目**

在一个executor中运行过多的task并发地从Alluxio中读取数据可能会造成网络连接、网络带宽等资源的争用。基于Alluxio worker节点的数量，用户可以通过合理地调整executor的数量以及每个executor的task数量( Spark中可配)，从而更好地将task分配给更多节点(获取更高的Alluxio带宽)，并减少资源争用的开销。

## 8、预加载数据到Alluxio**

尽管Alluxio提供了透明性和异步缓存机制(见文末链接7)，但是用户的第一次冷读取还是会存在性能开销。为了避免这个问题，用户通过使用以下命令行将数据预加载到Alluxio存储空间中：

$ bin/alluxio  fs  load  /path/to/load 

-Dalluxio.user.ufs.block.read.location.policy=alluxio.client.file.policy.MostAvailableFirstPolicy

注意：load命令只是简单地从底层存储的这台节点中中加载目标文件到Alluxio，因此数据写入到Alluxio的速度往往会受到单个服务器节点的限制。在Alluxio 2.0中，我们计划实现分布式加载数据功能以提高加载的吞吐量。

> **容量管理**

Alluxio为用户提供一个高层的热点输入数据缓存服务。事实上，正确合理的分配和管理缓存的容量配额对取得好性能同样非常重要。

## 9 使用SSD或HDD扩展Alluxio系统存储容量**

Alluxio workers也可以使用本地SSD(固态硬盘)或者HDD(硬盘)来和RAM(内存)存储资源进行互补。为了减少数据被剔除Alluxio存储空间，我们建议在Alluxio存储层中设置多级存储目录而非仅采用单一存储层，详情参见文末链接8。在AWS EC2上运行Alluxio workers的用户需要注意：将EBS存储挂载为本地磁盘会通过网络传输数据，这可能会造成数据访问速度可能很慢。

## 10 关闭被动缓存特性以防缓存抖动**

Alluxio客户端的配置参数alluxio.user.file.passive.cache.enabled可以控制是否在Alluxio中缓存额外的数据副本。这个属性是默认开启的(即alluxio.user.file.passive.cache.enabled=true)。因此，在客户端请求数据时，一个Alluxio worker可能会缓存已经在其他worker上存在的数据。当该属性为false时，Alluxio存储空间中数据将不会有任何额外副本。

值得注意的是，当开启该属性时，相同的数据块可以在多个workers处访问到，这样的多副本存储会降低Alluxio空间的总存储容量。根据数据集的大小与Alluxio的存储容量，建议关闭被动缓存(即设置该参数为：alluxio.user.file.passive.cache.enabled=false)，对于不需要数据本地性但希望更大的Alluxio存储容量的工作负载是有益的。未来， Alluxio 2.0将会支持更细粒度的数据副本设置，例如灵活配置Alluxio中给定文件的副本数最小值和较大值。

> 参考链接：

refer1：https://www.alluxio.org/docs/1.8/en/Architecture-DataFlow.html#local-cache-hit
refer2：http://www.alluxio.org/docs/1.8/en/operation/Metrics-System.html
refer3：[https://Linux.die.net/man/1/dstat](https://linux.die.net/man/1/dstat)
refer4：https://hadoop.apache.org/docs/r2.9.1/hadoop-yarn/hadoop-yarn-site/NodeLabel. html
refer5：http://www.alluxio.org/docs/1.8/en/compute/Spark.html#advanced-setup
refer6：https://issues.apache.org/jira/browse/SPARK-10149
refer7：https://www.alluxio.com/blog/asynchronous-caching-in-alluxio-high-performance-for-partial-read-caching
refer8：https://www.alluxio.org/docs/1.8/en/advanced/Alluxio-Storage-Management.html# single-tier-storage