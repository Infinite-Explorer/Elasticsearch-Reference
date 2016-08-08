## 修改数据

ES提供了准实时的操作和查询数据能力。默认的，你可以预料到从你索引/更新/删除数据到出现搜索结果为止有1秒的延迟（刷新间隔）。这和其他的平台有很大的不同，例如SQL中的数据可以在事物完成之后立即可用。

### 索引/替换文档

我们前面已经索引了一个文档。我们再次调用那个命令：

> **<pre>
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
> </pre>**

再一次的，上面的命令会将指定的文档索引进customer索引中的external类型中去，ID为1。如果我们使用不同（或者相同）的文档再次执行上面的命令，ES会替换（即 重索引）现有ID为1的文档。

> **<pre>
curl -XPUT 'localhost:9200/customer/external/2?pretty' -d '
{
  "name": "Jane Doe"
}'
> </pre>**

上面的命令索引了一个ID为2的新文档。
在索引文档的时候，ID是可选的。如果没有指定，ES会产生一个随机ID并用来索引文档。现在ES产生的ID（或者我们前面例子明确指定的ID），会作为索引API调用的返回内容的一部分。

下面这个例子展示了如何在不指定ID时进行索引文档：

> **<pre>
curl -XPOST 'localhost:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
> </pre>**

注意我们在上面的例子中，由于我们没有指定ID，所以使用了POST动词而不是PUT动词。
