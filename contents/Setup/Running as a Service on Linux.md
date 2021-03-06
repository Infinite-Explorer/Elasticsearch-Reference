### 在Linux上以服务运行

为了让ES在你的操作系统上以服务运行，我们提供的包试着使在重启和升级的时候开启和停止ES尽可能简单。

### Linux

现在我们已经创建了 debian 包和 RPM 包，在下载页面有提供下载。这些包自身没有任何依赖，只是你必须确保已经安装了 JDK。

每个包都放置了一个配置文件，运行你去设置下面的这些参数


`ES_USER` 
 
 运行的用户，默认为`elasticsearch`

`ES_GROUP` 

运行的组，默认为`elasticsearch`

`ES_HEAP_SIZE` 

启动时分配的堆大小

`ES_HEAP_NEWSIZE` 

新产生堆的大小

`ES_DIRECT_SIZE`

直接内存的最大值

`MAX_OPEN_FILES`

打开文件的最大数量，默认为 `65535`

`MAX_LOCKED_MEMORY`

最大锁住能存大小。如果在elasticsearch.yml中使用 boostrap.mlockall 选项的话，就设为"unlimited"。你也必须设置ES_HEAP_SIZE参数。

`MAX_MAP_COUNT`

一个进程能够拥有的映射区域的最大数量。如果你使用`mmapfs`作为索引存储类型，确保该参数设为比较高的值。想了解更多，请查看关于`max_map_count`的linux内核文档。该参数在启动ES前通过`sysctl`设置。默认为`65535`。

`LOG_DIR`

日志目录，默认为 `/var/log.txt/elasticsearch`

`DATA_DIR`

数据目录，默认为`/var/lib/elasticsearch`

`conf_dir`

配置文件目录（包含`elasticsearch.yml`和`logging.yml`文件），默认为`/etc/elasticsearch`

`ES_JAVA_OPTS`

任何你想使用的附加java选项。如果你需要设置`node.name`属性，但不想修改`elasticsearch.yml`文件，这或许会有用，因为它是通过供应系统，比如puppet或者chef进行分发的。例如：`ES_JAVA_OPTS="-Des.node..name=search-01"`

`RESTART_ON_UPGRADE`

配置为在包升级的时候重启，默认为`false`。这意味着你在手动安装包之后必须重启ES实例。这样设置的原因是确保集群的升级不会导致持续的分片分配，继而导致高网络消耗和集群响应变慢。

`ES_GC_LOG_FILE`

JVM创建垃圾回收日志文件的绝对路径。注意这个日志文件增长很快，因此默认禁用该参数。

#### 基于RPM发行

##### 使用 chkconfig

一些基于RPM发行的包是使用`chkconfig`去禁用和启用服务。其初始化脚本位于`/etc/ini.d/elasticsearch`，配置文件放置在`/etc/sysconfig/elasticsearch`。和debian包（安装）一样，RPM包默认不会在安装之后启动，如果你想启动的话，你必须手动执行下面的命令：

> **<pre>
sudo /sbin/chkconfig --add elasticsearch
sudo service elasticsearch start
> </pre>**

#### 使用 systemd

发行版本如Debian Jessie，Ubuntu 14，和许多 SUSE衍生版本都不使用`chkconfig`工具来注册服务，而是使用`systemd`及其命令`/bin/systemctl` 去启动和停止服务（至少是在比较新的版本中，不然还是使用上面的`chkconfig`命令）。如果系统是基于rpm安装的，配置文件也在`/etc/sysconfig/elasticsearch`。如果是基于deb安装的，配置文件就在`/etc/default/elasticsearch`。在安装RPM包之后，你必须修改systemd配置然后才能启动ES。

也要记住修改`/etc/sysconfig/elasticsearch`中的`MAX_MAP_COUNT`设置不会有任何效果，为了在启动时生效，你必须在`/usr/lib/sysctl.d/elasticsearch.conf`中修改它。




