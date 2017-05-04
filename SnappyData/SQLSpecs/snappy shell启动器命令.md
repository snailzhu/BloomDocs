##snappy-shell启动器命令

###使用gfxd命令行实用程序启动RowStore实用程序。

###句法
###注意：尽管RowStore引入了“snappy-shell”实用程序来替换早期的“sqlf”实用程序，但是为了方便起见，“sqlf”仍然作为可选语法提供和支持。

显示snappy-shell命令和选项的完整列表：<br/>
<code>
snappy-shell --help
</code>
<br/>
显示特定实用程序用法的命令窗体是：<br/>
<code>
snappy-shell <utility> --help
</code>
<br/>
没有参数，snappy-shell启动交互式SQL命令shell：<br/>
<code>
snappy-shell
</code>
<br/>
要为交互式snappy-shell会话指定系统属性，必须在开始之前定义JAVA_ARGS环境变量snappy-shell。例如，snappy-shell使用snappy-shell.historysystem属性定义存储在交互式会话期间执行的命令列表的文件。要更改此属性，您可以将其定义为JAVA_ARGS变量的一部分：<br/>
<code>
$ export JAVA_ARGS="-Dgfxd.history=/Users/yozie/snappystore-history.sql"
<br/>
$ snappy
</code>
<br/>
要启动和退出snappy-shell实用程序（而不是启动交互式snappy-shellshell），请使用以下语法：<br/>
<code>
snappy-shell <utility> <arguments for specified utility>
</code>
<br/>
在此命令窗体中，<utility>是以下之一。<br/>

###实用程序名称	描述
###server 服务器	启动并停止RowStore Server成员，并提供正在运行的状态。

###locator 定位器	启动并停止RowStore定位器成员，并提供正在运行的状态。

###version 版	    打印RowStore产品版本信息。

###stats  统计	    从统计档案中打印统计值。

###merge-logs 上合并日志	将多个日志文件合并到一个日志中。

### license 执照	    打印获取新许可证所需的信息。

###encrypt-password 加密密码	在使用BUILTIN身份验证时加密gemfirexd.properties文件中使用的密码，或者使用AsyncEventListener实现或DBSynchronizer的外部数据源使用密码。

###validate-disk-store 验证磁盘店	 确认磁盘存储的文件是否有效。
###version   版	   显示RowStore产品版本信息。

###compact-disk-store 紧凑盘店	压缩脱机磁盘存储，以从永久文件中删除所有不需要的记录。

###compact-all-disk-stores 紧凑的全磁盘存储	要求所有分布式系统成员压缩其磁盘存储。

###revoke-missing-disk-store撤销缺失盘店	要求分布式系统成员停止等待指定的磁盘存储。

###list-missing-disk-stores列表失踪磁盘存储	打印目前从分布式系统中丢失的磁盘存储。

###shut-down-all关机，所有	请求所有数据存储和访问者系统成员关闭。

###backup 备用	请求所有分布式系统成员备份其持久数据。
要在启动snappy-shell实用程序时指定系统属性，请使用-JD property_name = property_value参数。
<br/>

描述<br/>
除了启动RowStore提供的各种实用程序之外，当没有任何参数snappy-shell启动时，启动一个交互式命令shell，您可以使用它来连接到一个RowStore系统并执行各种命令，包括SQL语句。<br/>

启动器会对当前的CLASSPATH环境变量进行荣誉，并将其添加到正在启动的实用程序或命令shell的CLASSPATH中。要将其他参数传递给JVM，请JAVA\_ARGS在调用gfxd脚本时设置环境变量。<br/>

注意： `JAVA \ _ARGS`环境变量不适用于启动单独后台进程的`snappy-shell rowstore server`和`snappy-shell rowstore locator`工具。要将Java属性传递给这些工具，请使用“-J”选项，如这些工具的帮助中所述。<br/>

启动器使用javaPATH中找到的可执行文件。要覆盖此行为，请将GFXD\_JAVA环境变量设置为指向所需的Java可执行文件。（请注意支持的配置和系统要求中支持的JRE版本）。<br/>

