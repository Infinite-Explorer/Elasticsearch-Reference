### 备份你的数据!

总是在升级之前备份你的数据。这能让你在遇到突发事故时回滚。升级有时也包括对被ES用来访问索引文件的Lucene库进行升级，当索引文件被更新到能再新版本Lucene工作之后，之前的ES发行版本可能不能访问现在版本的Lucene。

#### 警告

#### 在升级前总是备份你的数据

你不能回滚到之前的版本，除非你有之前数据的备份。

### 备份1.0及之后的版本

要备份运行1.0及之后版本的系统，最简单的方式就是使用快照功能。完再说嘛请看[使用快照备份与恢复](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html)

### 备份0.90.x及之前的版本

备份运行版本0.90.x的系统：

#### 步骤1：禁用索引冲洗

这会防止在备份时索引被冲洗到磁盘：

> **<pre>
PUT /_all/_settings
{
    "index": {
      "translog.disable_flush": "true"
    }
}
> </pre>**

步骤2：禁用分配

这会防止在备份时集群在节点间移动数据：

> **<pre>
PUT /_cluster/settings
{
    "transient": {
      "cluster.routing.allocation.enable": "none"
  }
}
> </pre>**

步骤3：备份你的数据

在分配和索引冲洗都被禁用之后，使用你最爱的备份方法（tar,存储阵列快照，备份软件）开始ES的数据路径备份。

步骤4：重启分配和索引冲洗

当备份完成，数据不再从ES数据路径读取的时候，分配和索引冲洗必须被重启：

> **<pre>
PUT /_all/_settings
{
    "index": {
      "translog.disable_flush": "false"
    }
}
PUT /_cluster/settings
{
    "transient": {
      "cluster.routing.allocation.enable": "all"
    }
}
> </pre>**






