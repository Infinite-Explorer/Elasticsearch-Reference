### 在Windows上以服务运行

Windows用户可以配置ES作为服务，这样就可以在后台运行或者在开机时不用任何的用户交互就能自动启动。哲可以通过`bin/`目录下面的`service.bat`脚本实现，该脚本使安装，卸载,管理或者配置服务，开启和停止服务等所有的操作都在命令行完成。

> **<pre>
c:\elasticsearch-2.3.4\bin>service
Usage: service.bat install|remove|start|stop|manager [SERVICE_ID]
> </pre>**

该脚本需要一个参数（需要执行的命令），接着一个可选的表示服务id的参数（在安装多个ES服务的时候很有用）

可用命令如下：

`install`

安装ES以作为一个服务

`remove`

移除已安装的ES服务（如果是已经启动的话，还会停止服务）

`start`

启动ES服务（如果已经安装）

`stop`

停止ES服务（如果已经启动）

`manager`

启动一个管理已安装服务的图形界面

注意，在安装的时的环境配置选项在服务的整个生命周期都会被被复制和使用。这意味着在安装之后对它们的任何改动都不会生效，除非服务重装。

基于已安装可用的 JDK/JRE(通过`JAVA_HOME`设置)的架构，合适的64位（x64）或者32位（x86）服务服务会被安装。这个信息在安装时能够看到：

> **<pre>
c:\elasticsearch-{version}bin>service install
Installing service      :  "elasticsearch-service-x64"
Using JAVA_HOME (64-bit):  "c:\jvm\jdk1.8"
The service 'elasticsearch-service-x64' has been installed.
> </pre>**

### 注意

尽管JRE可以被ES服务使用，由于其使用了客户端虚拟机（相对于服务端JVM能为长久运行的应用提供更好的性能），其不备推荐使用并且会发出一个警告。

