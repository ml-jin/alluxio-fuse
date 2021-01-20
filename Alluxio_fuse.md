## Alluxio - Fuse 部署操作指南

- 安装依赖

``` bash
yum install -y fuse-libs fuse
```

- 配置

``` bash
cat alluxio-2.x-x/conf/alluxio-site.properties
alluxio.master.hostname=x.x.x.x
echo user_allow_other >> /etc/fuse.conf
```

- 挂载Alluxio FUSE

```bash
./alluxio-2.x.x/integration/fuse/bin/alluxio-fuse mount -o <options> /mountpoint /path/in/alluxio
```

- 查看挂载点运行信息

``` bash
/opt/alluxio-2.3.0/integration/fuse/bin/alluxio-fuse stat
```

- 查看存储空间

``` bash
df -h -t fuse.alluxio-fuse
```

- 卸载Alluxio FUSE

``` bash
/opt/alluxio-2.3.0/integration/fuse/bin/alluxio-fuse umount mount_point
```

或者 ：

``` bash
umount /[fuse_mountpoint]
```

- 查看挂载参数

``` bash
mount -t fuse.alluxio-free
```

- 注意事项: 

  1. 要使用启动master和worker的用户来挂载fuse，比如使用hdfs用户启动的Alluxio，则要用hdfs来挂载，可以正常使用，如果使用root用户挂载，目录信息会乱码且无法正常使用。hdfs用户下成功mount后，切换到root用户也会看到挂载点信息乱码。Alluxio相关服务未启动，挂载点信息也会乱码。 Alluxio默认只能写本地worker，如果明确知道要写入的文件大小的范围，可以使用ASYNC_THROUGH并加大worker的缓存大小，或者配置多级缓存使worker的缓存空间大于写入文件的大小，才能防止被置换，从而提高效率 如果不确定写的文件大小的范围，就不要使用ASYNC_THROUGH这个参数，因为如果本地Worker缓存空间不够就会写入失败，这时，为了保险起见可以使用写参数CACHE_THROUGH边缓存边写或写参数THROUGH只写底层存储，来防止写入文件失败。 当然，还有一种比较好的方案，写参数设为ASYNC_THROUGH配合更大的Worker缓存来提高效率，同时设置alluxio.user.file.write.location.policy.class=alluxio.client.file.policy.RoundRobinPolicy参数来保证写入不会失败。如果只写一次，可以及时free掉无用的缓存，减少后面写数据时发生的缓存置换。    

  2. 当<alluxio_path> 未给定，则该值默认为根 （'/'）。请注意，"<mount_point>"必须是本地文件系统层次结构中的现有和空路径，并且运行"path/to/alluxio/bin/alluxio-fuse"脚本的用户必须拥有装载点并具有读取和写入权限。可以在同一节点中创建多个 Alluxio FUSE 安装点。所有"AlluxioFuse"进程在"$ALLUXIO_HOME_log=fuse.log"处共享相同的日志输出，这对于在文件系统下的操作中发生错误时进行故障排除非常有用。

- 引用

>  [Alluxio fuse 用例介绍](https://shmily-qjj.top/44511/#Alluxio-FUSE)