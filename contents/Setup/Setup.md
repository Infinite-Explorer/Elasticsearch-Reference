## 安装

这一章节包含了如何安装和运行ES的相关信息。如果如果你还未准备充足，[下载](http://www.elastic.co/downloads)并查看[安装](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html#setup-installation)文档。

### 注意

ES也能够通过使用`apt`或者`yum`从我们的仓库安装。请看[仓库](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-repositories.html)相关信息。

### 支持的平台

正式支持的操作系统和JVM可用矩阵请点击这里：[支持的矩阵](https://www.elastic.co/support/matrix)。

### 安装

在[下载](https://www.elastic.co/downloads/elasticsearch)最新版本并解压之后，运行es：

> **$ bin/elasticsearch**

在类Unix系统里，上边的命令会在前台启动进程。

### 作为守护进程运行

为了在后台运行，需要添加`-d` 选项：

> **$ bin/elasticsearch -d**

### 进程ID

ES进程可以在启动的时候将其进程ID写进指定的文件，这使得以后关闭进程非常容易。

> **<pre>
$ bin/elasticsearch -d -p pid     (1) 
$ kill `cat pid`   (2)   
> </pre>**

(1) 进程ID被写入到叫做`pid`的文件。

(2) `kill`命令向存储在`pid`文件中的进程ID对应的进程发送了`TERM`信号。

###注意

[Linux](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-service.html)和[Windows](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-service-win.html)平台的启动脚本管理为你ES管理ES进程的启动和停止。


><pre>
>*NIX
>在使用`elasticsearch`shell脚本时有些特性可以使用。第一个特性能够在前台或者后台运行es进程，这个在后面会介绍到。

>另一个特性直接向脚本传递 -D 选项或者getopt long 类型的配置参数。一旦设置，所有使用JAVA_OPTS或者ES_JAVA_OPTS设置的变量都会被覆盖，比如：
>
>$ bin/elasticsearch -Des.index.refresh_interval=5s --node.name=my-node
> </pre>

### Java(JVM)版本

ES是用java编写的，至少需要[java 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)才能运行。只有Oracle的 Java和OpenJDK才被支持。所有的ES节点和客服端应该使用相同版本的JVM。

我们推荐推荐**Java 8 update 20 及之后的版本**或者**Java 7 update 55 及之后的版本**。java 7 之前的版本已经发现了会导致索引损坏和数据丢失的bug。

可以在`JAVA_HOME`环境变量中设置java的使用版本。

