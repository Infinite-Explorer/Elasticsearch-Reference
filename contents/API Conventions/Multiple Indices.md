### 多索引

大多数API都能够利用`index`参数以支持跨多个索引执行，使用简单的`test1,test2,test3`的写法（或者使用`_all`来表示所有的索引）。也支持通配符，例如：`test*`,也可以"添加"(`+`)和"移除"(`-`)，例如：`+test*,-test3`。

所有的多索引API都支持下面的这些url查询字符串参数：

`ignore_unavailable`
	
　　控制在指定的索引不可用的时候是否忽略掉，这些索引包含不存在的和已关闭的索引。该参数可以设为`true`或者`false`。

`allow_no_indices`

　　控制在通配符索引表达式没能匹配到索引的时候是否返回失败。该参数可设为`true`或者`false`。例如指定通配符表达式`foo*`，没有以`foo`开始的索引，那么利用该设置，请求就会失败。该设置在使用`_all`,`*`或者不指定索引的时候都可以使用。该设置也适用于别名，在别名指向关闭的索引情况下也能使用。

`expand_wildcards`
　　控制通配符索引表达式扩展到的具体索引的类型。如果指定`open`，那么通配符表达式就被扩展为只能是打开的索引。如果指定`closed`，那么通配符表达式被扩展为只能是关闭的索引。两个值也能够同时指定，以扩展到全索引。

如果指定`none`，那么通配符表达式会被禁用。如果指定`all`，通配符表达式会扩展到所有索引（这跟指定`open,closed`效果一样）。

上面参数的默认设置取决于使用的API类型。

### 注意

单索引API，例如[ 文档API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html)和[单索引别名API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)都不支持多索引。
