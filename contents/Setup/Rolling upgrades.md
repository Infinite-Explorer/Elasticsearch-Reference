### 滚动升级

滚动升级能够使ES集群一次只升级一个节点，无需停机终端用户。不管超出升级所需时间多少，对于跑着多个版本ES的集群来说都是不支持的，因为分片不会从较近版本复制到较老版本。

参考这张表格去确认你ES的版本是否支持回滚升级。

为了执行回滚升级，你需要如下步骤：

### 步骤1：禁用分片分配

当你关闭一个节点的时候，分配进程会立即试图将该节点上的分片复制集群中的其他分片上去，这会造成大量的I/O浪费。这个问题可以通过在关闭节点之前禁用分配来避免：

> **<pre>
PUT /_cluster/settings
{
    "transient": {
      "cluster.routing.allocation.enable": "none"
    }
}
> </pre>**

### 步骤2：停止不必要的索引操作并且执行一次(索引)同步刷新(可选)

你可以在升级的时候愉快地继续索引数据。然而，如果你暂时停掉不必要的索引操作并发出一个[同步刷新](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/indices-synced-flush.html)的请求，分片恢复会更快：

> **POST /_flush/synced**

同步刷新请求是"最有效"的操作。如果有任一处理中的索引操作会导致同步刷新请求失败，但如果有必要的话，多次重新发送请求也是安全的。

### 步骤3：停止单个节点并升级

在开始升级之前，关闭集群中的一个节点。

#### 贴士

当使用zip或者tar包的时候，`config`,`data`,`logs`,`plugins`目录都是默认放在ES的家目录中。

把那些目录放到不同的地方是一个很好的想法，因为这样在升级ES的时候就不会把它们删掉。这些自定义的路径可以通过`path.conf`和`path.data`设置项进行[配置](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/setup-configuration.html#paths)。

Debian和RPM包根据不同的系统，将这些目录放到恰当的位置。

使用[Debian或者RPM](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/setup-repositories.html)包进行升级：

* 使用`rpm`或者`dpkg`去安装新的包。所有的文件都应被放在它们正确的位置，并且配置文件不能被覆盖。

使用zip或者压缩后的tar升级：

* 解压zip或者tar到新目录，确保你不会覆盖`config`或者`data`目录。
* 要么从之前安装的`config`目录中复制文件到新安装的`config`目录，要么在命令行中使用`--path.conf`选项去指向
一个内部的配置文件目录。
* 要么从之前安装的`data`目录中复制文件到新安装的`config`目录，要么在`config/elasticsearch.yml`文件中设置`path.data`来配置数据目录的位置。

### 步骤4：启动已升级的节点

启动已升级的节点，并通过检查日志文件或者检查请求输出内容来确认该节点是否加入到集群中了：

> GET _cat/nodes

### 步骤5：重启分片分配

一旦节点加入到集群，重启分片分配并开始使用节点：

> **<pre>
PUT /_cluster/settings
{
    "transient": {
      "cluster.routing.allocation.enable": "all"
    }
}
> </pre>**

### 步骤6：等待节点恢复

在升级下个节点之前，你需要等到集群完成分片分配。你可以通过请求`_/cat/health`查看进度：

> GET _cat/health

等待`status`列从`yellow`变为`green`。状态`green`意味着所有的主分片和副本分片都已经分配完毕。      

#### 重要

在滚动升级的时候，分配给较高版本节点的主分片永远都不会得到被分配到较低版本节点上的副本分片，因为较新版本有不同的数据格式，较老版本不能理解这种数据格式。

如果不能分配副本分片到较高版本节点上—例如，如果集群中只有一个较高版本节点—那么副本分片会一直保持未分配状态并且集群健康的状态一直为`yellow`。

在这种情况下，在我们继续之前先检查下是否还有处于初始化中和分配中状态的分片（`init`列和`relo`列）

一旦另一个节点完成升级，副本分片就应该被分配了，集群健康状态也会变成`green`。

未同步刷新的分片可能需要花些时间才能恢复。可以通过请求`_/cat/recovery`监控单个节点的恢复状态：

> GET _cat/recovery

如果你之前停止了索引，那么只要分片恢复一完成，重新启动索引就是安全的。










