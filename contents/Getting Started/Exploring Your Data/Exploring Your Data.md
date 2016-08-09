## 探索数据

### 样例数据

我们已经了解了一些基本的东西，现在我们试着在更真实的数据上操作。我准备了一份虚拟的用户用户账号信息的JSON文档。每个文档都是如下结构：

> **<pre>
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
> </pre>**

出于好奇，我在[www.json-generator.com/](http://www.json-generator.com/)上面生成了这些数据，由于都是随机生成的，所以请忽略这些数据实际的值和语义。

### 导入样例数据

你可以从[这里](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true)下载样例数据。解压到当前目录并导入到我们的集群中：

> **<pre>
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
> </pre>**

返回内容：

> **<pre>
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
> </pre>**

返回内容表明我们刚刚已经成功索引了1000个文档到bank索引中（account类型下面）。