## 删除文档

删除文档操作非常直接。下面的例子展示了如何删除我们之前创建的ID为2的custo文档。

> **<pre>
curl -XDELETE 'localhost:9200/customer/external/2?pretty'
> </pre>**

`delete-by-query` 插件可以将查询匹配到的文档全部删除。