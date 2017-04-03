## Reader介绍

适配器是一个进程，将DFlow平台连接到特定类型的外部应用程序或文件（请参阅“概念指南”中的适配器）。选择哪个适配器决定了哪些属性必须指定数据源或目标。

### Readers

Readers通过在数据源和缓存中指定来使用。

一些 Reader 提供了一些解析器。例如，TCPReader可以使用BinaryParser，DSVParser或XMLParser。有关详细信息，请参阅解析器。


### DataBaseReader

DatabaseReader可以从Microsoft SQL Server，MySQL、Postgres和Oracle等关系型数据库中的一个或多个表查询所需要的数据。

<table>
<tr><td>警告</td></tr>
<tr><td>必须按照安装数据库驱动程序中所述安装DBMS的JDBC驱动程序。</td></tr>
</table>


<table>
<tr><td>属性</td><td>类型</td><td>默认值</td><td>解释</td></tr>
<tr><td>ConnectionURL</td><td>java.lang.String</td><td></td><td>JDBC连接URL例如， jdbc:oracle:thin:@192.168.1.2:1521:orcl</td></tr>
<tr><td>ExcludedTables</td><td>	
java.lang.String</td><td></td><td>当指定通配符时，您可以在此处指定要从查询中排除的任何表。指定值Tables。例如，从SQL Server中读取时，要包括来自HR_EMPLOYEES和HR_DEPTS而不是HRMASTER的数据（因为您不能在Tables字符串中指定文字下划线）：<table><td>Tables = 'HR％'，
 ExcludedTables = 'HRMASTER'</td></table></td></tr>
<tr><td>FetchSize</td><td>java.lang.Integer</td><td>100</td><td>在单个JDBC方法执行中要从数据库中获取的最大记录数（请参阅JDBC驱动程序文档中的fetchsize讨论）</td></tr>
<tr><td>Password</td><td>Password</td><td></td><td>请参阅密码加密</td></tr>
<tr><td>Query</td><td>java.lang.String</td><td></td><td>SQL语句指定要返回的数据。您可以查询表，别名，同义词和视图。如果指定Tables，此属性将被忽略。
如果查询包含包含句点的同义词，则必须将其包含在转义引号中。例如：select * from
            \"synonym.name\"
</td></tr>
<tr><td>ReturnDateTimeAs</td><td>	
java.lang.String</td><td>JODA</td><td>设置为  String返回时间戳值作为字符串而不是Joda时间戳。此选项的主要目的是避免在将微秒时间转换为Joda毫秒时丢失精度。字符串的格式为yyyy-mm-dd hh：mm：ss.ffffff。</td></tr>
<tr><td>Tables</td><td>	
java.lang.String</td><td></td><td>要阅读的表格或视图。在MySQL和Oracle名称中区分大小写; 在SQL Server中不区分大小写。
对于Oracle和SQL Server，您可以将多个表和视图指定为以分号或以下通配符分隔的列表：
%：任何的字符
\_：MySQL或Oracle中的任何单个字符（请注意，使用OracleReader时不需要转义下划线）
_：SQL Server中的任何一个字符
例如，HR。％将读取HR模式中的所有表。
从Oracle读取时，_是一个字面的下划线，因此，例如，HR_％将包括HR_EMPLOYEES和HR_DEPTS，但不包括HRMASTER。（请注意，在使用OracleReader的情况下_是相反的情况：通配符  \_是字面的下划线）
从SQL Server读取时，无法指定下划线。
</td></tr>
<tr><td>Username</td><td>java.lang.String</td><td></td><td></td></tr>
</table>

输出类型为WAevent。

<table>
<tr><td>注意</td></tr>
<tr><td>要从Oracle CDB和PDB数据库中的表中读取，您必须创建两个DatabaseReader实例，每个实例一个。</td></tr>
</table>

如下示例为创建一个数据缓存，从MySQL中查询数据。

<code>

CREATE TYPE RackType(
  rack_id String KEY,
  datacenter_id String,
  rack_aisle java.lang.Integer,
  rack_row java.lang.Integer,
  slot_count java.lang.Integer
);
CREATE CACHE ConfiguredRacks USING DatabaseReader (
  ConnectionURL:'jdbc:mysql://10.1.10.149/datacenter',
  Username:'username',
  Password:'passwd',
  Query: "SELECT rack_id,datacenter_id,rack_aisle,rack_row,slot_count FROM RackList"
)
QUERY (keytomap:'rack_id') OF RackType;
The following example creates a cache of data retrieved from an Oracle table:
CREATE TYPE CustomerType (
  IPAddress  String KEY,
  RouterId  String,
  ConnectionMode  String,
  CustomerId  String,
  CustomerName  String
);
CREATE CACHE Customers USING DatabaseReader (
  Password: 'password',
  Username: 'admin',
  ConnectionURL: 'jdbc:oracle:thin:@node05.example.com:1521:test5',
  Query: 'SELECT ip_address, router_id, connection_mode, customer_id, customer_name FROM customers',
  FetchSize: 1000
)
QUERY (keytomap:'IPAddress') OF CustomerType;

</code>

DatabaseReader数据类型支持和转换：

<table>
<tr><td>JDBC列类型</td><td>CQL类型</td></tr>
<tr><td>Types.BIGINT</td><td>java.lang.Long</td></tr>
<tr><td>Types.BIT</td><td>java.lang.Boolean</td></tr>
<tr><td>Types.CHAR</td><td>java.lang.String</td></tr>
<tr><td>Types.DATE</td><td>org.joda.time.LocalDate</td></tr>
<tr><td>Types.DECIMAL</td><td>java.lang.String</td></tr>
<tr><td>Types.DOUBLE</td><td>java.lang.Double</td></tr>
<tr><td>Types.FLOAT</td><td>java.lang.Double</td></tr>
<tr><td>Types.INTEGER</td><td>java.lang.Integer</td></tr>
<tr><td>Types.NUMERIC</td><td>java.lang.String</td></tr>
<tr><td>Types.REAL</td><td>java.lang.Float</td></tr>
<tr><td>Types.SMALLINT</td><td>java.lang.Short</td></tr>
<tr><td>Types.TIMESTAMP</td><td>org.joda.time.DateTime</td></tr>
<tr><td>Types.TINYINT</td><td>java.lang.Short</td></tr>
<tr><td>Types.VARCHARCHAR</td><td>java.lang.String</td></tr>
<tr><td>其他类型</td><td>java.lang.String</td></tr>
</table>














































































