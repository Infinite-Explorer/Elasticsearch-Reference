## 配置

### 环境变量

在启动脚本里面，JVM启动的时候会传入ES内置的`JAVA_OPTS`变量。其中最重要的设置就是控制进程最大内存占用量的`-Xmx`，控制进程最小内存占用量（一般来说，进程被分配的内存越多越好）的`-Xms`。

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

### 内存设置

大多数操作系统都会试着将尽可能多的内存作为文件系统缓存并且积极地交换出未使用的应用内存，这可能会导致ES进程被交换。交换对于性能和节点稳定性来说是非常不好的，因此应该不惜一切代价避免它。

避免交换有三点：

* 禁用交换

    这是安全禁用交换的最简单选项。通常ES都是在一个盒子中运行的唯一服务，并且其内存使用是被环境变量`ES_HEAP_SIZE`所控制的。没有必要开启交换。

    在Linux系统中，你可以执行`sudo swapoff -a`临时禁用交换。如果想永久禁用的话，你需要在文件`/etc/fstab`中注释掉任何包含`swap`的行。

    在Windows系统中，可以通过完全禁用分页文件达到同样的效果，操作顺序为:`System Properties → Advanced → Performance → Advanced → Virtual memory`。

* 配置 `swappiness`

    第二个选项是确保 sysctl 中的`vm.swappiness`值设为`0`。这减少了内核交换的倾向并且不会导致在正常情况下发生交换，同时仍然运行整个系统在紧急情况下进行交换。

    ### 注意
    
    从内核版本3.5-rc1起，`swappiness`值设为`0`会导致 OOM killer 杀掉进程，而不是允许交换。你需要设置`swappiness`为1使在紧急情况下依然能够允许交换。

* `mlockall`

