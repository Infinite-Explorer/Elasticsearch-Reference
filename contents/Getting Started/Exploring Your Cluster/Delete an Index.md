## 删除索引

我们删除刚刚创建的索引，并再次列出所有索引：

> **<pre>
curl -XDELETE 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
> </pre>**

返回内容：

> **<pre>
curl -XDELETE 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
> </pre>**

返回内容表明customer索引已经被成功删除，我们回到了集群中一无所有的时候。

在我们继续之前，让我们再静距离了解下目前为止学的一些API命令：

> **<pre>
curl -XPUT 'localhost:9200/customer'
curl -XPUT 'localhost:9200/customer/external/1' -d '
{
  "name": "John Doe"
}'
curl 'localhost:9200/customer/external/1'
curl -XDELETE 'localhost:9200/customer'
> </pre>**

如果我们仔细研究上面的命令，我们的确能看到在ES中访问数据的模式。模式总结如下：

> **curl -X&lt;REST Verb&gt; &lt;Node&gt;:&lt;Port&gt;/&lt;Index&gt;/&lt;Type&gt;/&lt;ID&gt;**

REST 访问模式对所有的API命令是普遍适用的，你只需记住就行，这对于掌握ES会是一个好的开始。

