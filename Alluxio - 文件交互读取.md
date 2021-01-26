# Alluxio 的文件交互读取详解

[TOC]

### 总流程

#### 时序图

![alluxio文件读取流程总时序图](https://img-blog.csdnimg.cn/20200322093646889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

#### 交互流程概览

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531085718429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)
如上图所示，alluxio文件系统的交互流程主要由三部分组成：client(发起请求)、master(响应client和worker的请求)、worker（提供数据的存储与访问）。

### Client - Master

#### 1.client通过rpc连接master，获取文件path对应的元数据信息status

```
Client端，负责发送请求是 RetryHandlingFileSystemMasterClient
Master端，负责接收请求并处理是FileSystemMasterClientServiceHandler
client 和 master的rpc连接由Thrift框架实现，以上两个类都是Thrift定制生成。
123
```

#### 2.基于status、path、options，创建文件输入流对象FileInstream

```
属性说明：
stauts 从上面master返回(再次通过rpc与master进行交互)；
options 从命令行提交的请求，默认为null，使用 default options来填充。
context 所有filesystem对象共享的上下文，此流程主要获取client端 和 master 、worker 的客户端对象。
blockStore 文件的各种块操作的管理对象。特别注意，其初始化时，写入了当前Client所在节点的层级化属性：
  默认值=>TierIdentity（node=cleint节点本地ip，rack=null），该属性在后续为block选取worker节点时使用。
123456
```

#### 3.将文件（FileInStream） 拆分为各个块（BlockInstream）来读取

```
补充：
    alluxio的文件读取会转化为文件的块读取；
    在alluxio中，block是 文件读取的最小基本单位, 通过BlockIntream实现文件读取。
    文件读取的传输方式，还需要根据命中情况，拆分成短路读和netty远程读2种方式。

byte[] buf = new byte[512];
try (FileInStream is = mFileSystem.openFile(path, options)) {//使用默认的options打开文件，详见 openFile
int read = is.read(buf);//读取 512 bytes， 详见read方法内部
while (read != -1) {
  System.out.write(buf, 0, read);
  read = is.read(buf);
}
123456789101112
```

源码说明：

```
a、从上面的 is.read(buf)看到，每次循环都是读取buf=512Byte 数据。
   这个读取过程中，每次都会记录/更新 当前读取到文件的哪个位置position，目的就是计算这部分的数据是属于文件的哪个blockId。blockId记录在上面提及的status.blockIds列表。
   每个blockId 都会创建一个block输入流对象BlockInStream 来进行后续的数据块的数据读取。
   每读完一个block，则会创建一个新的blockInstream去读取下一个block。
b、block输入流对象blockInstream 封装了 block的基本信息BlockInfo，每个block的读取行为（即 与worker节点的选定直接相关），以及读取数据时的通讯信息。
12345
```

下面是每个block的读取流程描述：

##### 3.1 构建blockInStream：为block选定worker节点，根据读命中类型，创建对应数据传输方式

通过position确定要status.blockIds列表的下标，从而取出下一个要读取的blockId。

通过块管理对象BlockStore，创建一个block输入流对象BlockInStream， 并设置流对象开始读取文件的位置。

具体创建 BlockInStream的过程，实际就是为block选定worker节点，并根据命中类型创建对应的数据传输方式的过程。

###### 1）client通过rpc连接master，获取blockId对应的blockInfo信息：

```
获取到的locations 就是block所在的worker列表。 目前block 仅保存在一个worker上。 10.37.129.2
从locations 可以 提取出worker的网络位置信息：workerNetAdderss；
workerNetAdderss 包含了worker的层级化属性：node=ip，rack=null， 可用于判断各种命中类型的worker节点选取。
123
```

###### 2）blockInfo 选取最优worker节点

各个节点都有层级化属性 —— 层级化属性可以从脚本/configuration获取，默认都不设置时，仅获取进程所在的ip来填充，即

TierIdentity：node=主机Ip，rack=null

所以有如下三个节点的位置信息：

client节点的层级化属性： TierIdentity：node=本地主机Ip，rack=null
blockInfo的层级化属性： 当前要读取的block所在的worker列表，封装在blockInfo.localtions里面。

下文是block的woker节点选取路线：
**情况一：当数据存在于alluxio本地资源（MEM，ssd，hhd）时，即blockInfo.localtions列表非空。**

```
 将client的TierIdentity 和 blockInfo.localtions列表的各个worker的TierIdentity进行比较。
 当且仅当client和 待选worker的node相同（即ip相同）时，该worker节点才是本地命中worker节点，标记为“LOCAL”。 （注：rack=null 不参与最优worker节点评选）
 如果找不到“本地命中worker节点”时，返回blockInfo.localtions列表的第一个节点作为最优worker节点，此时标注为“REMOTE”（远程命中节点）
123
```