第三个选项是在Linux/Unix系统上使用[mlockall](http://opengroup.org/onlinepubs/007908799/xsh/mlockall.html)，或在Windows系统上使用[VitualLock](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366895%28v=vs.85%29.aspx)，试图将进程地址空间锁进RAM，防止哪怕一点的ES内存被交换出去。可以通过在`config/elasticsearch.yml`文件中添加下面这一行来达到目的：

> **bootstrap.mlockall: true**

在启动ES之后，你可以通过查看下面请求返回信息中`mlocalall`的值来判断设置是否生效：

> **curl http://localhost:9200/_nodes/process?pretty**

如果你发现`mlockall`值是`false`，这意味着`mlockall`请求失败了。在Linux/Unix系统上最可能的原因是运行ES的用户没有权限去锁内存。这可以在启动ES之前通过`roo`用户运行`ulimit -l unlimited`命令来授权解决。
    
另一个`mlockall`失败可能的原因是临时目录（通常为`/tmp`）挂载时使用了`noexec`选项。这可以在ES启动时指定一个新的临时目录来解决：

> **./bin/elasticsearch -Djna.tmpdir=/path/to/new/dir**

### 警告

如果试图去分配超过可用内存的内存量，`mlockall`可能会导致JVM或者shell session退出。

### ES 设置

ES的配置文件可以在`ES_HOME/config`目录下找到。目录中带有2个文件，`elasticsearch.yml`用来配置ES的不同[模块](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules.html)，`logging.yml`用来配置ES日志相关的东西。

配置内容的格式是[YAML](http://www.yaml.org/)。这里有一个修改所有基于网络的模块都会绑定和发布的地址的例子：

> **<pre>
network :
    host : 10.0.0.4
> </pre>**

#### 路径

在生产环境中，你几乎肯定会想去修改数据和日志文件的路径：

> **<pre>
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
> </pre>**

#### 集群名称

当然也不要忘记给你生产环境的集群取一个名称，它会被用于发现和自动加入其他节点：

> **<pre>
cluster:
  name: <NAME OF YOUR CLUSTER>
> </pre>**

请确保你不会在不同的环境使用相同的集群名称，否则你可能会导致节点加入到错误的集群中去。你可以使用`logging-dev`,`logging-stage`,`logging-prod`分别作为开发环境，测试环境，生产环境集群的名称。

#### 节点名称

你或许也想修改每个节点的默认名称为其他的什么东西，例如显示主机名什么的。默认的，在你启动节点的时候，ES会从一个大约有3000个漫威角色的列表中随机挑选一个作为节点名称。

> **<pre>
node:
  name: &lt;NAME OF YOUR NODE&gt;
> </pre>**

机器的主机名通过环境变量`HOSTNAME`获得。如果在你的机器上只运行了一个ES作为集群的节点，你可以使用`${...}`符号将节点名称设置为主机名：

> **<pre>
node:
  name: ${HOSTNAME}
> </pre>**

#### 配置风格

在配置文件内部，所有的设置都被转换成"命名空间式"的设置。例如，上面的设置被转换为`node.name`。这意味着很容易支持其他的配置格式，例如，[JSON](http://www.json.org/)。如果JSON是首选配置格式，只需重命名`elasticsearch.yml`文件为`elasticsearch.json`并添加如下内容：

> **<pre>
{
    "network" : {
        "host" : "10.0.0.4"
    }
}
> </pre>**

也意味着使用`ES_JAVA_OPTS`或者作为参数传给`elasticsearch`命令从外部提供设置是非常简单的，例如：

> **$ elasticsearch -Des.network.host=10.0.0.4**

另一个选项是使用`es.default`前缀替换'es'前缀，这表示在默认设置会被使用除非在配置文件中明确设置过。

另一个选项是在配置文件中使用`${...}`符号，它会被解析成一个环境设置，例如：

> **<pre>
{
    "network" : {
        "host" : "${ES_NET_HOST}"
    }
}
> </pre>**

此外，对于那些你不想存储在配置文件中的设置，你可以使用`${prompt.text}`或者`${prompt.secret}`并且在前台启动ES。`${prompt.secret}`禁用了输出，因此在你的终端不能看到输入的值；`${prompt.text}`允许你看到输入的值。例如：

> **<pre>
node:
  name: ${prompt.text}
> </pre>**

在`elasticsearch`命令执行时，你会被提示输入实际的值像这样：

> **Enter value for [node.name]:**

#### 注意

当ES进程作为服务运行或者在后台运行的时候，在设置中使用${prompt.text}`或者`${prompt.secret}`都会导致ES不能启动。

### 索引设置

在集群中创建的索引可以提供它们自己的设置。例如，下面创建了一个刷新间隔为5秒而不是默认的刷新间隔的索引（格式可以为YAML或者JSON）:

> **<pre>
$ curl -XPUT http://localhost:9200/kimchy/ -d \
'
index:
    refresh_interval: 5s
'
> </pre>**

在`elasticsearch.yml`中，索引级别的设置也可以设置在节点级别之上，像下面这样：

> **<pre>
index :
    refresh_interval: 5s
> </pre>**

这意味着在特定的使用上面提到的配置的节点中创建的索引会使用5秒的刷新间隔，除非索引明确的设置刷新间隔。换句话说，任何索引级别的设置都会覆盖节点中的设置。当然，上面的设置也可以写成"折叠"形式的设置，例如：

> **elasticsearch -Des.index.refresh_interval=5s**

所有的索引级别配置可以在每个[索引模块](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html)找到。

### 日志记录

ES利用log4j来抽离和开放内部日志。它试图通过使用[YAML](http://www.yaml.org/)来配置以简化log4j的配置，日志配置文件是`config/logging.yml`。JSON和属性格式也都是支持的。同时可以加载多个配置文件，只要都是以`.logging`作为前缀并且后缀是被支持的某一个（`.yml`,`.yaml`,`.json`，`.properties`）。logger区域包含了java包及其相应的日志级别，java包可能省略了`org.elasticsearch`前缀。appender区域包含了日志的目的地。关于如何让自定义日志和所有被支持的附加的大量信息都能在[log4j文档](http://logging.apache.org/log4j/1.2/manual.html)中找到。

[log4j-extras](http://logging.apache.org/log4j/extras/)提供的其他附加器和日志类也都是可用和开放的。

### 弃用的日志记录

除了常规的日志记录，ES 运行你启用一些被弃用的行为的日志记录。例如，这让你你能早点决定在将来是否需要迁移某些功能。默认的，弃用的日志记录时被禁用的。你可以在`config/logging.yml`文件中设置弃用日志级别为`DEBUG`来启用。

> **deprecation: DEBUG, deprecation_log_file**

这会在你的日志目录中产生一个每天滚动的弃用日志文件。经常查看该文件，尤其在你想要升级到一个新的主要版本的时候。