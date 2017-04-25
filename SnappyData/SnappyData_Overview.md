## SnappyData 概述
本节介绍几种常见操作，例如启动集群，使用表（加载，查询，更新），处理流和运行近似查询。


您可以通过以下任何方式运行示例：

在本地模式下：通过使用bin/run-example脚本（运行Scala示例）或使用bin/spark-submit脚本（运行Python示例）。这些示例与Spark + SnappyData Store并置在同一个JVM中。

作为一个工作：许多Scala示例也被实现为SnappyData作业。在这种情况下，可以将示例作为作业提交到正在运行的SnappyData集群。有关如何运行作业的详细信息，请参阅作业部分。

注意：SnappyData还支持Java API。请参阅文件针对Java API的更多细节。


### 如何启动SnappyData群集
#### 在单台机器上启动SnappyData集群

启动群集：运行sbin/snappy-start-all.sh脚本以使用默认设置在单台计算机上启动SnappyData群集。这将启动一个引导节点，一个定位器和一个数据服务器。

<code>
$ sbin/snappy-start-all.sh
</code>

在本地机上的整个集群启动可能需要30秒或更长时间来启动。

打印输出：输出显示如下：

<code>
Starting SnappyData Locator using peer discovery on: localhost[10334]
Starting DRDA server for SnappyData at address localhost/127.0.0.1[1527]
Logs generated in /home/user/snappyData/work/localhost-locator-1/snappylocator.log
SnappyData Locator pid: 9368 status: running
Starting SnappyData Server using locators for peer discovery: user1-laptop[10334]
Starting DRDA server for SnappyData at address localhost/127.0.0.1[1527]
Logs generated in /home/user1/snappyData/work/localhost-server-1/snappyserver.log
SnappyData Server pid: 9519 status: running
  Distributed system now has 2 members.
  Other members: localhost(9368:locator)<v0>:16944
Starting SnappyData Leader using locators for peer discovery: user1-laptop[10334]
Logs generated in /home/user1/snappyData/work/localhost-lead-1/snappyleader.log
SnappyData Leader pid: 9699 status: running
  Distributed system now has 3 members.
  Other members: localhost(9368:locator)<v0>:16944, 192.168.63.1(9519:datastore)<v1>:46966
</code>

检查状态：您可以使用以下命令检查正在运行的集群的状态：

<code>
$ sbin/snappy-status-all.sh
SnappyData Locator pid: 9368 status: running
SnappyData Server pid: 9519 status: running
  Distributed system now has 2 members.
  Other members: localhost(9368:locator)<v0>:16944
SnappyData Leader pid: 9699 status: running
  Distributed system now has 3 members.
  Other members: localhost(9368:locator)<v0>:16944, 192.168.63.1(9519:datastore)<v1>:46966

</code>

您可以通过http://<leadHostname>:5050在浏览器中打开来检查SnappyData UI。使用Snappy SQL shell连接到集群并执行各种SQL操作。

关闭群集：您可以使用以下命令关闭群集：

<code>
$ sbin/snappy-stop-all.sh
The SnappyData Leader has stopped.
The SnappyData Server has stopped.
The SnappyData Locator has stopped.
</code> 

### 



### 


### 










































