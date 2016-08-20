### 2.3的重大变更

这部分会讨论你应用所用的ES迁移到2.3版本时所要知道的一些变更。

### REST API

CORS实现的改变意味着CORS在2.3.0和2.3.1中不支持预检发送`OPTIONS`请求。这在2.3.2中得到了修复。

### 映射

`nested`字段的数量限制

索引一个含100个内嵌字段（文档）的文档，事实上索引了101个文档，这是因为每个内嵌文档都被作为一个独立的文档进行索引。为了防止错误定义索引，每个索引能定义的嵌套字段（文档）数量被限制到50。该默认限制数量可以通过索引设置`index.mapping.nested_fields.limit`进行修改。注意该限制只有在创建新索引或者更新映射的时候才会检测。因此，如果映射被修改，只会影响在2.3版本之前已经存在的索引。

### 脚本

##### Groovy依赖

在之前的ES版本里，Groovy脚本的能力取决于`org.codehaus.groovy:groovy-all`工件。除了引入Groovy语言，也引入了大量的功能特性，这对于ES中的脚本功能来说完全没有必要。除了在管理如此多依赖的固有困难外，这也增加了更多的安全问题可能性。该依赖已经被减少到只剩Groovy语言的核心`org.codehaus.groovy:groovy`工件。

### 分析（分词）API 变更

弃用的`filters`/`token_filters`/`char_filters`参数已被重命名为`filter`/`token_filter`/`char_filter`。`filters`/`token_filters`/`char_filters`已在5.0.0版本中移除。



