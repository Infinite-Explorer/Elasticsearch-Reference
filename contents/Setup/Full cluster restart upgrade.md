### 全集群重启更新

在跨主要版本（例如：从0.x到1.x 1.x到2.x）升级的时候ES需要全集群重启。滚动升级不支持跨主版本升级。

执行全集群重启更新的过程如下：

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

如果是从0.90.x升级到1.x，使用下面的设置：

> **<pre>
PUT /_cluster/settings
{
    "persistent": {
      "cluster.routing.allocation.disable_allocation": true,
      "cluster.routing.allocation.enable": "none"
  }
}
> </pre>**

### 步骤2：执行同步刷新

如果你停止索引操作并发出一个[同步刷新](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/indices-synced-flush.html)的请求，分片恢复会更快：

> **POST /_flush/synced**

同步刷新请求是"最有效"的操作。如果有任一处理中的索引操作会导致同步刷新请求失败，但如果有必要的话，多次重新发送请求也是安全的。

### 步骤3：关闭并升级所有节点

停掉集群中的所有节点上的ES服务。每个节点可以按照在[步骤3：停止单个节点并升级](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html#upgrade-node)中描述的过程来升级。

### 步骤4：启动集群

如果你已经设置了专门的主节点—`node.master`设为`true`（默认），`node.data`设为`false`的节点—最好先启动它们。在继续处理数据节点之前，等待它们形成一个集群，并选出一个主节点。你可以通过日志查看这一进程。

一旦最小数量的具有成为主节点资格的节点互相发现，它们就会组成一个集群并选举出一个主节点。从那时起就可以使用[_cat/health](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-health.html)和[_cat/nodes](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-nodes.html)接口监控节点加入集群的情况：

> <pre>
GET _cat/health
GET _cat/nodes
> </pre>

使用这些接口去检查所有已经成功加入到集群的节点。

步骤5：等待集群监控状态变为yellow

一旦每个节点加入到集群，就会开始恢复所有本地存储的主分片。开始时，请求[_cat/health](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-health.html)接口会返回`status`为`red`，这意味着不是所有的主分片已被分配。

一旦每个节点已经恢复其本地（主）分片，`status`会变成`yellow`,这意味着所有的主分片都已恢复，但不是所有的副本分片都已分配。这也是意料之中的，因为分片分配还是在被禁用中。

步骤6：重新启动分片分配

推迟副本分片的分配直到所有的节点加入集群可以让主节点分配副本分片到已经有本地（主）分片拷贝的节点。这个时候，集群中已经有了所有的节点，重新启动分片分配是安全的：

> **<pre>
PUT /_cluster/settings
{
    "persistent": {
      "cluster.routing.allocation.enable": "all"
    }
}
> </pre>**


如果是从0.90.x升级到1.x，使用下面的设置：

>  **<pre>
PUT /_cluster/settings
{
    "persistent": {
      "cluster.routing.allocation.disable_allocation": false,
      "cluster.routing.allocation.enable": "all"
    }
}
>  </pre>**

现在集群会开始分配副本分片到所有的数据节点。这个时候恢复索引和搜索是安全的，只是如果你可以推迟点索引和搜索直到所有的分片恢复，这样你的集群会恢复的更快。

你可以使用[_cat/health](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-health.html)和[_cat/nodes](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-nodes.html)接口监控进度：

> <pre>
GET _cat/health
GET _cat/recovery
> </pre>

一旦`_cat/health`接口返回的`status`列变成`green`,说明所有的主分片和副分片都已经成功分配。

