## 配置

### 环境变量

在启动脚本里面，JVM启动的时候会传入ES内置的`JAVA_OPTS`变量。其中最重要的设置就是控制进程最大内存占用量的`-Xmx`，控制进程最小内存占用量（一般来说，进程被分配的内存越多越好）。

大部分时候最好不要去修改默认的`JAVA_OPTS`，使用`ES_JAVA_OPTS`去设置/修改JVM的设置或参数。

环境变量`ES_HEAP_SIZE`可以设置分配给ES java进程的堆内存大小。它会分配相同的最大和最小内存占有量，尽管这可以通过`ES_MIN_MEM`（默认为`256m`）和`ES_MAX_MEM`（默认为`1g`）来设置（不推荐）。

我们推荐将最大，最小的内存占有量设为相同值，并启用
[mlockall](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html#setup-configuration-memory)。

### 系统设置

#### 文件描述符

确保在机器上增加可打开文件描述符数量（或者是用户使用ES能打开的文件描述符数量）。推荐设置为32K或者甚至设为64K。

为了测试可以打开多少文件描述符，启动JVM的时候传入`-Des.max-open-files`,并设为`true`。这会在ES java进程启动的时候打印出进程能够打开的文件描述符数量。

此外，你可以通过使用[Nodes Info](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-nodes-info.html) API获取`max_file_descriptors`:

> **curl localhost:9200/_nodes/stats/process?pretty**

#### 虚拟内存

ES默认使用mmapfs和niofs混合的方式存储索引。操作系统默认的mmap数量上限可能太少了，这可能会导致内存溢出。在Linux上，你可以`root`用户执行如下命令来增加上限：

> **sysctl -w vm.max_map_count=262144**

可以在`/etc/sysctl.conf`文件中更新`vm.max_map_count`来让设置的上限值永久有效。

### 注意

如果你是使用包（.deb,.rpm文件）安装的ES，这个设置会自动改变。可以通过执行`sysctl vm.max_map_count`来查看。


