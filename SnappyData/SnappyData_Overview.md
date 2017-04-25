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


### SnappyData思路



### 主要特性


### Spark运行时的扩展










































