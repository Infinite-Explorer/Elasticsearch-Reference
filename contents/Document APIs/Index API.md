### 索引API

索引API使用输入的JSON文档在指定的索引中添加或更新，以使该文档能够被搜索到。在下面的例子中，将JSON文档插入到了"twitter"索引，"tweet"类型下，并且id为1：

> **<pre>
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

上面所有操作的返回结果如下：

> **<pre>
{
    "_shards" : {
        "total" : 10,
        "failed" : 0,
        "successful" : 10
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true
}
> </pre>**

`_shards`头提供了索引操作的复制过程的信息。

* `total` - 表示了索引操作需要在多少分片拷贝上执行。
* `successful` - 表示索引操作执行成功的分片拷贝数量。
* `failed` - 包含在副本分片上索引操作失败时，复制相关错误的数组。

索引操作在`successful`字段值大于等于1时，即表示成功。

#### 注意

在索引操作成功返回（默认必须规定数量）的时候，可能并不是所有的副本分片都已经启动了的。在这种情况下，`total`字段值跟索引副本设置的总分片数一致，`successful`字段值跟启动的分片数（主分片加上副本分片）一致。由于没有失败发生，`failed`字段值为0。

### 自动索引创建

索引操作在索引还未创建之前（查看创建[索引API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html)）会自动创建，如果指定的类型还未创建映射（查看设置映射API，了解如何手动创建[类型映射](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html)）就会自动创建一个动态类型映射。

映射自身是很灵活和模式自由的。新的字段和对象会自动加入到指定类型的映射定义中去。查看映射部分以获得关于映射定义的更多信息。

自动索引创建可以通过在所有节点的配置文件中将`action.auto_crete_index`设为`false`来禁用掉。自动映射创建可以通过在所有节点的配置中将`index.mapper,dynamic`设置为`false`来禁用掉（或者是在指定的索引设置中进行设置）。

自动索引创建可以引入基于模式的黑白名单，例如，将`action.auto_create_index`设为`+aaa*,-bbb*,+ccc*,-*`（+表示运行，-表示禁止）。

### 版本控制

每一个被索引的文档都有一个版本号。相关联的`version`号作为索引API请求的响应内容的一部分返回。索引API在指定`version`参数的时候可允许乐观并发控制。这会控制将要执行操作的文文档的版本。一个关于版本控制好的用例是执行读取然后更新的事务操作。在开始读取文档的时候指定`version`可以确保同时不会出现变动（当为了之后的更新而读取的时候，建议将`perference`改为`_primary`）。例如：

> **<pre>
curl -XPUT 'localhost:9200/twitter/tweet/1?version=2' -d '{
    "message" : "elasticsearch now has versioning support, double cool!"
}'
> </pre>**

**注意**：版本控制是完全实时的，并且不会受到搜索操作准实时方面的影响。如果不提供版本，操作执行的时候不会进行版本检查。

默认的，内部的版本号从1开始计算，并且在每次的更新，删除操作的时候加1。版本号也可以从外部获取（例如,将版本号存储在数据库）。为了开启这个功能,`version_type`应该设为`external`。值应该为大于等于0，小于约9.2e+18的长整数。当使用外部的版本号时，系统不会检查是否匹配版本号，而是查看索引请求中的版本号是否大于当前文档中存储的版本号。如果大于，该文档就会被索引，并开始使用新的版本号。如果外部提供的版本号小于等于文档中存储的版本号，会出现版本冲突并且索引操作失败。

这样的一个好处是就算源数据库改变也不必维护异步索引操作执行的严格顺序，只要继续使用之前源数据库的版本号。如果使用外部版本号，即使使用数据库的数据更新ES索引的简单例子都会被简化，因为如果索引操作由于某种原因未能按照顺序的话，只有带最新的版本号的索引操作才能被执行。

#### 版本类型

除了上面解释的版本类型`internal`和`external`,对于具体使用情况ES也支持其他版本类型。下面是对于不同版本类型及其语义的概述。

`internal`

只有在提供的版本号跟已存储文档的版本号一样的时候才会索引文档。

`external`或者`external_gt`

只有在提供的版本号严格高于已存储文档的版本号或者不存在该文档时才会索引文档。提供的版本号会被用作新的版本号并被存储在新文档中。提供的版本号必须是非负长整型数字。

`external_gte`

只有在提供的版本号大于等于已存储文档的版本号时才会索引文档。如果不存在该文档，索引操作也会成功。提供的版本号会被用作新的版本号并被存储在新文档中。提供的版本号必须是非负长整型数字。

`force`

无论文档存在与否，文档都会被索引。即使存在也会在索引的时候忽略掉版本号的限制。提供的版本号会被用作新的版本号并被存储在新文档中。该版本类型常用来修改错误。

**注意**：`external_gte`和`force`版本类型都是在特殊情况下使用的，应该谨慎使用。如果使用不当，会造成数据丢失。

### 操作类型

索引操作也可以使用`op_type`参数去强制进行`create`操作，类似于"如果不存在就创建"。当使用`create`的时候，如果索引中已经存在该id的文档，那么索引操作就会失败。

下面是一个使用`op_type`参数的例子：

> **<pre>
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1?op_type=create' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

另一个使用`create`操作类型的方法是使用下面的url：

> **<pre>
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1/_create' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

### 自动生成ID

索引操作执行的时候可以不用指定id。在这种情况下，id会自动生成。此外，`op_type`会被自动设为`create`。下面有一个例子（注意使用的是**POST**而不是**PUT**）：

> **<pre>
$ curl -XPOST 'http://localhost:9200/twitter/tweet/' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

上面操作的结果如下：

> **<pre>
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "6a8ca01c-7896-48e9-81cc-9f70661fcb32",
    "_version" : 1,
    "created" : true
}
> </pre>

### 路由

默认的，分片分配 —— 或者说是`routing` ——是由文档ID的哈希值来控制的。为了更明确的控制，可以在每个操作中使用`routing`参数直接指定传给哈希函数，供路由器使用的值。例如：


> <pre>
$ curl -XPOST 'http://localhost:9200/twitter/tweet?routing=kimchy' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'

> </pre>

在上边的例子中，"tweet"文档基于提供的`routing`参数"kimchy"被路由到一个分片上去。

当建立了具体的映射，`_routing`字段可以可选地被用来指引索引操作从文档自身获取路由值。这确实也带来了额外的文档解析过程的（很小的）代价。如果定义了`_routing`映射并设为了`required`，如果没有提供或者获取到路由值，所有操作就会失败。

### 父文档与子文档

子文档可以在索引的时候指定其父文档。例如：

> **<pre>
$ curl -XPUT localhost:9200/blogs/blog_tag/1122?parent=1111 -d '{
    "tag" : "something"
}'
> </pre>**

在索引子文档的时候，路由值自动设为跟其父文档一致，除非使用了`routing`参数指明了路由值。

### 时间戳

#### 警告

###### 在版本2.0.0-beta2中已被遗弃。

`_timestamp`字段已被遗弃。取而代之，使用[`date`](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html)字段设置其值。

文档可以带着一个`timestamp`被索引。文档的`timestamp`值可以使用`timestamp`参数进行设置。例如：

> **<pre>
$ curl -XPUT localhost:9200/twitter/tweet/1?timestamp=2009-11-15T14%3A12%3A12 -d '{
    "user" : "kimchy",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

如果`timestamp`值没有从外部提供或者存在于`_source`参数中，`timestamp`会被自动设为文档被索引时的日期。更多信息请看关于 _timestamp 映射的页面。








