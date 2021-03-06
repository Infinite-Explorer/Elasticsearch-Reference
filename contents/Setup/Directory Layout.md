### 目录布局

安装的目录布局如下：

<table style="border-collapse:collapse;">
<tr>
<td>类型</td>
<td>描述</td>
<td>默认位置</td>
<td>设置</td>
</tr>
<tr>
<td>home</td>
<td>ES安装的根目录</td>
<td></td>
<td>path.home</td>
</tr>
<tr>
<td>bin</td>
<td>包括启动节点的elasticsearch在内的二进制脚本</td>
<td>{path.home}/bin</td>
<td></td>
</tr>
<tr>
<td>conf</td>
<td>包括elasticsearch.yml在内的配置文件</td>
<td>{path.home}/config</td>
<td>path.conf</td>
</tr>
<tr>
<td>data</td>
<td>每个索引或分片的数据文件在被分配的节点上的位置。可以有多个位置。</td>
<td>{path.home}/data</td>
<td>path.data</td>
</tr>
<tr>
<td>logs</td>
<td>日志文件位置</td>
<td>{path.home}/logs</td>
<td>path.logs</td>
</tr>
<tr>
<td>plugins</td>
<td>插件文件位置。每个插件会被包含在一个子目录中。</td>
<td>{path.home}/plugins</td>
<td>path.plugins</td>
</tr>
<tr>
<td>repo</td>
<td>共享文件系统仓库位置。可以有多个位置。一个文件系统仓库可以被放置到在这里指定的任意目录的任意子目录中。</td>
<td>未配置</td>
<td>path.repo</td>
</tr>
<tr>
<td>script</td>
<td>脚本文件位置</td>
<td>{path.conf}/scripts</td>
<td>path.scripts</td>
</tr>
</table>

多个`data`路径可以被指定，以分散数据到多个硬盘或位置，但是来自同一分片的文件会被写入到相同的路径。这可以像下面这样进行配置：

> **path.data: /mnt/first,/mnt/second**

或者使用数组格式：

> **path.data: ["/mnt/first", "/mnt/second"]**

###### 贴士

为了使分片在多个硬盘分散，请使用磁盘冗余阵列。

### 默认路径

下面的ES会使用的默认路径，如果没有修改的话。

#### deb 和 rpm

<table style="border-collapse:collapse;">
<tr>
<td>类型</td>
<td>描述</td>
<td>在Debian/Ubuntu中的位置</td>
<td>在RHEL/CentOS中的位置</td>
</tr>
<tr>
<td>home</td>
<td>ES安装的根目录</td>
<td>/usr/share/elasticsearch</td>
<td>/usr/share/elasticsearch</td>
</tr>
<tr>
<td>bin</td>
<td>包括启动节点的elasticsearch在内的二进制脚本</td>
<td>/usr/share/elasticsearch/bin/bin</td>
<td>/usr/share/elasticsearch/bin</td>
</tr>
<tr>
<td>conf</td>
<td>包括elasticsearch.yml在内的配置文件</td>
<td>/etc/elasticsearch/config</td>
<td>/etc/elasticsearch</td>
</tr>
<tr>
<td>conf</td>
<td>包括堆大小，文件描述符在内的环境变量</td>
<td>/etc/default/elasticsearch</td>
<td>/etc/sysconfig/elasticsearch</td>
</tr>
<tr>
<td>data</td>
<td>每个索引或分片的数据文件在被分配的节点上的位置。可以有多个位置。</td>
<td>/var/lib/elasticsearch/data/data</td>
<td>/var/lib/elasticsearch</td>
</tr>
<tr>
<td>logs</td>
<td>日志文件位置</td>
<td>/var/log/elasticsearch</td>
<td>/var/log/elasticsearch</td>
</tr>
<tr>
<td>plugins</td>
<td>插件文件位置。每个插件会被包含在一个子目录中。</td>
<td>/usr/share/elasticsearch/plugins</td>
<td>/usr/share/elasticsearch/plugins</td>
</tr>
<tr>
<td>repo</td>
<td>共享文件系统仓库位置。</td>
<td>未配置</td>
<td>未配置</td>
</tr>
<tr>
<td>script</td>
<td>脚本文件位置</td>
<td>/etc/elasticsearch/scripts/scripts</td>
<td>/etc/elasticsearch/scripts</td>
</tr>
</table>

#### zip 和 tar.gz

<table style="border-collapse:collapse;">
<tr>
<td>类型</td>
<td>描述</td>
<td>位置</td>
</tr>
<tr>
<td>home</td>
<td>ES安装的根目录</td>
<td>{extract.path}</td>
</tr>
<tr>
<td>bin</td>
<td>包括启动节点的elasticsearch在内的二进制脚本</td>
<td>{extract.path}/bin</td>
</tr>
<tr>
<td>conf</td>
<td>包括elasticsearch.yml在内的配置文件</td>
<td>{extract.path}/config</td>
</tr>
<tr>
<td>data</td>
<td>每个索引或分片的数据文件在被分配的节点上的位置。可以有多个位置。</td>
<td>{extract.path}/data</td>
</tr>
<tr>
<td>logs</td>
<td>日志文件位置</td>
<td>{extract.path}/logs</td>
</tr>
<tr>
<td>plugins</td>
<td>插件文件位置。每个插件会被包含在一个子目录中。</td>
<td>{extract.path}/plugins</td>
</tr>
<tr>
<td>repo</td>
<td>共享文件系统仓库位置。</td>
<td>未配置</td>
</tr>
<tr>
<td>script</td>
<td>脚本文件位置</td>
<td>{extract.path}/config/scripts</td>
</tr>
</table>


