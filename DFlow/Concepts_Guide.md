## DFlow关键概念

DFlow 关键概念主要包括应用程序、流程、数据源、数据流、类型、事件、窗口、连续查询(CQ)、缓存、数据存储、数据目标和订阅等。


### 应用程序

DFlow 可让您开发和运行从外部来源获取数据的自定义应用程序，并且对它们进行实时处理，并通过DFlow仪表板或其他应用程序进行消费。在SQL环境中，每个应用程序的核心都是一个或多个查询。如本概念指南的其余部分所述，应用程序还包含汇聚到一个或多个流中的源，目标和其他逻辑组件，以及其使用的任何图表，映射或其他内置可视化的定义。

应用程序可以使用Web客户端或使用TQL查询语言（TQL）进行图形化创建，TQL是可以使用Java扩展的类似SQL语法（请参阅示例应用程序指南）。TQL也由平台的命令行客户端Tungsten控制台使用(借鉴Spark Tungsten)。

### 数据流

流程定义应用程序接收的数据，处理数据的方式及其结果。

流程由几种组件组成：

来自适配器的实时事件数据

定义其他组件之间定义的数据流

窗口通过时间或计数来绑定事件数据

连续查询过滤，聚合，加入，丰富和转换数据

缓存历史或参考数据，以丰富事件数据

数据存储填充内置的报告和可视化，并持久化处理完的数据

数据目标将数据传递到外部应用程序

应用程序可能包含多个流，以将组件组织成逻辑组。以MultiLogApp为例。

应用程序本身就是可以包含其他流的流，所以当应用程序只包含一个流时，它不需要被显式地创建。以PosApp为例。

### 数据源

数据源是流的起点，定义如何从外部数据源获取数据。数据流可能有多个数据来源。

每个数据源指定：

用于从外部数据源（例如日志文件）（适配器是DFlow服务器的组件）收集实时数据的输入适配器（读取器）;有关详细信息，请参阅阅读器）

所选适配器所需的属性，如主机名，目录路径，身份验证凭证等

使用一些适配器，一个解析器定义了如何处理来自数据源的数据（例如，DSVParser解析分隔的文件，或FreeFormTextParser使用正则表达式解析）

输出流将数据传递给其他流量组件

以下是MultiLogApp示例应用程序的其中一个数据源的TQL代码：

<code>
 CREATE SOURCE Log4JSource USING FileReader (
  directory:'Samples/MultiLogApp/appData',
  wildcard:'log4jLog.xml',
  positionByEOF:false
) 
PARSE USING XMLParser(
  rootnode:'/log4j:event',
  columnlist:'log4j:event/@timestamp,
    log4j:event/@level,
    log4j:event/log4j:message,
    log4j:event/log4j:throwable,
    log4j:event/log4j:locationInfo/@class,
    log4j:event/log4j:locationInfo/@method,
    log4j:event/log4j:locationInfo/@file,
    log4j:event/log4j:locationInfo/@line'
)
OUTPUT TO RawXMLStream;
</code>


Log4JSource使用FileReader适配器读取 /Samples/MultiLogapp/appData/log4jLog.xml，使用XMLPaarser进行解析，并将数据输出到RawXMLStream。在UI中，相同的数据源配置如下所示：

图

注意：“概念指南”中的其他示例仅显示在其TQL表单中，但它们都具有与上述类似的UI对应项。


### 数据流

数据流将一个组件的输出传递给一个或多个其他组件。例如，仅写入文件的简单流程可能具有以下顺序：

<code>
source > stream1> queryA> stream2> FileWriter
</code>

这个更复杂的流程在stream2中分支，以便发送警报并填充仪表板：

<code>
source > stream1 > queryA > stream2 > ...
... stream2 > queryB > stream3 > Subscription
... stream2 > queryC > WActionStore
</code>













































































