## 配置

### 环境变量

在启动脚本里面，JVM启动的时候会传入ES内置的`JAVA_OPTS`变量。其中最重要的设置就是控制进程最大内存占用量的`-Xmx`，控制进程最小内存占用量（一般来说，进程被分配的内存越多越好）。

大部分时候最好不要去修改默认的`JAVA_OPTS`，使用`ES_JAVA_OPTS`去设置/修改JVM的设置或参数。

环境变量`ES_HEAP_SIZE`可以设置分配给ES java进程的堆内存大小。它会分配相同的最大和最小内存占有量，尽管这可以通过`ES_MIN_MEM`（默认为`256m`）和`ES_MAX_MEM`（默认为`1g`）来设置（不推荐）。

我们推荐将最大，最小的内存占有量设为相同值，并启用
[mlockall](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html#setup-configuration-memory)