备份 为在分布式系统中运行的所有成员创建操作磁盘存储的备份。具有持久数据的每个成员创建自己的配置和磁盘存储的备份。<br/>
compact-all-disk-stores 执行在线压缩RowStore磁盘存储。<br/>
compact-disk-store 执行单个RowStore磁盘存储的离线压缩。<br/>
encrypt-password在配置BUILTIN身份验证或使用AsyncEventListener实现或DBsynchronizer配置访问外部数据源时，生成用于 gemfirexd.properties文件的加密 密码字符串。<br/>
install-jar 安装JAR文件并自动将JAR文件类加载到RowStore类加载器中。这使得JAR类可用于分布式系统的所有成员（包括稍后加入系统的成员）。<br/>
list-missing-disk-stores 列出其他成员正在等待的最新数据的所有磁盘存储。<br/>
定位器 允许分布式系统中的对等体（包括其他定位器）发现对方，而无需对任何其他对等体进行硬编码。<br/>
日志记录支持 您可以指定JDBC引导属性snappy-shell rowstore server和snappy-shell rowstore locator命令，以分别为RowStore服务器和定位器配置日志文件位置和日志严重性级别。<br/>
merge-logs 将多个日志文件合并到一个日志中。<br/>
print-stacks 打印RowStore成员进程的堆栈转储。<br/>
remove-jar 删除JAR文件安装，卸载与JAR关联的所有类。<br/>
replace-jar 使用新的JAR文件的内容替换已安装的JAR文件。新JAR文件中的类将自动加载到RowStore类加载器中，并替换以前为相同JAR标识符安装的任何类。RowStore还重新编译依赖于JAR文件的对象，例如已安装的监听器实现。<br/>
revoke-missing-disk-store 指示RowStore成员停止等待磁盘存储可用。<br/>
运行 连接到RowStore分布式系统并执行SQL命令文件的内容。指定文件中的所有命令必须与交互式gfxd shell兼容。<br/>
服务器 RowStore服务器是RowStore系统中的主要服务器端组件，可提供与集群中其他服务器，对等体和客户端的连接。它可以托管数据。使用 gfxd启动器的服务器实用程序启动服务器。<br/>
show-disk-store-metadata 显示指定磁盘存储目录的磁盘存储元数据。<br/>
shutdown-all 指示所有RowStore访问器和数据存储器成员从分布式系统断开连接并关闭。<br/>
统计信息 显示统计信息归档中的统计值。<br/>
upgrade-disk-store （此版本不支持）将磁盘存储升级到当前版本的RowStore。<br/>
validate-disk-store 验证脱机磁盘存储的运行状况，并使用该磁盘存储提供有关表的信息。<br/>
版本 打印有关RowStore产品版本的信息。<br/>
write-data-dtd-to-file 创建文档类型定义（DTD）文件，该文件指定使用创建的XML数据文件的布局snappy-shell write-data-to-xml。<br/>
write-data-to-db 使用一个或多个数据XML文件（使用snappy-shell write-data-to-xml创建）将数据插入数据库，并将数据库模式定义在一个或多个模式XML文件（与snappy-shell write-schema-to-xml）。该命令通常与RowStore集群一起用于导出表数据，但也可以与其他JDBC数据源一起使用。<br/>
write-data-to-xml 将数据库中的所有表的数据写入XML文件。（您可以使用snappy-shell write-data-dtd-to-file命令创建描述XML文件中数据布局的文档类型定义（DTD）文件。）生成的XML文件可用于重新生成在RowStore或另一个数据库管理系统中将数据加载到表中。该命令通常与RowStore集群一起用于导出表数据，但也可以与其他JDBC数据源一起使用。<br/>
write-schema-to-db 通过使用一个或多个模式XML文件的内容（请参阅snappy-shell write-schema-to-xml）在数据库中创建模式。此命令通常与RowStore集群一起用于导出模式，但也可以与其他JDBC数据源一起使用。<br/>
write-schema-to-sql 将数据库的模式作为SQL命令写入文件。您可以使用生成的文件在RowStore或另一个数据库管理系统中重新创建模式。此命令通常与RowStore集群一起用于导出模式，但也可以与其他JDBC数据源一起使用。<br/>
write-schema-to-xml 将数据库的模式写入XML文件。您可以使用生成的XML文件在RowStore或另一个数据库管理系统中重新创建模式。此命令通常与RowStore集群一起用于导出模式，但也可以与其他JDBC数据源一起使用。
