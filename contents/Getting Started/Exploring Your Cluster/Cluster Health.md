## 集群健康

我们以一个基本的健康检查开始，可以知道集群工作的怎么样。我们会使用curl，当然你可以使用任何能发出HTTP/REST请求的工具。假设我们仍在之前启动的ES上的同一节点，并打开一个新的shell窗口。

为了检查集群健康，我们会用到[_cat API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)回想一下，我们节点的HTTP端点的可用端口是`9200`：

> **curl 'localhost:9200/_cat/health?v'**

响应：

> **<pre>
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign
1394735289 14:28:09  elasticsearch green           1         1      0   0    0    0        0
> </pre>**

我们可以看到elasticsearch集群是绿色状态。

无论我们什么时候请求集群的健康状态，我们都可以获得绿色，黄色，红色中的一种。绿色意味着情况很好（集群功能完好），黄色意味着所有的数据都可用但是部分副本数据还未被分配（集群功能完好），红色表示部分数据由于某种原因不可用。记住即使集群出于红色状态，仍然部分功能可用（例如 能够继续使用可用分片提供搜索服务），但因为丢失了数据，所以你可能需要尽快修复。

我们再看看上面的响应，能够看到总共1个节点，0个分片，因为我们至今还未存入数据。注意由于我们使用了默认的集群名称（elasticsearch），并且ES默认使用单播去发现同一机器的其他节点，这就有可能导致你偶然建立不止一个节点在电脑上，而且处在同一个集群中。这种情况下，你可能会找上门的响应中看到多个节点。我们也可以这样来获取集群中的集群：

> **curl 'localhost:9200/_cat/nodes?v'**

响应：

> **<pre>
curl 'localhost:9200/_cat/nodes?v'
host         ip        heap.percent ram.percent load node.role master name
mwubuntu1    127.0.1.1            8           4 0.00 d         *      New Goblin
></pre>**






