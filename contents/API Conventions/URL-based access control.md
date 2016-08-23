### 基于URL的访问控制

许多用户都使用了基于URL的访问控制使访问ES索引变得安全保险的代理。对于[multi-search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-multi-search.html)，[multi-get](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-multi-get.html)和[bulk](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)请求，用户能够在URL和每个请求的请求体中指定索引。这让基于URL的访问控制压力很大。

为了防止用户覆盖在URL指定的索引，可以在`config.yml`文件中添加下面的设置：

> **rest.action.multi.allow_explicit_index: false**

默认值为`true`，但当设为`false`时，ES会拒绝在请求体中指定索引的请求。