**情况二：当数据不存在于alluxio时，先为block选定一个最优worker节点， 后续会通过这个worker从ufs读取数据**

```
 block不在alluxio的任意一个worker，首先需要通过rpc连接master，获取当前所有worker节点的列表。
 然后通过LocalFirstPolicy 策略，从所有worker列表中选出一个最优worker。
 LocalFirstPolicy 一个优先返回本地主机的定位策略，如果本地worker没有足够的容量，那么就会从活跃有效的workers列表随机选择一个worker用于每个块的写入。
     关键成员变量mLocalHostName，即本地主机名，它是用于选择本地本机worker的关键变量。
     核心方法getWorkerForNextBlock()， 为下一个block选取一个worker，返回Worker的网络地址WorkerNetAddress。
     具体逻辑，分为两大步骤，如下：
    （1）首先尝试从本地主机中选择：
       先遍历worker列表，获取每个workerInfo：【回应一下，这里的worker列表，指的是上面master返回的所有worker节点列表】
       然后判断workerInfo所在主机名是否与mLocalHostName，且workerInfo的总字节数是否满足当前块所需要的大小blockSizeBytes，
       如果满足条件则返回，否则继续遍历；
    （2）如果步骤1始终未选择到满足条件的worker，则随机挑选一个容量足够满足要求的worker：
        先对workerInfoList进行shuffle，避免热点问题；
        然后遍历worker列表，获取每个BlockWorkerInfo，即workerInfo：
       只要workerInfo的总字节数是否满足块需要的大小blockSizeBytes，就是我们需要的worker，否则继续遍历；
    （3）连步骤2都不满足，即所有worker的容量都足以保存这个块，直接返回null。
 补充，从UFS读取数据，这种命中类型标记为“UFS”
12345678910111213141516
```

**情况三：上面两种情况都无法找到block对应可用的worker节点，则抛异常。**

###### 3）基于不同命中类型的worker节点，创建不同的数据传输流BlockInStream

从上面的步骤，我们得到一个BlockInStream的基本信息：

```
BlockInStream.create(mContext, info, dataSource, dataSourceType, options);
参数说明：
mContext = FileSystemContext , 用来获取各种client对象
info = blockInfo, 当前块的信息
dataSource = 当前块选定的worker的网络地址，即命中的worker。
dataSourceType = 数据来源（也是命中类型）；
例如 LOCAL，REMOTE，UFS，当仅当是从node选出的最优worker节点才满足 本地命中的要求，读取数据时走“短路读”。其他都是远程命中，或不命中。
options = 操作的选项
12345678
```

针对不同的命中类型，我们创建不同传输方式的BlockInStream，接下来就是client与worker之间不同的交互方式。

### Client - Worker

#### 情况一：本地命中 - LocalFilePacketReader读取流程

##### client端

```
client识别到数据源所在worker与本地worker是同一台机器，则下面进入短路读的流程。
1
```

##### worker端

```
worker端对client的请求处理如下图所示：
1
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531085956312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)
1.对于短路读取的流程，如上图中的4a所示，请求经由LocalFilePacketReader发起，到达worker端之后，在Netty的通道中流经各个Handler，最后被ShortCircuitBlockReadHandler捕获(出入口由RPCMessageDecoder和RPCMessageEncoder担当)；
2.在ShortCircuitBlockReadHandler中进一步根据传入的blockId进行块的处理，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531090219647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)
→ BlockWorker会将目标块移动到MEM中的指定目录(移动之前会先判断是否有必要移动)；
→ 将处理后的目标块的路径返回给客户端；

#### 情况二：非本地命中的其他读取行为(Netty远程数据传输)

##### client端

注：client端在向worker请求数据前的一系列步骤如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531090329673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
1. client在向master端获取block所在的worker列表之前需要获取对应的blockId列表，过程如下图所示：
    1.1 client端通过RPC请求传入要访问的文件的路径path
    1.2 master端根据该path查找其对应的Inode信息，并生成对应文件的FileInfo，该FileInfo包含了BlockId列表、UfsPath、MountId、Path等信息；
123
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531090543717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
2. client端遍历获取到的目标文件的BlockId列表，再次通过RPC请求向master获取对应blockId的worker列表(客户端返回的blockInfo中有blockId、以及包含该blockId的worker地址列表)：
3. client端对比master返回的对应的blockId的worker列表，找出目标worker，查找策略在上面已经提及过：
    3.1 当数据存在于alluxio本地worker中时(MEM、SSD、HDD)，即blockInfo.locations列表非空；
    3.2 当数据不存在于alluxio本地worker中时，先为block选定一个最优worker节点，后续会通过这个worker从ufs中读取数据；
    3.3 上面两种情况都找不到block所对应的可用的worker节点，则抛出异常。
4. client端会判断选择出来的worker是否是本地worker进行相应的读取策略。
123456
```

