## 安装

ES需要java7+的支持。尤其在写本文时，强烈推荐使用Oracle JDK 1.8.0_73。在不同平台之间的安装方式各不相同，所以我们不再赘述。Oracle推荐的安装文档能够在[Oracle网站](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)找到。我只想说，在安装ES之前，先通过运行检查java的版本（如果有必要的话再进行安装和升级）：

>  **java -version**

>  **echo $JAVA_HOME**

一旦安装了java之后，我们就可以下载运行ES了。在[www.elastic.co/downloads](http://www.elastic.co/downloads)可以找到之前所有版本的二进制文件。每一个发布的版本，都可以找到`zip`和`tar`压缩包，或者`DEB`和`RPM`包。为了方便，我们使用tar文件。

向下面这样下载ES 2.3.4（Windows用户可以下载ZIP包）

> **curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.4/elasticsearch-2.3.4.tar.gz**

然后解压（Windows用户可以使用unzip解压ZIP包）

> **tar -xvf elasticsearch-2.3.4.tar.gz**

然后会在当前目录产生一大堆文件和目录。然后进入bin目录：

> **cd elasticsearch-2.3.4/bin**

现在我们准备启动我们的节点和单个集群（Windows用户运行elasticsearch.bat文件）：

> **./elasticsearch**

如果一切顺利，你应该会看见很多这样的信息：

> **<pre>
./elasticsearch
[2014-03-13 13:42:17,218][INFO ][node           ] [New Goblin] version[2.3.4], pid[2085], build[5c03844/2014-02-25T15:52:53Z]
[2014-03-13 13:42:17,219][INFO ][node           ] [New Goblin] initializing ...
[2014-03-13 13:42:17,223][INFO ][plugins        ] [New Goblin] loaded [], sites []
[2014-03-13 13:42:19,831][INFO ][node           ] [New Goblin] initialized
[2014-03-13 13:42:19,832][INFO ][node           ] [New Goblin] starting ...
[2014-03-13 13:42:19,958][INFO ][transport      ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9300]}, publish_address {inet[/192.168.8.112:9300]}
[2014-03-13 13:42:23,030][INFO ][cluster.service] [New Goblin] new_master [New Goblin][rWMtGj3dQouz2r6ZFL9v4g][mwubuntu1][inet[/192.168.8.112:9300]], reason: zen-disco-join (elected_as_master)
[2014-03-13 13:42:23,100][INFO ][discovery      ] [New Goblin] elasticsearch/rWMtGj3dQouz2r6ZFL9v4g
[2014-03-13 13:42:23,125][INFO ][http           ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9200]}, publish_address {inet[/192.168.8.112:9200]}
[2014-03-13 13:42:23,629][INFO ][gateway        ] [New Goblin] recovered [1] indices into cluster_state
[2014-03-13 13:42:23,630][INFO ][node           ] [New Goblin] started
> </pre>**

没有过多深入细节，我们可以发现名为“New Goblin”（在你那里会是不同的漫威角色的名称）的节点已经启动了，并且在单集群中选举自己作为master节点。现在我们不用关心master什么意思。重要的是我们已经在一个集群启动了一个节点。

正如前面所说，我们可以修改集群和节点的名称。可以在命令行启动ES的时候完成：

> **./elasticsearch --cluster.name my_cluster_name --node.name my_node_name**

也要注意注意带有我们节点访问的HTTP地址(`192.168.8.112`)和端口(`9200`)的http行。默认的，ES提供`9200`端口访问其REST API。这个端口可以在有必要时进行修改。
