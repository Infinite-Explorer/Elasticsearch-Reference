### 重大修改

这部分会讨论你应用所用的ES在不同版本间迁移时所要知道的一些变更。

按照惯例：

* 主版本间的迁移 — 例如从`1.x`到`2.x` — 需要[全集群重启](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/restart-upgrade.html)。
* 次版本间的迁移 — 例如从`1.x`到`1.y` — 可以通过[一次只更新一个节点](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/rolling-upgrades.html)来进行。

查看[升级](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/setup-upgrade.html)获取更多信息。
