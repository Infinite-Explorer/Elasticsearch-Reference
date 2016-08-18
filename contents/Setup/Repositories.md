### 仓库

我们也有利用APT和YUM发布的仓库可以使用。注意，我们只提供二进制包，而不是源码包，因为该包是作为ES构建的一部分被创建的。

我们已经将主要版本分割成单独的url，以避免在多个主要版本之间不自觉地进行升级。所有的2.x 版本我们使用2.x版本号发行，3.x.y版本使用3.x发行，以此类推。

我们使用PGP密钥，ES签名密钥，使用指纹

    4609 5ACC 8548 582C 1A26 99A9 D27D 666C D88E 42B4

为我们所有的包签名。可以在[https://pgp.mit.edu/](https://pgp.mit.edu/)上找到。

### APT

下载并安装公开签名密钥：

> **wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -**

将定义库的命令写到`/etc/apt/sources.list.d/elasticsearch-2.x.list`中：

> **echo "deb https://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list**

#### 警告

使用上面说的`echo`方法去添加ES仓库。而不要使用`add-apt-respository`，因为这会同时添加一个`deb-src`条目，但是我们却不会提供源码包。如果你添加了`deb-src`条目，你会看到下面这个错误：

    Unable to find expected entry 'main/source/Sources' in Release file (Wrong sources.list entry or malformed file)

只要从`/etc/apt/source.list`中删除`deb-src`就能正常安装了。

如果想使用apt-get更新并要使用ES仓库。你可以这样安装它：

> **sudo apt-get update && sudo apt-get install elasticsearch**

#### 警告

如果在同一个ES仓库中存在两个条目，在执行`apt-get update`的时候你会看到下面的错误：

    Duplicate sources.list entry https://packages.elastic.co/elasticsearch/2.x/debian/ ...`

检查`/etc/apt/sources.list.d/elasticsearch-2.x.list`中重复的条目或者在`/etc/apt/sources.list.d/`中的文件和文件`/etc/apt/sources.list`中定位重复的条目。

配置ES在系统引导过程中自动启动。如果你的系统是使用SysV 初始化，你就需要运行：

> **sudo update-rc.d elasticsearch defaults 95 10**

如若你的系统是使用systemd：

> **<pre>
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
> </pre>**

### YUM/DNF

下载安装公开签名密钥：

> **rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch**

将下面的内容写到`/etc/yum.repos.d/`目录下以`.repo`作为后缀的一个文件中，例如`elasticsearch.repo`

> **<pre>
[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=https://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
> </pre>**

你的仓库就可以使用了。你可以这样安装ES：

> **yum install elasticsearch**

或者针对较新版本的Fedro和Redhat：

> **dnf install elasticsearch**

配置ES在系统引导过程中自动启动。如果你的系统是使用SysV 初始化(使用命令`ps -p 1`检查)，你就需要运行：

##### 警告

仓库不能在基于较老的依然在使用RPM v3的系统中正常工作，例如CentOS5。

> **chkconfig --add elasticsearch**

如果你的系统使用的是`systemd`:

> **<pre>
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
> </pre>**