##### worker端

```
注：worker端对client请求的处理如上图client - worker交互流程所示：
1.对于远端worker命中，client读取数据的流程如上图4b所示，请求经由NettyPacketReader发起，到达worker端之后，在Netty的通道中流经各个Handler，最后被BlockReadHandler捕获(出入口由RPCMessageDecoder和RPCMessageEncoder担当)；
2.在BlockReadHandler中进一步根据传入的blockId进行块的处理，如下图：
123
```

【此图待修改】

```
2a.1 BlockReadHandler会先对该块加读锁，如果该块在Alluxio(MEM、SSD、HDD)中存在，则根据blockId、lockId获取到的LocalFileBlockReader中包含了块的存储路径(如：/Volumes/ramdisk/alluxioworker/50331648000 → 前半部分是块所在的目录的路径；最后的50331648000则是blockId对应的块)，如下图：
1
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531090735826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
    2a.2 位于Alluxio中的数据会被写入ByteBuf中，最后数据被封装到RPCProtoMessage中，传输到client端;
    
    2b.1 要访问的目标块不在Alluxio中(MEM、SSD、HDD)，则根据blockId获取到的UnderFileSystemBlockReader过程中包含了在Alluxio中创建临时块、创建对临时块进行操作的BlockWriter、以及从ufs路径加载目标blockId的InputStream，如下图：
123
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531090810124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)
2b.2 对于UnderFileSystemBlockReader获取(acquire)目标块(blockId)对应的InputStream过程，Alluxio提供了缓存InputStream的概念：
即UfsInputStreamManager维护了一个映射表，资源号(blockId → fileId → resources)到CachedSeekableInputStream之间的一个映射，在配置允许缓存的前提下(alluxio.worker.ufs.instream.cache.enabled)，优先查找已经缓存的InputStream并返回；
→ CachedSeekableInputStream封装了FileInputStream（本地环境下由LocalUnderFileStream根据path生成并返回）
2b.3 位于ufs中的数据会被写入ByteBuf中，最后数据被封装到RPCProtoMessage中，传输到client端;

### Worker - Master

#### 非本地读取完成后的同步行为

```
注：文件读取完成后，客户端在关闭BlockInstram时，会根据本次读文件是否是本地读进行进一步的处理，如下图：

1.短路读取则直接关闭BlockInstream，并结束；

2.非短路读取则在关闭BlockInstream后，由客户端向worker(本地是worker则向本地worker请求，否则向文件读取的worker请求)发送无响应数据同步请求，请求到达worker端后，主要有AsyncCacheHandler中列出的五种情况，如下图：
12345
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531091017180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
3.以上五种情况可以分为两大类，处理数据源的worker与当前client请求同步的worker是否是同一个worker：
    3.1 是同一个worker，则该client请求同步的worker需要调用cacheBlockFromUfs进行同步，如下图所示：
        → worker会从调用readUfsBlock()方法获取UnderFileSystemBlockReader；
        → cacheBlockFromUfs中通过调用UnderFileSystemBlockReader的read()方法,将ufs中的数据复制到byte []数组中;
        → 获取ufs数据之后，通过TieredBlockStore为目标block申请存储路径；
        → 通过LocalFileBlockWriter将由byte []数组转换成的Buffer写入临时块；(临时块的路径示例：/Volumes/ramdisk/alluxioworker/.tmp_blocks/708/5c0b3f1a9800d6c4-fa4000000)
123456
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020053109122523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
    3.2 不是同一worker，则该client请求同步的worker需要调用cacheBlockFromRemoteWorker进行同步，如下图所示：
        → 1. 通过BlockWorker先为目标blockId在指定层级创建临时块空间（包括临时块元数据信息）；
        → 2. 在创建RemoteBlockReader的时候，本地worker从远端数据源worker请求到数据连接通道(封装在RemoteBlockReader内)；
        → 3. 通过BlockWorker获取到的目标块的BlockWriter和已经建立连接的RemoteBlockReader，在BufferUtils.fastCopy(reader.channel, writer.channel)下从远端传输到临时块空间；
        → 4. 本地worker继续通过BlockWorker将临时块中的数据移动到真正的块中(完成后，临时块删除)；
12345
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531091504687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)

```
→ 5. 本地worker处理完目标块的所有的同步操作后，向Master端同步目标块的元数据信息，Master端在收到worker端提交块的元数据同步请求后，master端对元数据对一些处理如下：
1
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200307171403836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RvbmdmYW5nc2h1b3NodW8=,size_16,color_FFFFFF,t_70)