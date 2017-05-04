#  Snappy sql 规范
##一  查询
###1      根据表中内置的现有表或常量创建虚拟表。
语法：
<br/>
<code>
{ 
( Query ) 
| 
SelectExpression  | VALUES 
}
</code>
<br/>
描述：查询根据现有的表或内建的常量创建一个虚拟表。
<br/>
举例：
<br/>
<code>
-- a Select expression 
<br/>
SELECT * FROM TRADE.CUSTOMERS 
<br/>
-- a subquery 
<br/>
SELECT * FROM TRADE.CUSTOMERS C WHERE EXISTS (SELECT * FROM TRADE.PORTFOLIO F WHERE F.CID = C.CID AND TID =? AND F.QTY >927)
<br/>
-- a values expression 
<br/>
VALUES (1,2,3
</code>
<br/>
2描述：该查询必须使用单个列计算为单个行。
有时也称为表达式子查询。
<br/>
例
<br/>
-- avg always returns a single value, so the subquery is a ScalarSubquery 
<br/>
SELECT S.CID FROM TRADE.SELLORDERS S WHERE (SELECT SUM(QTY) FROM TRADE.PORTFOLIO F WHERE F.CID = S.CID GROUP BY F.CID) > 1277 AND TID =1
<br/>
###TableSubquery
<br/>
TableSubquery是一个返回多行的子查询。
<br/>
与ScalarSubquery不同，TableSubquery仅允许使用EXISTS，IN或量化比较。
<br/>
当与EXISTS一起使用时，只有使用*返回多个列时，才会返回多个列。
<br/>
当与IN或量化比较一起使用时，它必须返回单个列。
<br/>
注意：不支持在FROM子句中使用TableSubquery作为TableExpression，即使这不会引发语法错误。
<br/>
例子：
<br/>
<code>
-- a subquery used with EXISTS 
<br/>

SELECT C.CID FROM TRADE.CUSTOMERS C WHERE EXISTS (SELECT * FROM TRADE.PORTFOLIO F WHERE F.CID = C.CID AND TID = 1 AND F.QTY >927) 
-- a subquery used with IN 
SELECT F.CID FROM TRADE.PORTFOLIO F WHERE  F.CID IN (SELECT CID FROM  TRADE.CUSTOMERS C WHERE SINCE > ?) 
<br/>
-- a subquery used with a quantified comparison 
<br/>
SELECT S.CID FROM TRADE.SELLORDERS S WHERE (SELECT SUM(QTY) FROM TRADE.PORTTOLIO F WHERE F.CID = S.CID GROUP BY F.CID) > 1231 
</code>
<br/>
##二 语句

### 1  ALTER TABLE 使用ALTER TABLE语句将列和约束添加到现有表，从表中删除它们，或修改其他表功能，如AsyncEventListener实现和网关发送器配置。
<code>
ALTER TABLE table-name
<br/>
{
<br/>
  ADD COLUMN column-definition |
<br/>
  ADD table-constraint |
<br/>
  ALTER [ COLUMN ] column-name SET GENERATED ALWAYS AS IDENTITY |
<br/>
  DROP [ COLUMN ] column-name [ RESTRICT ] |
<br/>
  DROP { PRIMARY KEY | FOREIGN KEY constraint-name | UNIQUE 
<br/>
    constraint-name | CHECK constraint-name | CONSTRAINT constraint-name } |
<br/>
  SET EVICTION MAXSIZE integer-constant |
<br/>
  SET EVICTION FREQUENCY integer-constant { SECONDS | HOURS | DAYS } [ START { D 'date-constant' | T 'time-constant' | TS 'timestamp-constant' } ] |
  SET GATEWAYSENDER ( [sender-name] [, sender-name] * ) |
  SET ASYNCEVENTLISTENER ( [listener-name] [, listener-name] * )
<br/>
}
<br/>
</code>
###2 CREATE语句 使用Create语句来创建函数，索引，过程，模式，同义词，表，触发器和视图。
<br/>
创建：表
<br/>
语法：
<br/>
<code>
CREATE TABLE table-name {
<br/>
    ( { column-definition | table-constraint }
<br/>
    [ , { column-definition | table-constraint } ] * )
| 
    [ ( column-name [, column-name ] * ) ]
    AS query-expression
    WITH NO DATA
<br/>
}
<br/>
    [ partitioning-clause | REPLICATE ]
<br/>
    [ server-groups-clause ]
<br/>
    [ gateway-sender-clause ]
<br/>
    [ async-event-listener-clause ]
<br/>
    [ eviction-clause | eviction-by-criteria-clause]
<br/>
    [ expiration-clause ] *
<br/>
    [ persistence-clause ]
<br/>
    [ offheap-clause ]
<br/>

 CREATE TABLE HOTELAVAILABILITY
<br/>
    (HOTEL_ID INT NOT NULL, 
<br/>
     BOOKING_DATE DATE NOT NULL,
<br/>
     ROOMS_TAKEN INT DEFAULT 0,
<br/>
     PRIMARY KEY (HOTEL_ID, BOOKING_DATE));
<br/>
###CREATE PROCEDURE 创建可以使用CALL语句调用的Java存储过程。
<br/>
CREATE PROCEDURE procedure-name
<br/>
([ procedure-parameter [, procedure-parameter] * ])
<br/>
[ procedure-element ] *
<br/>

###CREATE PROCEDURE创建可以使用CALL语句调用的Java存储过程。在当前成员上调用一个过程。因为这个过程是用Java代码编写的，所以它可以在内部使用JDBC调用SQL命令。合格的过程名称不能使用为内部使用保留的SYS模式。以下部分描述语法。
<br/>
RowStore不支持在过程或函数正文中执行DDL语句。
<br/>

###3 CREATE HDFSSTORE 创建与Hadoop名称节点的连接，以便将一个或多个表持久化到HDFS。
<br/>
创建与Hadoop名称节点的连接，以便将一个或多个表持久化到HDFS。每个连接定义用于持久化数据的HDFS NameNode和目录，以及用于配置用于持久化表事件的队列，启用持久性连接，压缩HDFS操作日志等的RowStore特定选项。
<br/>
<code>
CREATE HDFSSTORE store-name
<br/>
  NAMENODE 'url'
<br/>
  [ HomeDir 'directory-name' ]<br/>
  [ BatchSize integer-constant ]<br/>
  [ BatchTimeInterval integer-constant { MILLISECONDS | SECONDS | MINUTES | HOURS | DAYS } ]<br/>
  [ MaxQueueMemory integer-constant ]<br/>
  [ DispatcherThreads integer-constant ]<br/>
  [ QueuePersistent boolean-constant ] <br/>
  [ DiskSynchronous boolean-constant ]<br/>
  [ DiskStoreName store-name ]<br/>
  [ MinorCompact boolean-constant ]<br/>
  [ MaxInputFileSize integer-constant ]<br/>
  [ MinInputFileCount integer-constant ]<br/>
  [ MaxInputFileCount integer-constant ]<br/>
  [ MinorCompactionThreads integer-constant ]<br/>
  [ MajorCompact boolean-constant ]<br/>
  [ MajorCompactionInterval integer-constant { MILLISECONDS | SECONDS | MINUTES | HOURS | DAYS } ]<br/>
  [ MajorCompactionThreads integer-constant ]<br/>
  [ MaxWriteOnlyFileSize integer-constant ]<br/>
  [ WriteOnlyFileRolloverInterval  integer-constant { MILLISECONDS | SECONDS | MINUTES | HOURS | DAYS } ]<br/>
  [ BlockCacheSize integer-constant ]<br/>
  [ ClientConfigFile 'file-name' ]<br/>
  [ PurgeInterval integer-constant { MILLISECONDS | SECONDS | MINUTES | HOURS | DAYS } ]<br/>
</code>
<br/>
注意：
<br/> HDFSSTORE可以使用HDFS只写模型或HDFS读/写模型来保存表数据; 您可以使用CREATE TABLE语句指定HDFS持久性模型。虽然多个表可以使用相同的HDFSSTORE来保持持久性，但通常需要创建多个HDFSSTORE配置来修改每个表的队列和压缩行为。
<br/>
HDFS访问的所有身份验证和授权与执行RowStore成员进程的用户相关联。例如，当您在RowStore中创建HDFS存储时，商店的默认目录结构是在HDFS 的/ user / * user-name *目录下创建的，其中* user-name *是RowStore的进程所有者。（您可以通过指定以“/”字符开头的绝对路径，或者使用hdfs-root-dir属性为服务器设置备用HDFS根目录来覆盖此默认值。）RowStore用户必须能够使用Kerberos进行身份验证，并且必须对每个Hadoop集群节点上的HDFS存储目录具有读/写权限。请参阅安全HDFS的配置要求。
<br/>
###CREATE VIEW 视图是由查询形成的虚拟表。
<br/>
CREATE VIEW view-name<br/>
    [ ( simple-column-name [, simple-column-name] * ) ]<br/>
AS view_query <br/>
视图是由查询形成的虚拟表。视图是一个字典对象，您可以使用它，直到放置它。视图不可更新。<br/>
注意：
<br/>您在CREATE VIEW语句中提供的查询不能使用以下任何SQL子句： - ORDER BY -OFFSET和FETCH FIRST子句 此外，RowStore不支持涉及分组，聚合，独立或连接操作的视图一个分区表。<br/>
视图所有者自动获得视图上的SELECT权限。SELECT权限无法从视图所有者撤销。启动进程的分布式成员所有者自动获得该视图的SELECT权限，并且可以将此权限授予其他用户。分配的成员所有者不能撤销SELECT权限。CREATE SCHEMA提供额外的所有权细节。<br/>
###4   DELETE 从表中删除行。
<br/>
     语法：
<br/>
{
<br/>
    DELETE FROM table-name [ [ AS ] correlation-name ]<br/>
        [ WHERE ]<br/>
}<br/>
描述：<br/>
此表单称为搜索删除，删除由表名和WHERE子句标识的所有行。<br/>
###5   DROP语句   删除语句用于删除函数，索引，过程，模式，表，触发器和视图。
###DROP INDEX 删除指定的索引。
<br/>
DROP INDEX [ IF EXISTS ] [schema-name.]index-name<br/>
描述：<br/>
删除给定模式中的索引（或当前模式，如果没有提供）。IF EXISTS只有当RowStore中存在指定的索引时，才包括子句来执行该语句。<br/>
###DROP DISKSTORE 从RowStore集群中删除磁盘存储配置。
<br/>
DROP DISKSTORE [ IF EXISTS ] store-name
<br/>
IF EXISTS 只有当RowStore中存在指定的磁盘存储区时，才
包括该IF EXISTS子句来执行该语句。<br/>
store-name <br/>
要删除的磁盘存储配置的用户定义名称。可用的名称存储在SYSDISKSTORES系统表中。<br/>
例：<br/>
此命令从集群中删除磁盘存储“STORE1”：<br/>
DROP DISKSTORE store1;<br/>
###DROP SCHEMA 
<br/>
删除指定的空模式。<br/>
句法<br/>
DROP SCHEMA [ IF EXISTS ] schema-name RESTRICT<br/>
描述<br/>
IF EXISTS仅当RowStore中存在指定的模式时，才包括子句来执行该语句。RESTRICT关键字强制执行规则，即在指定的架构中不能定义要从数据库中删除的架构的对象。RESTRICT关键字是必需的，因为RowStore不能删除包含数据库对象的模式。<br/>
例<br/>
<code>
DROP SCHEMA EMP RESTRICT;</code>
<br/>
如果EMP模式中没有定义任何对象，则EMP架构只能从数据库中删除。
<br/>
###DROP TABLE 删除指定的表。<br/>
DROP TABLE [ IF EXISTS ] [schema-name.]table-name<br/>
DROP TRIGGER 删除指定的触发器。<br/>
删除指定的视图。<br/>
句法：<br/>
DROP VIEW [ IF EXISTS ] view-name<br/>
描述：<br/>
   删除指定的视图。IF EXISTS只有当RowStore中存在指定的视图时，才包括子句来执行该语句。<br/>
###6   GRANT 启用特定用户或所有用户的权限。
<br/>
###7    INSERT INSERT语句创建一行或多行并将其存储在命名表中。在INSERT语句中分配的值的数目必须与指定或隐含列的数量相同。
<br/>
###8    REVOKE 撤销对表或例程的权限。
<br/>
###9     SELECT 具有可选ORDER BY CLAUSE和可选的FOR UPDATE子句的查询。
<br/>
<code>
SELECT COUNT(*),SID,AVG(QTY)<br/>
FROM TRADE.PORTFOLIO<br/>
GROUP BY SID<br/>
ORDER BY 3<br/>
</code>
WHERE子句使得根据布尔表达式从结果中过滤行。结果中只返回表达式求值为TRUE的行。<br/>
GROUP BY子句将结果中的行分组为具有一个或多个列的匹配值的子集。GROUP BY子句通常与聚合一起使用。<br/>
RowStore处理一个SelectExpression按以下顺序：<br/>
###FROM子句<br/>
###WHERE子句
###GROUP BY（或隐式GROUP BY）
###HAVING子句
###SELECT子句
###10    SET ISOLATION 更改连接的事务隔离级别。
<br/>
###11    SET SCHEMA 设置或更改连接会话的默认模式。
<br/>
###12    TRUNCATE TABLE 从表中删除所有内容，并将其返回到其初始的空状态。TRUNCATE TABLE清除指定表的所有内存中数据以及持久存储到RowStore磁盘存储的任何数据。对于HDFS读写表，TRUNCATE TABLE还标记表的HDFS持久性文件以进行验证。对于HDFS只写表，TRUNCATE TABLE保留存储在HDFS日志文件中的所有表数据，以便稍后使用MapReduce或HAWQ进行处理。
<br/>
WHERE子句使得根据布尔表达式从结果中过滤行。结果中只返回表达式求值为TRUE的行。<br/>
GROUP BY子句将结果中的行分组为具有一个或多个列的匹配值的子集。GROUP BY子句通常与聚合一起使用。<br/>
###13   UPDATE 更新一个或多个列的值。
<br/>
句法：
<br/>
<code>
{ UPDATE table-name [ [ AS ] correlation-name]
        SET column-name = value
        [, column-name = value} ]*
        [ WHERE ]    
}
</code>
描述：<br/>
      这种形式的UPDATE语句称为搜索更新。它将为WHERE子句评估为TRUE的表的所有行更新一个或多个列的值。
<br/>为更新值指定DEFAULT将列的值设置为为该表定义的默认值。<br/>
UPDATE语句返回更新的行数。<br/>


###表达式
<code>
{
<br/>
VALUES ( Value {, Value }* )
<br/>
[ , ( Value {, Value }* ) ]* |
<br/>
VALUES Value [ , Value ]*
<br/>
}<br/>
</code>
其中Value定义为：<br/>
<code>
Expression | DEFAULT<br/>
INSERT INTO TRADE.CUSTOMERS (CID, CUST_NAME, ADDR,TID)
<br/>
VALUES (1, 'JOHN', 'VMWARE ',1),
<br/>
(2,'SMITH', 'VMWARE', 2)
<br/>
INSERT INTO TRADE.SELLORDERS (OID , CID, SID, STATUS, TID)
<br/>
VALUES (1,1,2,DEFAULT,2)
<br/>
-- using a built-in function
<br/>
VALUES CURRENT_DATE
<br/>
-- getting the value of an arbitrary expression
<br/>
VALUES (3*29, 26.0E0/3)
</code>
<br/>
###WHERE子句
<code>
WHERE Boolean expressions
<br/>
SELECT *
<br/>
FROM FlightAvailability
<br/>
WHERE business_seats_taken IS NULL
<br/>
OR business_seats_taken = 0
<br/>

SELECT THIS_EMP.EMPNO, THIS_EMP.SALARY, DINFO.AVGSALARY, DINFO.EMPCOUNT
<br/>
 FROM EMPLOYEE THIS_EMP,<br/>
   (SELECT OTHERS.WORKDEPT AS DEPTNO,<br/>
           AVG(OTHERS.SALARY) AS AVGSALARY,<br/>
           COUNT(*) AS EMPCOUNT<br/>
    FROM EMPLOYEE OTHERS<br/>
    GROUP BY OTHERS.WORKDEPT<br/>
   )AS DINFO<br/>
 WHERE THIS_EMP.JOB = 'SALESREP'<br/>
           AND THIS_EMP.WORKDEPT = DINFO.DEPTNO<br/>






SELECT [ DISTINCT | ALL ] SelectItem [ , SelectItem ]*<br/>
FROM<br/>
[ WHERE]<br/>
GROUP BY<br/>
HAVING<br/>

SELECT COUNT(*),SID,AVG(QTY)<br/>
FROM TRADE.PORTFOLIO<br/>
GROUP BY SID<br/>
ORDER BY 3<br/>
</code>
##数据类型Data Types
###BIGINT
       为长整数值提供8字节存储。
###BLOB
        二进制大对象表示长度不同的原始字节数组。
###CHAR
       提供固定长度的字符串。
###CHAR FOR BIT DATA
###提供固定长度的字节串
###CLOB
###DATE
###提供将日期存储为年月日
###DECIMAL提供具有指定精度和比例的精确十进制值。
###DOUBLE  Provides 8-byte storage for numbers using IEEE floating-point notation.
###DOUBLE PRECISION
###FLOAT
###INTEGER
###JSON
###LONG VARCHAR
###LONG VARCHAR FOR BIT DATA
###NUMERIC
###REAL
###SMALLINT
###TIME
###TIMESTAMP
###User-Defined Types
###VARCHAR
###VARCHAR FOR BIT DATA
###XML

###BIGINT为长整数值提供8字节存储。如果该值溢出较小类型允许的最大值，则尝试将BIGINT值放入具有较小尺寸/精度的另一精确数字类型（例如INT）中。
###对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，将另一数字数据类型的列中的一个数字数据类型的值存储。
<br/>
等效的Java类型      	java.lang.Long<br/>
最小值            	java.lang.Long.MIN_VALUE（-9223372036854775808）<br/>
最大值           	java.lang.Long.MAX_VALUE（9223372036854775807）<br/>
JDBC元数据类型   	java.sql.Types.BIGINT<br/>
JDBC方法	            ResultSet.getLong，PreparedStatement.setLong<br/>
###BLOB
###二进制大对象表示长度不同的原始字节数组。
<br/>
等效的Java类型	        java.lang.Blob<br/>
最大长度（也是默认长度）  	2 GB - 1（或2,147,483,647）<br/>
JDBC元数据类型	        java.sql.Types.BLOB<br/>
JDBC方法	                ResultSet.getBlob，PreparedStatement.setBlob<br/>

###{ BLOB | BINARY LARGE OBJECT } [ ( length [{ K | M | G }] ) ] 
默认情况下，BLOB的长度以字节数表示。后缀K，M和G代表千字节，兆字节和千兆字节，分别使用1024，1024 * 1024或1024 * 1024 * 1024的倍数。
<br/>
<code>
CREATE TABLE blob_data(id INT primary key, data BLOB(10M)); 
–- search for a blob 
select length(data) from blob_data where id = 100;
</code>
###CHAR
提供固定长度的字符串。如果字符串值小于预期长度，则插入空格以将字符串填充到预期长度。如果字符串值长于预期长度，则会修剪任何尾部空白以使长度与预期长度相同，如果需要截断空格以外的字符，则会引发异常。对于比较操作，较短的CHAR字符串用空格填充到较长的值。类似地，当在表达式中混合CHAR和VARCHAR时，较短的值用空格填充到更长的字符串长度。
<br/>
要在字符串中表示单引号，请使用两个引号：<br/>
VALUES 'going to Chandra''s place' 
<br/>
CHAR的长度是无符号整数常数。<br/>
	
等效的Java类型	java.lang.String<br/>
最大长度	        java.lang.Integer.MAX_VALUE（2147483647）<br/>
默认长度       	1<br/>
JDBC元数据类型	java.sql.Types.CHAR<br/>
JDBC方法	        ResultSet.getString，PreparedStatement.setString<br/>
CHAR            [ACTER] [(length)] <br/>
###字数数据
提供固定长度的字节串。由于非结构化数据或其他原因，CHAR类型不合适时使用。如果值小于预期长度，则填充0x20字节值。对于CHAR FOR BIT DATA和VARCHAR FOR BIT DATA类型，根据SQL-92标准（这与某些DBMS的行为方式不同），不执行填充。涉及VITAR FOR BIT DATA和CHAR FOR BIT DATA的操作产生VARCHAR FOR BIT DATA类型的值。<br/>
长度是以字节为单位的值的长度的无符号整数常量。
	
等效的Java类型	根据需要使用byte []，java.io.InputStream或java.lang.OutputStream
最大长度	254字节
默认长度	1
JDBC元数据类型	java.sql.Types.BINARY
JDBC方法	ResultSet.getBytes，PreparedStatement.setBytes
###{ CHAR | CHARACTER }[(length)] FOR BIT DATA 
###CLOB
字符大对象表示长度不同的字符数组。它用于存储大量基于字符的数据，如文档。<br/>
长度以数字字符表示，除非您指定分别使用1024，1024 * 1024或1024 * 1024 * 1024的倍数的后缀K，M或G。<br/>
	
等效的Java类型	java.sql.Clob<br/>
最大长度（也是默认长度）	2 GB - 1（或2,147,483,647）<br/>
JDBC元数据类型	java.sql.Types.CLOB<br/>
JDBC方法	ResultSet.getClob，PreparedStatement.setClob<br/>
{ CLOB | CHARACTER LARGE OBJECT } [ ( length [{ K | M | G }] ) ] <br/>
<code>
CREATE TABLE clob_data(id INT primary key, text CLOB(10M)); <br/>
–- search for a clob<br/>
select text from clob_data where id = 100;<br/>
</code>

###日期   
###提供将日期存储为年月日。支持的格式有：yyyy-mm-dd  mm/dd/yyyy  dd.mm.yyyy 
年（yyyy）必须始终以四位数表示，而月（mm）和日（dd）可能有一位或两位数字。DATE，TIME和TIMESTAMP不得在表达式中彼此混合，除了具有明确的CAST。<br/>
	
等效的Java类型	java.sql.Date<br/>
JDBC元数据类型	java.sql.Types.DATE<br/>
JDBC方法	ResultSet.getDate，PreparedStatement.setDate<br/>
VALUES '2010-05-04'<br/>
VALUES DATE('2001-10-12')<br/>
后一个示例使用内置函数和过程中描述的DATE（）函数。<br/>
###DECIMAL
提供具有指定精度和比例的精确十进制值。精度是小数点左侧和右侧的总位数，而缩放比例是小数点右侧小数位数。<br/>
只要非分数精度不会丢失，数值（例如INT，BIGINT，SMALLINT）可以放入DECIMAL，否则抛出范围异常（SQLState：“22003”）。当截断来自DECIMAL的尾数时，该值被舍入。<br/>
对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，十进制算术的缩放和另一数字数据类型的列中的一个数值数据类型的值。<br/>
	
等效的Java类型	java.math.BigDecimal<br/>
精度最小/最大	1到31<br/>
缩小最小/最大	小于或等于精度<br/>
默认精度	5<br/>
默认比例	0<br/>
JDBC元数据类型	java.sql.Types.DECIMAL<br/>
JDBC方法	ResultSet.getBigDecimal，PreparedStatement.setBigDecimal<br/>
{ DECIMAL | DEC } [(precision [, scale ])]<br/>
-- this cast loses fractional precision <br/>
values cast (23.8372 AS decimal(4,1)); <br/>
-–- results in:<br/> 
23.8 <br/>
-- this cast is outside the range <br/>
values cast (97824 AS decimal(4,1)); <br/>
–-- throws exception: <br/>
ERROR 22003: The resulting value is outside the range for the data type DECIMAL/NUMERIC(4,1). <br/>
###FLOAT
使用IEEE浮点表示法为数字提供8字节存储。<br/>
算术运算不会使其结果值为零。如果值太小，您将收到异常。数字浮点常数限制为30个字符的长度。<br/>
对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，并在另一数字数据类型的列中存储一个数字数据类型的值。<br/>
	
等效的Java类型	java.lang.Double<br/>
注意：如下所述，最大/最小限制与java.lang.Double的不同。<br/>
最小值	-1.79769E + 30<br/>
最大值	1.79769E + 308<br/>
最小正值	2.225E-307<br/>
最大的负值	-2.225E-307<br/>
默认精度	5<br/>
默认比例	0<br/>
JDBC元数据类型	java.sql.Types.DOUBLE<br/>
JDBC方法	ResultSet.getDouble，PreparedStatement.setDouble<br/>
–- examples of valid values <br/>
values 233.31E3; <br/>
values 8928E+06; <br/>
-- this example will throw a range exception (SQLState: "42820") <br/>
values 123456789012345678901234567890123456789e0; <br/>
###双重精准<br/>
###Synonyn为DOUBLE。<br/>
###浮动<br/>
具有REAL或DOUBLE数据类型的别名，具体取决于指定的精度。默认精度为53，相当于DOUBLE。23或更小的精度使FLOAT等效于REAL，而大于23则相当于DOUBLE。<br/>
	
等效的Java类型	java.lang.Double或java.lang.Float取决于精度<br/>
最小/最大限度	如果精度小于23，则与FLOAT相同。否则，与DOUBLE相同的最小/最大极限相同。<br/>
默认精度	53<br/>
JDBC元数据类型	java.sql.Types.FLOAT<br/>
JDBC方法	ResultSet.getFloat / getDouble，PreparedStatement.setFloat / setDouble<br/>
FLOAT [(precision)]<br/>
###整数
为整数值提供4字节存储。INT可以用作CREATE TABLE中的INTEGER的同义词。<br/>
对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，并在另一数字数据类型的列中存储一个数字数据类型的值。<br/>
	
等效的Java类型	java.lang.Integer<br/>
最小值	java.lang.Integer.MIN_VALUE（-2147483648）
<br/>
最大值	java.lang.Integer.MAX_VALUE（21474836487）
<br/>
JDBC元数据类型	java.sql.Types.INTEGER<br/>
JDBC方法	ResultSet.getInt，PreparedStatement.setInt   <br/>
###JSON
###JSON格式（JavaScript对象符号）字符串。JSON类型的列可以与JSON_EVALPATH函数一起使用，以提取JSON属性或使用JSON路径过滤查询结果。
###RowStore尝试在插入期间使用public com.gemstone.gemfire.pdx.JSONFormatter API 解析插入的数据为JSON 。如果RowStore无法将数据解析为JSON，则抛出SQLException并且插入失败。
###注意： RowStore不保留您插入的JSON数据中存在的空格或其他格式。此外，RowStore在查询JSON列值时，会添加空格和换行符来格式化字符串输出。RowStore不对JSON数据类型的列执行索引。
	
等效的Java类型	java.sql.Clob   <br/>
最大长度（也是默认长度）	2 GB - 1（或2,147,483,647字节）  <br/>
JDBC元数据类型	java.sql.Types.CLOB  <br/>
JDBC方法	ResultSet.getClob，PreparedStatement.setClob <br/>
<code>
CREATE TABLE json_store (id INT primary key, storedata JSON); 
–- insert JSON data as a string value delimited by single quotes
INSERT INTO json_data VALUES ( 1, 
'{ "store": {
    "book": [
      { "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      { "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99
      },
      { "category": "fiction",
        "author": "Herman Melville",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99
      },
      { "category": "fiction",
        "author": "J. R. R. Tolkien",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95
    }
  }
}' );
</code>
<br/>
另请参见JSON_EVALPATH中的示例。<br/>
###LONG VARCHAR
与VARCHAR相同，但不必指定最大长度，为32700。<br/>
	
等效的Java类型	java.lang.String<br/>
最大长度	32700<br/>
JDBC元数据类型	java.sql.Types.LONGVARCHAR<br/>
JDBC方法	ResultSet.getString，PreparedStatement.setString<br/>
用于位数据的长VARCHAR<br/>
与VARCHAR FOR BIT DATA相同，除了不需要指定最大长度为32700。<br/>
	
等效的Java类型	根据需要使用byte []或java.io.InputStream / java.lang.OutputStream<br/>
最大长度	32700字节<br/>
JDBC元数据类型	java.sql.Types.LONGVARBINARY<br/>
JDBC方法	ResultSet.getBytes，PreparedStatement.setBytes<br/>
NUMERIC<br/>
DECIMAL数据类型的同义词。<br/>
下面列出了DECIMAL的元数据差异。否则，NUMERIC的行为与DECIMAL相同。<br/>
	
JDBC元数据类型	java.sql.Types.NUMERIC<br/>
###NUMERIC [(precision [, scale ])]<br/>
###REAL<br/>
使用IEEE浮点表示法为数字提供4字节存储。<br/>
算术运算不会使其结果值为零。如果值太小，您将收到异常。常量总是映射到DOUBLE - 使用显式CAST将常量转换为REAL。<br/>
对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，将另一数字数据类型的列中的一个数字数据类型的值存储。<br/>
最大/最小限制与以下所述的java.lang.Float不同。<br/>
	
等效的Java类型	java.lang.Float<br/>
最小值	-3.402E + 38<br/>
最大值	3.402E + 38<br/>
最小正值	1.175E-37<br/>
最大的负值	-1.175E-37<br/>
JDBC元数据类型	java.sql.Types.REAL<br/>
JDBC方法	ResultSet.getFloat，PreparedStatement.setFloat<br/>
–- examples of valid values <br/>
values cast (233.31E3 as REAL); <br/>
values cast (8928E+06 as REAL); <br/>
-- this example will throw a range exception (SQLState: "42820") <br/>
values cast (123456789012345678901234567890123456789e0 as REAL);<br/>
###SMALLINT
为短整型值提供2字节存储。<br/>
对于表达式中其他类型的行为，请参阅表达式中的数字类型升级，并在另一数字数据类型的列中存储一个数字数据类型的值。<br/>
	
等效的Java类型	java.lang.Short<br/>
最小值	java.lang.Short.MIN_VALUE（-32768）<br/>
最大值	java.lang.Short.MAX_VALUE（32767）<br/>
JDBC元数据类型	java.sql.Types.SMALLINT<br/>
JDBC方法	ResultSet.getShort，PreparedStatement.setShort<br/>
时间TIME<br/>
提供以小时 - 分钟秒的时间存储。支持的格式有：<br/>
hh:mm[:ss] <br/>
hh.mm[.ss] <br/>
hh[:mm] {AM | PM} <br/>
小时（hh）可能有一个或两个数字，而分钟（mm）和秒（ss）（如果存在）必须有两位数字。DATE，TIME和TIMESTAMP不得在表达式中彼此混合，除了具有明确的CAST。<br/>
	
等效的Java类型	java.sql.Time<br/>
JDBC元数据类型	java.sql.Types.TIME<br/>
JDBC方法	ResultSet.getTime，PreparedStatement.setTime<br/>
VALUES '13:23:12' <br/>
VALUES TIME('13:23:12')<br/>
后一个例子使用内置函数和过程中描述的TIME（）函数。<br/>
###TIMESTAMP<br/>
提供DATE和TIME两者的组合值。此外，它允许具有最多六位数的小数秒。支持的格式有：<br/>
yyyy-MM-dd hh:mm:ss[.nnnnnn] <br/>
yyyy-MM-dd-hh.mm.ss[.nnnnnn] <br/>
年（yyyy）必须始终以四位数表示。月（MM），天（dd）和小时（hh）可能有一位或两位数字，而分钟（mm）和秒（ss）必须有两位数字。微秒（如果存在）可能有一到六位数。DATE，TIME和TIMESTAMP不得在表达式中彼此混合，除了具有明确的CAST。<br/>
	
等效的Java类型	java.sql.Timestamp<br/>
JDBC元数据类型	java.sql.Types.TIMESTAMP<br/>
JDBC方法	ResultSet.getTimestamp，PreparedStatement.setTimestamp<br/>
VALUES '2000-02-03 12:23:04' <br/>
VALUES TIMESTAMP(' 2000-02-03 12:23:04.827') <br/>
VALUES TIMESTAMP('2000-02-03 12:23:04')<br/>
后一个示例使用内置函数和过程中描述的TIMESTAMP（）函数。<br/>
用户定义的类型  User-Defined Types<br/>
RowStore允许您创建用户定义的类型。用户定义的类型是一个可序列化的Java类，其实例存储在列中。该类必须实现java.io.Serializable接口。为了有效的数据传输和存储，该类也应该实现java.io.Externalizable接口。如果该类实现了Apache Geode com.gemstone.gemfire.DataSerializable接口，那么它将被用于最佳的存储和对等传输。<br/>
有关创建和删除类型的信息，请参阅CREATE TYPE和DROP TYPE。<br/>
有关编写实现用户定义类型的Java类的信息，请参阅编程用户定义的类型。<br/>
###VARCHAR
提供长度最大限制的可变长度字符串。如果字符串值大于最大长度，则将修剪任何尾部空白以使其长度与最大长度相同，如果需要截断空格以外的字符，则会引发异常。在表达式中混合CHAR和VARCHAR时，较短的值用空格填充到更长的字符串长度。<br/>
字符串常量的类型是CHAR，而不是VARCHAR。要在字符串中表示单引号，请使用两个引号：<br/>
VALUES 'going to Chandra''s place' <br/>
VARCHAR的长度是无符号整数常数。<br/>
	
等效的Java类型	java.lang.String<br/>
最大长度	32672<br/>
JDBC元数据类型	java.sql.Types.VARCHAR<br/>
JDBC方法	ResultSet.getString，PreparedStatement.setString<br/>
{ VARCHAR | CHAR VARYING | CHARACTER VARYING }(length)<br/>
VARCHAR FOR BIT DATA<br/>
提供长度最大限制的可变长度字节串。当由于非结构化数据或其他原因而导致字符类型不合适时使用。对于CHAR FOR BIT DATA和VARCHAR FOR BIT DATA类型，根据SQL-92标准（这与某些DBMS的行为方式不同），不执行填充。涉及VITAR FOR BIT DATA和CHAR FOR BIT DATA的操作产生VARCHAR FOR BIT DATA类型的值。<br/>
字节字面值的类型始终是位数据的VARCHAR，而不是字节数据的CHAR。<br/>
长度是以字节为单位的值的长度的无符号整数常量。<br/>
	
等效的Java类型	根据需要使用byte []或java.io.InputStream / java.lang.OutputStream
<br/>
最大长度	32672字节<br/>
JDBC元数据类型	java.sql.Types.VARBINARY<br/>
JDBC方法	ResultSet.getBytes，PreparedStatement.setBytes<br/>
{ VARCHAR | CHAR VARYING | CHARACTER VARYING }[(length)] FOR BIT DATA <br/>
###XML
此类型可用于可扩展标记语言（XML）文档：<br/>
存储符合格式良好的XML（DOCUMENT（ANY））值的SQL / XML定义的XML文档<br/>
瞬态地为XML（SEQUENCE）值，可能不是格式正确的XML（DOCUMENT（ANY））值<br/>
注意： RowStore不支持SQL标准中的SQLXML类型，或者是SQLXML类型，而是将值作为普通字符串存储，并且用户必须根据需要明确地使用XMLSERIALIZE，XMLPARSE函数进行插入和查询。<br/>
对于执行XML相关功能的应用程序，应用程序必须使用内置JAXP / Xalan支持的JDK（例如Sun JDK5），或者必须为JAXP解析器和服务器类路径中的Xalan提供类（serializer.jar，xalan.jar ）。<br/>
要使用JDBC或ADO.NET驱动程序从RowStore数据库获取XML值，请在SQL查询中使用XMLSERIALIZE运算符。例如：
SELECT XMLSERIALIZE(doc as CLOB) FROM t1<br/>
然后使用与目标序列化类型相对应的ResultSet.getXXX方法检索XML值（例如，使用CLOB数据类型的上述示例中的getClob）。要使用JDBC将XML值存储到RowStore数据库中，请在SQL语句中使用XMLPARSE运算符。例如：<br/>
<code>
INSERT INTO t1(doc) VALUES XMLPARSE( DOCUMENT CAST (? AS CLOB) PRESERVE WHITESPACE)
</code>
然后使用与String类型兼容的任何PreparedStatement.setXXX方法（例如，在上述示例中使用
</br>
PreparedStatement.setString或PreparedStatement.setCharacterStream）。</br>
	
等效的Java类型	java.lang.String; 使用显式XML函数进行插入/查询，如上所述</br>
最大长度	java.lang.Integer.MAX_VALUE（2147483647）</br>
JDBC元数据类型	没有</br>
JDBC方法	根据目标序列化类型</br>
##Join

able-name [ INNER JOIN, [ LEFT OUTER JOIN, RIGHT ] OUTER ] JOIN table-name ON boolean-expression
<br/>
###INNER JOIN：指定具有explicit连接子句的两个表之间的连接。</br>
指定一个显式连接子句。</br>
句法</br>
<code>
TableExpression  [ INNER ] JOIN TableExpression  
{ ON boolean-expression }
</code>
<br/>
描述<br/>
另见查询功能和限制。<br/>
可以使用带有布尔表达式的ON来指定连接子句。ON子句中的表达式范围包括当前表和当前SELECT的外部查询块中的任何表。在以下示例中，ON子句引用当前表：<br/>
<code>
Select * from trade.securities INNER JOIN trade.portfolio 
     ON SECURITIES.sec_id = PORTFOLIO.sid and PORTFOLIO.tid = ?
</code>
<br/>
ON子句可以引用未连接的表，并且不必引用要连接的任何表（尽管通常是这样）。<br/>

例<br/>
<code>
-- Join the Customers and Portfolio tables<br/>
-- select all the columns from the customers table and<br/>
-- add the SID and QTY from the Portfolio table<br/>
-- to each row of the result<br/>
select C.* , F.sid, F.QTY from trade.customers  C JOIN trade.portfolio F ON C.cid = F.cid
</code><br/>
###LEFT OUTER JOIN：
<br/>
指定具有显式连接子句的两个表之间的连接，从第一个表保留不匹配的行。
<br/>
<code>
TableExpression  LEFT [ OUTER ] JOIN TableExpression <br/>
{
<br/>
ON boolean-expression<br/>
}
<br/>
</code>
描述<br/>
另见查询功能和限制。<br/>
ON子句中的表达式范围包括当前表和当前SELECT外部的查询块中的任何表。ON子句可以引用未连接的表，并且不必引用要连接的任何表（尽管通常是这样）。<br/>
例<br/>
<code>
--match all customers and their portfolio details, if any, having a specific<br/> 
--transaction ID<br/>
select * from trade.customers C LEFT OUTER JOIN trade.portfolio F on C.cid= F.cid where F.tid = ? <br/>
-- use the synonymous syntax, LEFT JOIN, to achieve exactly<br/>
-- the same results as in the example above<br/>
select * from trade.customers C LEFT  JOIN trade.portfolio F on C.cid= F.cid where F.tid = ?<br/>
</code><br/>
###RIGHT OUTER JOIN：指定具有显式连接子句的两个表之间的连接，从第二个表中保留不匹配的行。<br/>
在所有情况下，您可以在外连接子句或WHERE子句中连接的一个或两个表上指定其他限制。<br/>
指定一个JOIN子句，用于保留第二个（右）表中不匹配的行，并将其与第一个（左）表的形状连接。LEFT OUTER JOIN B等效于BRIGHT OUTER JOIN A，列的顺序不同。<br/>
###句法<br/>
<code>
TableExpression  RIGHT [ OUTER ] JOIN TableExpression <br/>
{<br/>
ON boolean-expression<br/>
}<br/>
</code><br/>
###描述<br/>
另见查询功能和限制。<br/>
ON子句中的表达式范围包括当前表和当前SELECT外部的查询块中的任何表。ON子句可以引用未连接的表，并且不必引用要连接的任何表（尽管通常是这样）。<br/>
注意： RowStore对受支持的加入查询类型施加限制。请参阅查询限制 - 连接，子查询，聚合。<br/>
例<br/>
--match all customers and their portfolio details, if any, having a specific <br/>
--transaction ID<br/>
select * from trade.portfolio F RIGHT OUTER JOIN trade.customers C on C.cid= F.cid where F.tid = ? <br/>
-- use the synonymous syntax, RIGHT JOIN, to achieve exactly<br/>
-- the same results as in the example above<br/>
select * from trade.portfolio F RIGHT JOIN trade.customers C on C.cid= F.cid where F.tid = ? <br/>
-- a TableExpression can be a joinOperation. Therefore<br/>
-- you can have multiple join operations in a FROM clause<br/>
-- List all the customers, there portfolio details, if any, and their sell <br/>
-- orders, if any<br/>
select * from trade.SELLORDERS SO RIGHT OUTER JOIN TRADE.PORTFOLIO F RIGHT OUTER JOIN TRADE.CUSTOMERS  c on f.cid = so.cid on c.cid= f.cid where f.tid = ?<br/>
###RIGHT OUTER JOIN：指定具有显式连接子句的两个表之间的连接，从第二个表中保留不匹配的行。<br/>
指定一个JOIN子句，用于保留第二个（右）表中不匹配的行，并将其与第一个（左）表的形状连接。LEFT OUTER JOIN B等效于BRIGHT OUTER JOIN A，列的顺序不同。<br/>
###句法
TableExpression  RIGHT [ OUTER ] JOIN TableExpression <br/>
{<br/>
ON boolean-expression<br/>
}<br/>
###描述
另见查询功能和限制。<br/>
ON子句中的表达式范围包括当前表和当前SELECT外部的查询块中的任何表。ON子句可以引用未连接的表，并且不必引用要连接的任何表（尽管通常是这样）。<br/>
注意： RowStore对受支持的加入查询类型施加限制。请参阅查询限制 - 连接，子查询，聚合。<br/>
例<br/>
<code>
--match all customers and their portfolio details, if any, having a specific 
--transaction ID<br/>
select * from trade.portfolio F RIGHT OUTER JOIN trade.customers C on C.cid= F.cid where F.tid = ? <br/>
-- use the synonymous syntax, RIGHT JOIN, to achieve exactly<br/>
-- the same results as in the example above<br/>
select * from trade.portfolio F RIGHT JOIN trade.customers C on C.cid= F.cid where F.tid = ? <br/>
-- a TableExpression can be a joinOperation. Therefore<br/>
-- you can have multiple join operations in a FROM clause<br/>
-- List all the customers, there portfolio details, if any, and their sell <br/>
-- orders, if any<br/>
select * from trade.SELLORDERS SO RIGHT OUTER JOIN TRADE.PORTFOLIO F RIGHT OUTER JOIN TRADE.CUSTOMERS  c on f.cid = so.cid on c.cid= f.cid where f.tid = ?<br/>

</code>

##核心JDBC类，接口和方法
###java.sql.BatchUpdateException类
###java.sql.Connection接口
###java.sql.DatabaseMetaData接口
###java.sql.Driver接口
###java.sql.DriverManager.getConnection方法
###java.sql.PreparedStatement接口
###java.sql.ResultSet接口
###java.sql.SavePoint类
###类java.sql.SQLException
###java.sql.Statement类
###javax.sql.XADataSource
##函数

###内置函数是SQL关键字或特殊操作符执行操作的表达式。内置功能使用关键字或特殊的内置操作符。
   ###1 聚合（集合函数）
Function Name	Permitted Data Types<br/>
COUNT	All types<br/>
MIN	Numeric built-in data types<br/>
MAX	Numeric built-in data types<br/>
AVG	Numeric built-in data types<br/>
SUM	Numeric built-in data types<br/>
###A SelectItem in a SelectExpression.
###A HAVING.
###An ORDER BY (using an alias name)， 如果总出现在相关查询块的结果。也就是说，只有当聚合出现在SelectExpression中的SelectItem中时，才允许在ORDER BY中使用聚合的别名。
-- not valid<br/>
SELECT MIN(flying_time), flight_id<br/>
FROM Flights<br/>
外部引用（相关性）不允许使用聚合。这意味着如果子查询包含聚合，则该聚合不能评估包含对外部查询块中的列的引用的表达式。例如，以下查询无效，因为SUM对外部查询的列进行操作：<br/>
<code>
SELECT c1<br/>
FROM t1<br/>
GROUP BY c1<br/>
HAVING c2 ><br/>
    (SELECT t2.x<br/>
    FROM t2<br/>
    WHERE t2.y = SUM(t1.c3)) <br/>
</code>
###ACOS
       ACOS函数返回指定数字的反余弦值。<br/>
      指定的数字是您想要的角度的余弦（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
         如果指定的数字为NULL，则此函数的结果为NULL。<br/>
         如果指定数字的绝对值大于1，则返回一个异常，表示该值超出范围（SQL状态22003）。<br/>
返回的值（以弧度表示）在0（0）到pi的范围内。返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
句法<br/>
###ACOS ( number )<br/>

 ###   ASIN
 ASIN函数返回指定数字的正弦。<br/>
   指定的数字是您想要的角度的正弦，以弧度表示。指定的数字必须是DOUBLE PRECISION号码。<br/>
如果指定的数字为NULL，则此函数的结果为NULL。<br/>
如果指定的数字为零（0），则该函数的结果为零，与指定的数字相同。<br/>
如果指定数字的绝对值大于1，则返回一个异常，表示该值超出范围（SQL状态22003）。<br/>
返回的值（以弧度表示）在-pi / 2到pi / 2的范围内。返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
###句法
###ASIN ( number )<br/>

   ATAN ATAN函数返回指定数字的反正切。<br/>
      指定的数字是您想要的角度的切线（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
如果指定的数字为NULL，则此函数的结果为NULL。<br/>
如果指定的数字为零（0），则该函数的结果为零，与指定的数字相同。<br/>
返回的值（以弧度表示）在-pi / 2到pi / 2的范围内。返回值的数据类型为DOUBLE PRECISION（双精度）编号。
###句法
###ATAN ( number )
###ATAN2
          ATAN2函数返回两个参数的商的反正切（弧度）。<br/>
     ATAN2函数返回两个参数的商的反正切（弧度）。<br/>
如果成功完成，该函数将返回的反正切ÿ / X的范围内- PI到PI弧度，其中ÿ是第一个参数，X是第二个参数。指定的数字必须是DOUBLE PRECISION数字。<br/>
如果任一参数为NULL，则该函数的结果为NULL。<br/>
如果第一个参数为零，第二个参数为正，则该函数的结果为零。<br/>
如果第一个参数为零，第二个参数为负，则函数的结果为最接近pi的双值。<br/>
如果第一个参数为正，第二个参数为零，则结果为最接近pi / 2 的双值。<br/>
如果第一个参数为负，第二个参数为零，则结果为最接近-pi / 2 的双精度值。<br/>
返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
句法<br/>
ATAN2 ( y, x )<br/>

###AVG
AVG是一个聚合函数，用于评估一组表达式的平均值。仅允许对求值为数字数据类型的表达式使用AVG。<br/>
###句法
###AVG ( [ DISTINCT | ALL ] Expression )
DISTINCT限定符消除了重复。ALL限定符保留重复。如果未指定ALL或DISTINCT，则ALL为默认值。例如，如果列包含值1.0,1.0,1.0,1.0和2.0，则AVG（col）返回比AVG（DISTINCT col）更小的值。<br/>
每个SelectExpression只允许一个DISTINCT聚合表达式。例如，以下查询无效：<br/>
<code>
SELECT AVG (DISTINCT flying_time), SUM (DISTINCT miles)<br/>
FROM Flights
</code>
该表达式可以包含多个列引用或表达式，但不能包含另一个聚合或子查询。它必须评估为SQL-92数字数据类型。因此，您可以调用评估SQL-92数据类型的方法。如果一个表达式计算结果为NULL，则聚合跳过该值。<br/>
生成的数据类型与它所运行的表达式相同（它将永远不会溢出）。以下查询例如返回INTEGER 1，这可能不是你所期望的：<br/>
<code>
SELECT AVG(c1)<br/>
FROM (VALUES (1), (1), (1), (1), (2)) AS myTable (c1)<br/></code>
如果要更精确地将表达式转换为其他数据类型：<br/>
<code>
SELECT AVG(CAST (c1 AS DOUBLE PRECISION))<br/>
FROM (VALUES (1), (1), (1), (1), (2)) AS myTable (c1)<br/>
</code>
###BIGINT
###CAST CAST函数将值从一种数据类型转换为另一种数据类型，并为动态参数（？）或NULL值提供数据类型。
###CEIL或CEILING CEIL和CEILING按照指定的数字运算，并返回大于或等于指定数字的最小数字。
###CHAR CHAR函数返回固定长度的字符串表示形式。
表示方式为： - 如果第一个参数是任何类型的字符串，则为字符串。 - datetime值，如果第一个参数是日期，时间或时间戳。 - 十进制数，如果第一个参数是十进制数。 - 双精度浮点数，如果第一个参数是DOUBLE或REAL。 - 一个整数，如果第一个参数是SMALLINT，INTEGER或BIGINT。<br/>
第一个参数必须是内置的数据类型。CHAR函数的结果是固定长度的字符串。如果第一个参数可以为null，则结果可以为null。如果第一个参数为空，则结果为空值。第一个参数不能是XML值。要将XML值转换为指定长度的CHAR，必须使用SQL / XML序列化运算符XMLSERIALIZE。<br/>
字符到字符语法<br/>
CHAR (CharacterExpression [, integer] ) <br/>
CharacterExpression <br/>
返回值为CHAR，VARCHAR，LONG VARCHAR或CLOB数据类型的表达式。<br/>
integer <br/>
所得到的固定长度字符串的length属性。该值必须介于0和254之间。<br/>
如果字符表达式的长度小于结果的长度属性，则结果将以空格填充直到结果的长度。如果字符表达式的长度大于结果的长度属性，则执行截断。除非截断的字符全部为空，并且字符表达式不是长字符串（LONG VARCHAR或CLOB），否则返回警告。<br/>
##整数到字符语法
##CHAR (IntegerExpression ) <br/>
###IntegerExpression 
返回一个整数数据类型（SMALLINT，INTEGER或BIGINT）的值的表达式。<br/>
结果是以整数常量形式表示参数的字符串。结果由n个字符组成，如果参数为负，则表示具有前面减号的参数值的有效数字。这是对的。<br/>
如果第一个参数是一个小整数：结果的长度为6.如果结果中的字符数小于6，则结果将填充在右侧，空格为6。<br/>
如果第一个参数是一个大整数：结果的长度为11.如果结果中的字符数小于11，则结果将填充在右侧，空白长度为11。<br/>
如果第一个参数是一个大整数：结果的长度为20.如果结果中的字符数小于20，则结果将在右侧填充，长度为20。
日期时间到字符语法<br/>
###CHAR (DatetimeExpression ) 
DatetimeExpression <br/>
一个表达式，它是以下三种数据类型之一：<br/>
日期：结果是日期的字符表示。结果的长度为10。<br/>
时间：结果是时间的字符表示。结果的长度为8。<br/>
timestamp：结果是时间戳的字符串表示形式。结果的长度是26。<br/>
Decimal to character小数字符<br/>
###CHAR (DecimalExpression ) 
###DecimalExpression 
返回一个十进制数据类型的值的表达式。如果需要不同的精度和尺度，则可以先使用DECIMAL标量函数进行更改。
浮点字符语法<br/>
###CHAR (FloatingPointExpression ) 
FloatingPointExpression <br/>
返回值为浮点数据类型（DOUBLE或REAL）的表达式。<br/>
例<br/>
使用CHAR函数将EDLEVEL（定义为smallint）的值作为固定长度的字符串返回：<br/>
<code>
SELECT CHAR(EDLEVEL) FROM EMPLOYEE
</code>
<br/>
18的EDLEVEL将作为CHAR（6）值'18'返回（18后跟四个空格）<br/>

###COALESCE 
       COALESCE函数使用两个或多个兼容的参数，并返回不为null的第一个参数。仅当所有参数为空时，结果才为null。<br/>
如果函数调用的所有参数都是动态的，则会发生错误。<br/>
注意： COALESCE的同义词是VALUE。VALUE被RowStore接受，但不能被SQL标准识别。<br/>
句法<br/>
COALESCE ( expression, expression [, expression]* )<br/>
该函数必须至少有两个参数。<br/>
###例
<code>
ij> -- create table with three different integer types<br/>
ij> create table temp(smallintcol smallint, bigintcol bigint, intcol integer);<br/>
0 rows inserted/updated/deleted<br/>
ij> insert into temp values (1, null, null);<br/>
1 row inserted/updated/deleted<br/>
ij> insert into temp values (null, 2, null);<br/>
1 row inserted/updated/deleted<br/>
ij> insert into temp values (null, null, 3);<br/>
1 row inserted/updated/deleted<br/>
ij> select * from temp;<br/>
SMALL&|BIGINTCOL           |INTCOL     <br/>
---------------------------------------<br/>
1     |NULL                |NULL       <br/>
NULL  |2                   |NULL       <br/>
NULL  |NULL                |3          <br/>
3 rows selected<br/>
ij> -- the return data type of coalesce is bigint<br/>
ij> select coalesce (smallintcol, bigintcol) from temp;<br/>
1                   <br/>
--------------------<br/>
1                   <br/>
2                   <br/>
NULL                <br/>
3 rows selected<br/>
ij> -- the return data type of coalesce is bigint<br/>
ij> select coalesce (smallintcol, bigintcol, intcol) from temp;<br/>
1<br/>
--------------------<br/>
1<br/>
2<br/>
3 <br/>
3 rows selected<br/>
ij> -- the return data type of coalesce is integer<br/>
ij> select coalesce (smallintcol, intcol) from temp;<br/>
1          <br/>
-----------<br/>
1          <br/>
NULL       <br/>
3          <br/>
3 rows selected<br/>

</code>

###Concatenation operator
         连接运算符<br/>
级联运算符||将其右操作数连接到其左操作数的末尾。它以字符或位表达式操作。<br/>
因为所有内置数据类型都被隐式转换为字符串，所以该函数可以对所有内置的数据类型执行操作。<br/>
句法<br/>
<code>
{
<br/>
    { CharacterExpression || CharacterExpression } |
<br/>
    { BitExpression || BitExpression }
<br/>
}
</code>
对于字符串，如果左和右操作数都是CHAR类型，则生成的类型为CHAR; 否则，它是VARCHAR。CHAR和VARCHAR的正常空白填充/修剪规则适用于此运算符的结果。<br/>
所得到的字符串的长度是两个操作数的长度之和。<br/>
对于位串，如果左和右操作数都为CHAR FOR BIT DATA类型，则生成的类型为CHAR FOR BIT DATA; 否则，它是VARCHAR FOR BIT DATA。<br/>
###例子
-- returns 'supercalifragilisticexbealidocious(sp?)'
<br/>
VALUES 'supercalifragilistic' || 'exbealidocious' || '(sp?)'
<br/>
-- returns NULL
<br/>
VALUES CAST (null AS VARCHAR(7))|| 'AString'
<br/>
-- returns '130asdf'<br/>

VALUES '130' || 'asdf'<br/>

###COS COS函数返回指定数字的余弦值。
<br/>
           指定的数字是您想要余弦的角度（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
           如果指定的数字为NULL，则此函数的结果为NULL。<br/>
     句法<br/>
           COS ( number )<br/>

###COSH COSH函数返回指定数字的双曲余弦值。
###COSH函数返回指定数字的双曲余弦值。
<br/>
指定的数字是您想要双曲余弦的角度（弧度）。指定的数字必须是DOUBLE PRECISION号码。
<br/>
如果指定的数字为NULL，则此函数的结果为NULL。
<br/>
如果指定的数字为零（0），则该函数的结果为1（1.0）。<br/>
句法
COSH ( number )<br/>

###COT 
COT功能返回指定数字的cotangens。<br/>
指定的数字是您想要cotangens的角度（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
如果指定的数字为NULL，则此函数的结果为NULL。<br/>
###句法
###COT ( number )

###COUNT
###COUNT是一个聚合函数，用于计算表达式中访问的行数（请参阅聚合（集合函数））。允许在所有类型的表达式上使用COUNT。
###句法
###COUNT ( [ DISTINCT | ALL ] Expression ) 
###DISTINCT限定符消除了重复。ALL限定符保留重复。如果既不指定ALL也不指定DISTINCT，则假定为ALL。例如，如果列包含值1,1,1和2，则COUNT（col）返回的值大于COUNT（DISTINCT col）。
###每个SelectExpression只允许一个DISTINCT聚合表达式。例如，不允许以下查询：
<code>
-- query not allowed<br/>
SELECT COUNT (DISTINCT flying_time), SUM (DISTINCT miles)<br/>
FROM Flights<br/>
</code>
一个表达式可以包含多个列引用或表达式，但它不能包含另一个聚合或子查询。如果一个表达式的计算结果为NULL，聚集体不被处理该值。<br/>
生成的COUNT数据类型为INTEGER。<br/>
例<br/>
<code/>
-- Count the number of countries in each region,
<br/>
-- show only regions that have at least 2
<br/>
SELECT COUNT (country), region
<br/>
FROM Countries
<br/>
GROUP BY region<br/>
HAVING COUNT (country) > 1<br/>
</code>
###COUNT(*)

COUNT（*）是一个聚合函数，用于计算访问的行数。没有消除NULL或重复。COUNT（*）不对表达式运行。
句法<br/>
COUNT(*)<br/>
结果数据类型为INTEGER。<br/>
###例
-- Count the number of rows in the Flights table<br/>
SELECT COUNT(*)<br/>
FROM Flights<br/>

###COUNT_ESTIMATE  An aggregate function that returns the estimated total number of rows in an HDFS read/write table (rows in operational memory as well as rows persisted to HDFS).<br/>
   一个聚合函数，返回HDFS读/写表中的估计总行数（操作内存中的行以及持续到HDFS的行）。<br/>
COUNT_ESTIMATE不对表达式操作，但要求您指定完全限定的表名。请注意，报告的行数可能与实际行数不同，误差幅度大约为2％。<br/>
您可以使用COUNT（*）并包含queryHDFS=true查询提示来获取HDFS表中的行的精确计数。然而，该方法在HDFS中遍历整个表，并且可能需要很长时间才能完成，具体取决于表的大小。<br/>
###句法
COUNT_ESTIMATE('schemaname.tablename')<br/>
结果数据类型为INTEGER。<br/>
###例
对于HDFS读/写表，使用COUNT（*）的FLIGHTAVAILABILITY仅返回表的内存，运行数据的结果：<br/>
<code>
snappy> select count(*) as operational from flightavailability;<br/>
OPERATIONAL          <br/>
-----------<br/>
400    <br/>
1 row selected<br/>
</code><br/>
使用COUNT_ESTIMATE提供HDFS持久行的估计：<br/>
<code>
snappy> select count(*) as operational, count_estimate('app.flightavailability') as 
<br/>
estimate from flightavailability;<br/>
OPERATIONAL|ESTIMATE            <br/>
--------------------------------<br/>
400        |513                 <br/>
1 row selected<br/>
</code><br/>
使用COUNT（*）queryHDFS=true提供HDFS持久性的准确计数，但可能需要额外的时间来迭代表的数据：<br/>
snappy> select count(*) as actual, count_estimate('app.flightavailability') as estimate from flightavailability --GEMFIREXD-PROPERTIES queryHDFS=true \n ;<br/>
ACTUAL     |ESTIMATE            <br/>
--------------------------------<br/>
518        |513                 <br/>
1 row selected<br/>

###CURRENT DATE

###CURRENT_DATE
###CURRENT ISOLATION
   CURRENT ISOLATION将当前隔离级别返回为“”（空白），“UR”，“CS”，“RS”或“RR”的char（2）值。<br/>
###句法
<br/>
CURRENT ISOLATION<br/>
###例
<code>
VALUES CURRENT ISOLATION

</code>
<br/>
###CURRENT_ROLE
###CURRENT SCHEMA
###CURRENT TIME
###CURRENT_TIME
###CURRENT TIMESTAMP
###CURRENT_TIMESTAMP
###CURRENT_USER
###DATE 
DATE函数从值返回一个日期。<br/>
参数必须是日期，时间戳，正数小于或等于2,932,897，日期或时间戳的有效字符串表示形式，或长度为7的不是CLOB，LONG VARCHAR或XML值的字符串。如果参数是长度为7的字符串，则它必须以yyyynnn的形式表示有效的日期，其中yyyy是表示一年的数字，nnn是001和366之间的数字，表示该年的某一天。功能的结果是一个日期。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
其他规则取决于指定的参数的数据类型：<br/>
  如果参数是日期，时间戳或日期或时间戳的有效字符串表示形式：结果是值的日期部分。<br/>
  如果参数是一个数字：结果是1970年1月1日以后n-1天的日期，其中n是数字的组成部分。<br/>
  如果参数是长度为7的字符串：结果是由字符串表示的日期。<br/>
###句法
DATE ( expression )<br/>
###例子
这个例子导致'1988-12-25'的内部表示。<br/>
VALUES DATE('1988-12-25')<br/>
此示例导致'1972-02-28'的内部表示。<br/>
VALUES DATE(789)<br/>

###DAY DAY函数返回值的日期部分。

参数必须是日期，时间戳，或不是CLOB，LONG VARCHAR或XML值的日期或时间戳的有效字符串表示形式。函数的结果是1到31之间的整数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
<code>
句法
DAY ( expression )<br/>
</code>
###例
<code>
values day('2007-08-02');
</code>
<br/>
结果值为2。<br/>

###DEGREES DEGREES函数将指定的数字从弧度转换为度。
###DOUBLE
DOUBLE函数返回一个与a相对应的浮点数： - number，如果参数是数值表达式。 - 如果参数是字符串表达式，则数字的字符串表示形式。<br/>
数字加倍 Numeric to double<br/>
DOUBLE [PRECISION] (NumericExpression ) <br/>
NumericExpression <br/>
参数是一个返回任何内置数值数据类型的值的表达式。<br/>
该功能的结果是双精度浮点数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。如果将参数分配给双精度浮点列或变量，则结果将发生相同的数字。<br/>
字符串加倍 Character string to double<br/>
DOUBLE (StringExpression ) <br/>
StringExpression <br/>
参数可以是数字常量形式的CHAR或VARCHAR类型。参数中的前导和尾随空格将被忽略。<br/>
该功能的结果是双精度浮点数。结果可以为null; 如果参数为null，则结果为空值。如果该字符串被认为是一个常量并分配给一个双精度浮点列或变量，结果将会发生相同的数字。<br/>

###DSID 返回RowStore集群中唯一分布式成员进程标识的字符串形式。<br/>
###EXP EXP函数将e提高到指定数量的幂。<br/>
###FLOOR FLOOR函数将指定的数字向下舍入，并返回小于或等于指定数字的最大数字。<br/>
###SYS.GET_CRITICAL_HEAP_PERCENTAGE 返回在LowMemoryExceptionRowStore数据存储上触发的Java堆内存使用的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。<br/>
###SYS.GET_CRITICAL_OFFHEAP_PERCENTAGE 返回在LowMemoryExceptionRowStore数据存储上触发s的非堆内存使用率的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。<br/>
###SYS.GET_EVICTION_HEAP_PERCENTAGE 返回Java堆内存使用率的百分比阈值，这些阈值触发了RowStore数据存储，以便在为LRU_HEAP驱逐配置的表上执行LRU驱逐。此过程仅在本地RowStore数据存储成员上执行。<br/>
###SYS.GET_EVICTION_OFFHEAP_PERCENTAGE 返回触发RowStore数据存储对执行LRU_HEAP逐出配置的表执行LRU驱逐的非堆内存使用的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。<br/>
###SYS.GET_TABLE_VERSION 显示表的数据字典版本。在WAN部署中使用此过程来验证表的版本与另一个RowStore分布式系统中的相同表匹配。<br/>
###GROUPS 以按排序顺序的逗号分隔列表返回当前分布式成员的服务器组。<br/>
###GROUPSINTERSECT 返回一个布尔值结果。此功能是GROUPSINTERSECTION功能的变体。<br/>
###GROUPSINTERSECTION 获取两个逗号分隔的服务器组列表。零长度字符串表示空服务器组。NULL表示默认服务器组，其中包括所有数据存储。<br/>
###GROUPSUNION 返回两个逗号分隔的服务器组排序列表的联合。零长度字符串表示空服务器组。NULL表示默认服务器组，其中包括所有数据存储。<br/>
###SYS.HDFS_LAST_MAJOR_COMPACTION 返回HDFS读/写表最后完成的主压缩周期的开始时间。
###HOUR HOUR函数返回值的小时部分。
###INTEGER
INTEGER函数以整型常量的形式返回数字或字符串的整数表示形式。<br/>
###句法
INT[EGER] (NumericExpression | CharacterExpression ) <br/>
NumericExpression <br/>
表达式返回任何内置数值数据类型的值。如果参数是数字表达式，则该参数与参数分配给一个大的整数列或变量时相同。如果参数的整个部分不在整数范围内，则会发生错误。如果存在，参数的小数部分将被截断。<br/>
CharacterExpression <br/>
返回长度不大于字符常量的最大长度的字符串值的表达式。前导和尾随空白被消除，结果字符串必须符合形成SQL整数常量的规则。字符串不能是长字符串。如果参数是一个字符表达式，则结果是相同的数字，如果相应的整型常量被分配给一个大的整数列或变量，将会发生。<br/>
函数的结果是一个大的整数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
例<br/>
使用EMPLOYEE表，选择一个包含薪水（SALARY）除以受教育水平（EDLEVEL）的列表。截断计算中的任何十进制。列表还应包含计算中使用的值和员工号（EMPNO）。列表应按照计算值的降序排列：<br/>
<code>
SELECT INTEGER (SALARY / EDLEVEL), SALARY, EDLEVEL, EMPNO<br/>
FROM EMPLOYEE<br/>
ORDER BY 1 DESC<br/>
</code><br/>

###JSON_EVALPATH 将JSON路径表达式应用于JSON列以从JSON列返回属性值。
###LCASE或LOWER
###LENGTH
LENGTH应用于字符串表达式或位字符串表达式，并返回结果中的字符数。<br/>
因为所有内置数据类型都被隐式转换为字符串，所以该函数可以对所有内置的数据类型执行操作。<br/>
###句法
<code>
LENGTH ( { CharacterExpression | BitExpression } )<br/>
###例子
-- returns 20<br/>
VALUES LENGTH('supercalifragilistic')<br/>
-- returns 1<br/>
VALUES LENGTH(X'FF')<br/>
-- returns 4<br/>
VALUES LENGTH(1234567890)<br/>
</code>
LN或LOG LN和LOG函数返回指定数字的自然对数（基数e）。<br/>
###LOG10 LOG10函数返回指定数字的10位对数。<br/>
###LOCATE<br/>
LOCATE函数用于搜索另一个字符串中的字符串。如果找到所需的字符串，LOCATE返回找到它的索引。如果找不到所需的字符串，则LOCATE返回0。
###句法
LOCATE(CharacterExpression, CharacterExpression [, StartPosition] ) <br/>
LOCATE函数有两个必需的参数，第三个可选参数。<br/>
第一CharacterExpression指定要搜索的字符串进行。<br/>
第二CharacterExpression指定字符串中进行搜索。<br/>
第三个参数是startPosition，并指定搜索开始的第二个参数中的位置。如果没有提供第三个参数，LOCATE函数将在第二个参数的开始处开始搜索。<br/>
LOCATE的返回类型是一个整数。LOCATE函数返回一个整数，表示第一个参数首次定位的第二个参数中的索引位置。索引位置以1.开头。如果第二个参数中没有找到第一个参数，则LOCATE返回0.如果第一个参数为空字符串（“），则LOCATE返回第三个参数的值（如果未提供，则返回1） ），即使第二个参数也是一个空字符串。如果为CharacterExpression参数传递NULL值，则返回NULL。<br/>
###例子
<code
-- returns 2, since 'love' is found at index position 2:
<br/>
VALUES LOCATE('love', 'clover')
<br/>
-- returns 0, since 'stove' is not found in 'clover':
<br/>
VALUES LOCATE('stove', 'clover')
<br/>
-- returns 5 (note the start position is 4):
<br/>
VALUES LOCATE('iss', 'Mississippi', 4)
<br/>
-- returns 1, because the empty string is a special case:
<br/>
VALUES LOCATE('', 'ABC')
<br/>
-- returns 0, because 'AAA' is not found in '':
<br/>
VALUES LOCATE('AAA', '')
<br/>
-- returns 3
<br/>
VALUES LOCATE('', '', 3)
<br/>

###LTRIM



###MAX
MAX是一个聚合函数，用于计算一组表达式的最大值。MAX仅允许对评估内置数据类型的表达式（包括CHAR，VARCHAR，DATE，TIME，BIT数据的CHAR等）。<br/>
###句法
<code>
MAX ( [ DISTINCT | ALL ] Expression )
<br/>
</code>
DISTINCT和ALL限定符消除或保留重复项，但这些限定符对MAX表达式没有影响。每个SelectExpression只允许一个DISTINCT聚合表达式。例如，不允许以下查询：<br/>
<code>
SELECT COUNT (DISTINCT flying_time), MAX (DISTINCT miles)
FROM Flights
</code>
<br/>
该表达式可以包含多个列引用或表达式，但它不能包含另一个聚合或子查询。它必须评估为内置的数据类型。因此，您可以调用评估内置数据类型的方法。（例如，返回java.lang.Integer或int的方法计算结果为一个INTEGER。）如果一个表达式计算结果为NULL，则聚合跳过该值。<br/>
类型的比较规则确定最大值。对于CHAR和VARCHAR，值结尾处的空格数可能会影响MAX的评估方式。例如，如果值“z”和“z”都存储在列中，则无法控制哪个值将返回为最大值，因为字符比较的空格被忽略。<br/>
生成的数据类型与它所运行的表达式相同（它将永远不会溢出）。<br/>
###例子
<code>
-- find the latest date in the FlightAvailability table
<br/>
SELECT MAX (flight_date) FROM FlightAvailability
<br/>
-- find the longest flight originating from each airport,<br/>
-- but only when the longest flight is over 10 hours<br/>

SELECT MAX(flying_time), orig_airport
<br/>
FROM Flights
<br/>
GROUP BY orig_airport
<br/>
HAVING MAX(flying_time) > 10
<br/>
</code>
###MIN
MIN是一个聚合函数，用于计算一组表达式的最小值。MIN仅允许对评估内置数据类型（包括CHAR，VARCHAR，DATE，TIME等）的表达式进行评估。<br/>
###句法
<code>
MIN ( [ DISTINCT | ALL ] Expression )
</code>
<br/>
DISTINCT和ALL限定符消除或保留重复项，但这些限定符在MIN表达式中没有影响。每个SelectExpression只允许一个DISTINCT聚合表达式。例如，不允许以下查询：<br/>
<code>
SELECT COUNT (DISTINCT flying_time), MIN (DISTINCT miles)
<br/>
FROM Flights
</code>
<br/>
该表达式可以包含多个列引用或表达式，但它不能包含另一个聚合或子查询。它必须评估为内置的数据类型。因此，您可以调用评估内置数据类型的方法。（例如，返回java.lang.Integer或int的方法计算结果为一个INTEGER。）如果一个表达式计算结果为NULL，则聚合跳过该值。<br/>
该类型的比较规则确定最小值。对于CHAR和VARCHAR，值结尾处的空格数可能会影响MIN的评估方式。例如，如果值“z”和“z”都存储在列中，则无法控制将哪个值作为最小值返回，因为字符比较的空格被忽略。<br/>
生成的数据类型与它所运行的表达式相同（它将永远不会溢出）。<br/>
###例子
<code>
-- NOT valid:<br/>
SELECT DISTINCT flying_time, MIN(DISTINCT miles) from Flights
<br/>
-- valid:<br/>
SELECT COUNT(DISTINCT flying_time), MIN(DISTINCT miles) from Flights
<br/>
-- find the earliest date:<br/>
SELECT MIN (flight_date) FROM FlightAvailability;
<br/>
</code>
 ###MINUTE   MINUTE函数返回值的小部分。
参数必须是不是CLOB，LONG VARCHAR或XML值的时间或时间戳的时间，时间戳或有效字符串表示形式。函数的结果是0到59之间的整数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
###句法
<code>
MINUTE ( expression )
</code>
<br/>
###例
选择“出发时间”在6:00至6:30之间的“航班”表格中的所有行：<br/>
<code>

SELECT * FROM flights 
<br/>
   WHERE HOUR(departure_time) = 6 and MINUTE(departure_time) < 31;
<br/>
</code>

###MOD
MOD返回参数1的余数（模数）除以参数2.只有当参数1为负数时，结果为负。<br/>
###句法
mod(integer_type, integer_type) 
<br/>
功能的结果是：
<br/>
###如果两个参数均为SMALLINT，则为SMALLINT。
###INTEGER如果一个参数为INTEGER，另一个为INTEGER或SMALLINT。
###BIGINT如果一个整数为BIGINT，另一个参数为BIGINT，INTEGER或SMALLINT。
结果可以为null; 如果任何参数为空，则结果为空值。<br/>
###MONTH
 MONTH函数返回值的月份部分。<br/>
参数必须是日期，时间戳，或不是CLOB，LONG VARCHAR或XML值的日期或时间戳的有效字符串表示形式。函数的结果是1到12之间的整数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
###句法
MONTH ( expression )<br/>
###例
在十二月份为出生人士（BIRTHDATE）选择EMPLOYEE表中的所有行。<br/>
<code>
SELECT * FROM EMPLOYEE WHERE MONTH(BIRTHDATE) = 12
</code>
<br/>
###PI
PI函数返回一个比pi更接近的值。<br/>
常数pi是圆的圆周与圆的直径的比率。<br/>
返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
###句法
<code>PI (  )</code>
<br/>

###RADIANS    RADIANS函数将指定的数字从度转换为弧度。
指定的数字是以度数测量的角度，其转换为以弧度测量的大致相等的角度。指定的数字必须是DOUBLE PRECISION号码。<br/>
注意：从度数到弧度的转换并不准确。<br/>
返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
###句法
<code>
RADIANS ( number )
</code>
 ###  RANDOM RANDOM函数返回随机数。RANDOM函数返回带有正号，大于或等于零（0）和小于一（1.0）的双精度数字。
###句法
<code>RANDOM()</code>
<br/>
###RAND 
   RAND函数返回给定种子号的随机数<br/>
   给定一个INTEGER种子号， RAND函数返回一个带有正号，大于或等于零（0）和小于一（1.0）的DOUBLE PRECISION号。<br/>
###句法
<code>RAND( seed )  </code>
<br/>

###RTRIM
RTRIM从字符串表达式的末尾删除空格。<br/>
###句法
<code>
RTRIM(CharacterExpression)
</code>
<br/>
CharacterExpression是CHAR，VARCHAR或LONG VARCHAR数据类型，任何内置的类型被隐式转换为字符串。
如果CharacterExpression求值为空，则RTRIM返回NULL 。<br/>
###例子
<code>
-- returns ' asdf'<br/>
VALUES RTRIM(' asdf  ')
<br/>
-- returns 'asdf'<br/>
VALUES RTRIM('asdf  ')
<br/>
</code>
###SECOND 
SECOND函数返回值的秒部分。<br/>
参数必须是不是CLOB，LONG VARCHAR或XML值的时间或时间戳的时间，时间戳或有效字符串表示形式。函数的结果是0到59之间的整数。如果参数可以为null，则结果可以为null。如果参数为null，结果为0。<br/>
###句法
<code>SECOND ( expression )
</code>
<br/>
###例
RECEIVED列包含时间戳，其内部值等同于2005-12-25-17.12.30.000000。要仅返回时间戳的秒部分，请使用以下
<br/>
###语法：
<code>
SECOND(RECEIVED)
</code>
返回值30。<br/>

###SESSION_USER
当在外部存储例程之外使用时，SESSION_USER，USER和CURRENT_USER都返回创建SQL会话的用户的授权标识符。
在存储例程中使用时，SESSION_USER也总是返回此值。<br/>
但是，如果在使用EXTERNAL SECURITY DEFINER创建的存储例程中使用，则USER和CURRENT_USER将返回拥有该例程模式的用户的授权标识符。这通常是创建用户，尽管数据库所有者也可以是创建者。<br/>
有关定义者和调用者权限的信息，请参阅CREATE PROCEDURE或CREATE FUNCTION。<br/>
###句法
<code>
SESSION_USER
</code>
<br/>
例
<code>
VALUES SESSION_USER
</code>
<br/>
###SIGN SIGN函数返回指定数字的符号。
###SIN SIN函数返回指定数字的正弦。
###SINH SINH函数返回指定数字的双曲正弦。
###SMALLINT
###SQRT
返回浮点数的平方根; 只支持内置类型REAL，FLOAT和DOUBLE PRECISION。SQRT的返回类型是参数的类型。<br/>
注意：要对其他数据类型执行SQRT，您必须将其转换为浮点类型。<br/>
###句法
<code>
SQRT(FloatingPointExpression)
</code><br/>
###例子
<code>
-- throws an exception if any row stores a negative number:<br/>
VALUES SQRT(3421E+09)
<br/>
-- returns the square root of an INTEGER after casting it as a<br/>
-- floating point data type:<br/>
SELECT SQRT(myDoubleColumn) FROM MyTable
<br/>
VALUES SQRT (CAST(25 AS FLOAT));
</code>
<br/>
###SUBSTR
SUBSTR函数作用于字符串表达式或位字符串表达式。结果类型的是VARCHAR在所述第一壳体和VARCHAR FOR BIT DATA在第二种情况下。结果的长度是源类型的最大长度。<br/>
###句法
<code>
SUBSTR({ CharacterExpression },<br/>
   StartPosition [, LengthOfString ] )
</code>
<br/>
参数startPosition和可选参数lengthOfString都是整数表达式。第一个字符或位的起始位置为1，您必须为startPosition指定1或更高的值。<br/>
参数characterExpression是CHAR，VARCHAR或LONG VARCHAR数据类型或隐式转换为字符串（除了位表达式）的任何内置类型。<br/>
对于字符表达式，startPosition和lengthOfString参数是指字符。对于位表达式，startPosition和lengthOfString参数是指位。<br/>
startPosition是指从源表达式开始的位置（将第一个字符计算为1）。该指定startPosition不能是负数。<br/>
如果lengthOfString没有指定，SUBSTR返回从表达式的子串指定startPosition到源表达的末尾。<br/>如果lengthOfString被指定时，SUBSTR返回长度的VARCHAR或VARBIT lengthOfString起始于指定startPosition。如果为参数lengthOfString指定了负数，则SUBSTR函数将返回错误。<br/>
###例子
要返回该字的子字符串hello，从第二个字符开始并持续到单词的末尾，请使用以下子句：<br/>
<code>
VALUES SUBSTR('hello', 2)
</code>
<br/>
结果是' ello'。<br/>
要返回字的子字符串hello，从第一个字符开始并继续两个字符，请使用以下子句：<br/>
<code>
VALUES SUBSTR('hello',1,2)
</code>
<br/>
结果是' he'。<br/>

###SUM
SUM是一个聚合函数，用于计算一组行上的表达式的总和。只有在求值为数值数据类型的表达式上才允许SUM。<br/>
###句法
<code>
SUM ( [ DISTINCT | ALL ] Expression )
</code>
<br/>
DISTINCT和ALL限定符消除或保留重复。如果既不指定ALL也不指定DISTINCT，则假定为ALL。例如，如果列包含值1,1,1和2，则SUM（col）返回比SUM（DISTINCT col）更大的值。<br/>
每个SelectExpression只允许一个DISTINCT聚合表达式。例如，不允许以下查询：<br/>
<code>
SELECT AVG (DISTINCT flying_time), SUM (DISTINCT miles)
<br/>
FROM Flights
</code>
<br/>
该表达式可以包含多个列引用或表达式，但它不能包含另一个聚合或子查询。它必须评估为内置的数字数据类型。如果一个表达式计算结果为NULL，则聚合跳过该值。<br/>
生成的数据类型与其运行的表达式相同（可能会溢出）。<br/>
###例子
<code>
-- find all economy seats available:<br/>
SELECT SUM (economy_seats) FROM Airlines;
<br/>
-- use SUM on multiple column references<br/>
-- (find the total number of all seats purchased):<br/>
SELECT SUM (economy_seats_taken + business_seats_taken + firstclass_seats_taken)
<br/>
as seats_taken FROM FLIGHTAVAILABILITY;
</code>
<br/>
###TAN TAN函数返回指定数字的切线。
 指定的数字是您想要切线的角度（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
   如果指定的数字为NULL，则此函数的结果为NULL。<br/>
   如果指定的数字为零（0），则该函数的结果为零。<br/>
返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
###句法
<code>TAN ( number )</code>
<br/>
###TANH  TANH函数返回指定数字的双曲正切值。
指定的数字是您想要双曲正切的角度（弧度）。指定的数字必须是DOUBLE PRECISION号码。<br/>
如果指定的数字为NULL，则此函数的结果为NULL。<br/>
如果指定的数字为零（0），则该函数的结果为零。<br/>
返回值的数据类型为DOUBLE PRECISION（双精度）编号。<br/>
###句法
<code>TANH ( number )</code>

###TIME TIME函数从一个值返回一个时间。
###TIMESTAMP TIMESTAMP函数从值或一对值返回时间戳。
###TRIM
TRIM是一个使用字符表达式并返回该表达式的函数，其中前导和/或后跟填充字符被删除。可选参数指示是否应删除前导或尾部或前后两个字符，并指定要删除的填充字符。<br/>
###句法
<code>
TRIM( [ trimOperands ] trimSource)
<br/>
trimOperands  ::= { trimType [ trimCharacter ]  FROM | trimCharacter FROM }
<br/>
trimType      ::= { LEADING | TRAILING | BOTH }
<br/>
trimCharacter ::= CharacterExpression
<br/>
trimSource    ::= CharacterExpression
</code>
<br/>
如果trimType没有指定，则默认为BOTH。如果trimCharacter没有指定，则默认为空格字符（”“）。否则trimCharacter表达式必须避免以下之一：<br/>
一个长度正好是一个的字符串，或者。<br/>
###空值<br/>
如果trimCharacter或trimSource的值为NULL，则TRIM函数的结果为NULL。否则，TRIM功能的结果定义如下：
如果trimType为LEADING，则结果将是trimSource值，并删除所有主要出现的trimChar。<br/>
如果trimType是TRAILING，结果将是trimSource值，并删除所有拖尾事件的trimChar。<br/>
如果trimType是BOTH，则结果将是trimSource值，并删除所有前导的*和*拖尾事件。<br/>
如果trimSource的数据类型为CHAR或VARCHAR，则TRIM函数的返回类型为VARCHAR。否则TRIM函数的返回类型将为CLOB。<br/>
###例子
<code>
-- returns 'derby' (no spaces)<br/>
VALUES TRIM('  derby ')
<br/>
-- returns 'derby' (no spaces)
<br/>
VALUES TRIM(BOTH ' ' FROM '  derby ')
<br/>
-- returns 'derby ' (with a space at the end)
<br/>
VALUES TRIM(LEADING ' ' FROM '  derby ')
<br/>
-- returns '  derby' (with two spaces at the beginning)
<br/>
VALUES TRIM(TRAILING ' ' FROM '  derby ')
<br/>
-- returns NULL<br/>
VALUES TRIM(cast (null as char(1)) FROM '  derby ')
<br/>
-- returns NULL<br/>
VALUES TRIM(' ' FROM cast(null as varchar(30)))
<br/>
-- returns ' derb' (with a space at the beginning)
<br/>
VALUES TRIM('y' FROM ' derby')
<br/>
-- results in an error because trimCharacter can only be 1 character
<br/>
VALUES TRIM('by' FROM ' derby')
<br/>
</code>
###UCASE或UPPER
UCASE或UPPER将字符表达式作为参数，并返回一个字符串，其中所有字母字符都已转换为大写。<br/>
###句法
UCASE or UPPER ( CharacterExpression ) <br/>
如果参数类型为CHAR，则返回类型为CHAR。否则返回类型为VARCHAR。<br/>
返回值的长度和最大长度与参数的长度和最大长度相同。<br/>
###例
要aSD1#w以大写形式返回字符串，请使用以下子句：<br/>
<code>VALUES UPPER('aSD1#w')</code>
<br/>
返回的值是ASD1#W。<br/>

###USER   当在存储例程之外使用时，USER，CURRENT_USER和SESSION_USER都返回创建SQL会话的用户的授权标识符。
在存储例程中使用时，SESSION_USER也总是返回此值。<br/>
但是，如果在使用EXTERNAL SECURITY DEFINER创建的存储例程中使用，则USER和CURRENT_USER将返回拥有该例程模式的用户的授权标识符。这通常是创建用户，尽管数据库所有者也可以是创建者。<br/>
有关定义者和调用者权限的信息，请参阅CREATE PROCEDURE或CREATE FUNCTION。<br/>
###句法
###USER
###例
<code>VALUES USER</code>
<br/>
###VARCHAR  VARCHAR函数返回字符串的变长字符串表示形式。
###字符到varchar语法 Character to varchar syntax
<code>
VARCHAR (CharacterStringExpression ) 
</code>
<br/>
CharacterStringExpression <br/>
表达式的值必须是字符串数据类型，最大长度为32,672字节。<br/>
Datetime到varchar语法Datetime to varchar syntax<br/>
<code>
VARCHAR (DatetimeExpression ) 
</code>
<br/>
###DatetimeExpression 
表达式的值必须为日期，时间或时间戳数据类型。<br/>
###例
使用EMPLOYEE表，为Dolores Quintana选择作为VARCHAR等价物的作业描述（JOB定义为CHAR（8））：<br/>
<code>
SELECT VARCHAR(JOB)
<br/>
FROM EMPLOYEE
<br/>
WHERE LASTNAME = 'QUINTANA'
<br/>
</code>

###XMLEXISTS XMLEXISTS是一个SQL / XML操作符，可用于在SQL中查询XML值。
###XMLPARSE XMLPARSE是用于将字符串表达式解析为RowStore XML值的SQL / XML操作符。
###XMLQUERY XMLQUERY是可用于在SQL中查询XML值的SQL / XML操作符。
###XMLSERIALIZE XMLSERIALIZE是一个SQL / XML操作符，可用于将XML类型转换为字符类型。没有其他方法可以转换RowStore XML值的类型。
###YEAR
YEAR函数返回值的年份部分。参数必须是日期，时间戳，或日期或时间戳的有效字符串表示形式。该函数的结果是1到9999之间的整数。如果参数可以为null，结果可以为null; 如果参数为null，则结果为空值。<br/>
###句法
<code>
YEAR ( expression )
</code>
<br/>
###例
选择PROJECT表中计划启动（PRSTDATE）并在同一日历年结束（PRENDATE）的所有项目。<br/>
<code>
SELECT * FROM PROJECT WHERE YEAR(PRSTDATE) = YEAR(PRENDATE)
</code>
<br/>

##Procedures
表1. RowStore内置程序
Category类别  相应的Procedures  程序
###Callback configuration  回调配置	
ADD\_LISTENER  <br/>
ATTACH\_LOADER<br/>
ATTACH\_WRITER<br/>
REMOVE\_LISTENER<br/>
REMOVE\_LOADER<br/>
REMOVE\_WRITER<br/>
START\_ASYNC\_EVENT\_LISTENER<br/>
STOP\_ASYNC\_EVENT\_LISTENER<br/>
WAIT\_FOR\_SENDER\_QUEUE\_FLUSH<br/>
###Data export and import  数据导出和导入	
EXPORT\_QUERY<br/>
EXPORT\_TABLE<br/>
IMPORT\_DATA<br/>
IMPORT\_DATA\_EX<br/>
IMPORT\_DATA\_LOBS\_FROM\_EXTFILE<br/>
IMPORT\_TABLE<br/>
IMPORT\_TABLE\_EX<br/>
IMPORT\_TABLE\_LOBS\_FROM\_EXTFILE<br/>
###Disk store and HDFS store management   磁盘存储和HDFS存储管理	
DISKSTORE\_FSYNC<br/>
HDFS\_FLUSH\_QUEUE<br/>
HDFS\_FORCE\_COMPACTION<br/>
HDFS\_FORCE\_WRITEONLY\_FILEROLLOVER<br/>
###Heap and off-heap configuration<br/>

堆和非堆配置	

SET\_CRITICIAL\_HEAP\_PERCENTAGE<br/>

SET\_CRITICIAL\_HEAP\_PERCENTAGE\_SG<br/>

SET\_CRITICIAL\_OFFHEAP\_PERCENTAGE<br/>

SET\_CRITICIAL\_OFFHEAP\_PERCENTAGE\_SG<br/>

SET\_EVICTION\_HEAP\_PERCENTAGE<br/>

SET\_EVICTION\_HEAP\_PERCENTAGE\_SG<br/>

SET\_EVICTION\_OFFHEAP\_PERCENTAGE<br/>

SET\_EVICTION\_OFFHEAP\_PERCENTAGE\_SG<br/>

See also the functions:<br/>

GET\_CRITICIAL\_HEAP\_PERCENTAGE<br/>
GET\_CRITICIAL\_OFFHEAP\_PERCENTAGE<br/>
GET\_EVICTION\_HEAP\_PERCENTAGE<br/>
GET\_EVICTION\_OFFHEAP\_PERCENTAGE<br/>

###JAR installation and management   JAR安装管理	
INSTALL\_JAR  <br/>
REMOVE\_JAR   <br/>
REPLACE\_JAR  <br/>
###Partitioned table bucket management  分区表桶管理
CREATE\_ALL\_BUCKETS   <br/>
REBALANCE\_ALL\_BUCKETS  <br/>
###Statement and statistics management  声明和统计管理	
CANCEL\_STATEMENT    <br/>
EMPTY\_STATEMENT\_CACHE   <br/>
SET\_EXPLAIN\_CONNECTION   <br/>
SET\_STATISTICS\_TIMING   <br/>
SET\_GLOBAL\_STATEMENT\_STATISTICS    <br/>
SET\_QUERYSTATS    <br/>
###Troubleshooting  故障排除	
DUMP\_STACKS  <br/>
SET\_TRACE\_FLAG  <br/>
###User credentials  用户凭证
CHANGE\_PASSWORD <br/>
CREATE\_USER  <br/>
SHOW\_USERS  <br/>
###WAN configuration  广域网配置	
ATTACH\_GATEWAY\_EVENT\_ERROR\_HANDLER  <br/>
INCREMENT\_TABLE\_VERSION<br/>
REMOVE\_GATEWAY\_EVENT\_ERROR\_HANDLER  <br/>
START\_GATEWAYSENDER  <br/>
STOP\_GATEWAYSENDER<br/>
WAIT\_FOR\_SENDER\_QUEUE\_FLUSH  <br/>
See also the function: GET\_TABLE\_VERSION.
 <br/>

###SYS.ADD_LISTENER 将事件侦听器（EventCallBack接口的实现）附加到表中。
###SYS.ATTACH_GATEWAY_EVENT_ERROR_HANDLER 附加自定义错误处理程序实现，以解决当RowStore无法应用通过WAN连接接收或使用DBSynchronizer应用的DML事件时发生的故障。除了默认错误处理之外，还会调用您安装的任何自定义错误处理程序。您只能安装一个错误处理程序实现。
###SYS.ATTACH_LOADER 将行加载器实现附加到表。
###SYS.ATTACH_WRITER 将写入程序附加到表。
###SYS.CANCEL_STATEMENT 尝试取消完成时间过长或导致系统瓶颈的SQL语句。
###SYS.CHANGE_PASSWORD 更改现有非系统BUILTIN用户的密码。
###SYS.CREATE_ALL_BUCKETS 为分区表数据预分配桶。
###SYS.CREATE_USER 向RowStore分布式系统添加一个新的BUILTIN用户帐户。
###SYS.DISKSTORE_FSYNC将 所有数据刷新和fsync到配置的磁盘存储，包括异步持久性队列中的数据。
###SYS.DUMP_STACKS 将线程堆栈，锁和事务状态写入RowStore日志文件。您可以为当前的RowStore成员或分布式系统中的所有RowStore成员编写堆栈信息。
###SYSCS_UTIL.EMPTY_STATEMENT_CACHE 从数据库范围的语句高速缓存中删除尽可能多的编译语句（计划）。
###SYSCS_UTIL.EXPORT_QUERY 将SELECT语句的结果导出到操作系统文件。
###SYSCS_UTIL.EXPORT_TABLE 将表中的所有数据导出到操作系统文件。
###SYS.HDFS_FLUSH_QUEUE 立即将表的HDFS队列中的所有条目刷新到配置的HDFS存储。
###SYS.HDFS_FORCE_COMPACTION 为配置了HDFS读/写持久性的表启动主要的压缩周期。
###SYS.HDFS_FORCE_WRITEONLY_FILEROLLOVER 关闭只写表的HDFS日志文件（使其可用于MapReduce和HAWQ），并开始写入新的日志文件。
###SYSCS_UTIL.IMPORT_DATA 该SYSCS_UTIL.IMPORT_DATA系统过程将数据导入列在表中的一个子集。
###SYSCS_UTIL.IMPORT_DATA_EX SYSCS_UTIL.IMPORT_DATA 的扩展版本，可以将数据导入特定列而不锁定目标表和/或使用自定义导入过程。当您需要继续访问表格时，请在长时间导入操作期间使用此过程。
###SYSCS_UTIL.IMPORT_DATA_LOBS_FROM_EXTFILE 使用SYSCS_UTIL.IMPORT_DATA_LOBS_FROM_EXTFILE系统过程将数据导入表中的列子集，其中LOB数据存储在单独的文件中。主导入文件包含所有其他数据以及对LOB数据位置的引用。
###SYSCS_UTIL.IMPORT_TABLE 将数据从输入文件导入到表的所有列。
###SYSCS_UTIL.IMPORT_TABLE_EX SYSCS_UTIL.IMPORT_TABLE的 扩展版本，可以在不锁定目标表的情况下导入数据。当您需要继续访问表格时，请在长时间导入操作期间使用此过程。
###SYSCS_UTIL.IMPORT_TABLE_LOBS_FROM_EXTFILE 使用SYSCS_UTIL.IMPORT_TABLE_LOBS_FROM_EXTFILE系统过程将数据导入到表中，其中LOB数据存储在单独的文件中。主导入文件包含所有其他数据以及对LOB数据位置的引用。
###SYS.INCREMENT_TABLE_VERSION 将表的数据字典版本增量指定量。在WAN部署中使用此过程手动设置表的版本，使其与RowStore分布式系统中的相同表匹配。
###SQLJ.INSTALL_JAR SQLJ.INSTALL_JAR系统过程在RowStore中安装JAR文件。
###SQLJ.REMOVE_JAR SQLJ.REMOVE_JAR系统过程从RowStore中删除已安装的JAR文件。
###SQLJ.REPLACE_JAR SQLJ.REPLACE_JAR系统过程将替换RowStore中已安装的JAR文件。
###SYS.REBALANCE_ALL_BUCKETS 在可用的RowStore成员上重新平衡分区表数据。
###SYS.REMOVE_GATEWAY_EVENT_ERROR_HANDLER 删除与SYS.ATTACH_GATEWAY_EVENT_ERROR_HANDLER一起安装的自定义错误处理程序实现。
###SYS.REMOVE_LISTENER 删除之前添加到表的监听器实现。
###SYS.REMOVE_LOADER 从给定模式和表名称的表中删除一个加载器。
###SYS.REMOVE_WRITER 在给定模式和表名称的情况下，删除现有的RowStore侦听器。
###SYS.SET_CRITICAL_HEAP_PERCENTAGE 设置在LowMemoryExceptionRowStore数据存储上触发的Java堆内存使用率的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。
###SYS.SET_CRITICAL_HEAP_PERCENTAGE_SG 设置LowMemoryException在一个或多个RowStore服务器组上触发的Java堆内存使用率的百分比阈值。
###SYS.SET_CRITICAL_OFFHEAP_PERCENTAGE 设置在LowMemoryExceptionRowStore数据存储上触发s的非堆内存使用率的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。
###SYS.SET_CRITICAL_OFFHEAP_PERCENTAGE_SG 设置LowMemoryException在一个或多个RowStore服务器组上触发s的非堆内存使用率的百分比阈值。
###SYS.SET_EVICTION_HEAP_PERCENTAGE 设置触发RowStore数据存储以执行LRU_HEAP逐出配置的表执行LRU驱逐的Java堆内存使用率的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。
###SYS.SET_EVICTION_HEAP_PERCENTAGE_SG 设置触发一个或多个RowStore服务器组的成员的Java堆内存使用率的百分比阈值，以便对配置为逐出的表执行LRU驱逐。
###SYS.SET_EVICTION_OFFHEAP_PERCENTAGE 设置触发RowStore数据存储以对配置为LRU_HEAP驱逐的表执行LRU驱逐的非堆内存使用率的百分比阈值。此过程仅在本地RowStore数据存储成员上执行。
###SYS.SET_EVICTION_OFFHEAP_PERCENTAGE_SG 设置触发一个或多个RowStore服务器组的成员对执行LRU_HEAP逐出配置的表执行LRU驱逐的非堆内存使用率的百分比。
###SYSCS_UTIL.SET_EXPLAIN_CONNECTION 启用或禁用捕获当前连接的查询执行计划和统计信息。
###SYS.SET_GLOBAL_STATEMENT_STATISTICS 为RowStore分布式系统中的所有成员和客户端连接配置语句统计信息集。
###SYS.SET_QUERYSTATS 在SYS.QUERYSTATS系统表中启用或禁用查询统计信息收集。
###SYSCS_UTIL.SET_STATISTICS_TIMING 当打开统计时间时，可以跟踪语句执行的各个方面的时间。统计定时关闭时，所有定时值均设置为零。
###SYS.SET_TRACE_FLAGsnappydata.debug.true在分布式系统的所有成员（包括定位器）上 启用跟踪标志。
###SYS.SHOW_USERS 显示在RowStore成员中配置的所有BUILTIN用户的列表。
###SYS.START_ASYNC_EVENT_LISTENER 启动已安装的AsyncEventListener配置。
###SYS.START_GATEWAYSENDER 启动配置的网关发送者进程。
###SYS.STOP_ASYNC_EVENT_LISTENER 停止正在运行的AsyncEventListener配置。要确保没有排队的事件丢失，请在停止侦听器之前使用SYS.WAIT_FOR_SENDER_QUEUE_FLUSH。
###SYS.STOP_GATEWAYSENDER 停止配置的网关发送者进程。要确保没有排队的事件丢失，请在停止发件人之前使用SYS.WAIT_FOR_SENDER_QUEUE_FLUSH。
###SYS.WAIT_FOR_SENDER_QUEUE_FLUSH 此过程等待与网关发送者或AsyncEventListener相关联的队列清空托管队列或侦听器（主要和次要）的所有成员上的所有事件。您可以在停止网关发件人或AsyncEventListener之前以交互方式或SQL命令脚本中使用此过程，以确保没有排队的事件丢失。在配置有事件队列的表上执行ALTER TABLE语句之前，RowStore会自动调用此过程。

##Clauses
  对于此版本的RowStore中的每个SQL子句，请阅读语法，自定义扩展和示例的说明。
###FOR UPDATE
###注意： RowStore不支持瘦客户端连接中的此子句。
FOR UPDATE子句是SELECT语句的可选部分。默认情况下，游标是只读的。为了获得可更新的游标，需要FOR UPDATE子句。有关可更新性的更多信息，请参阅可更新游标和可更新ResultSet的要求。<br/>
###句法
<code>
FOR
<br/>
{
<br/>
    READ ONLY | FETCH ONLY |
<br/>
    UPDATE [ OF column-name [ , column-name]* ]
<br/>
}
<br/>
</code>
column-name是指基础查询的FROM子句中指定的表可见的名称。<br/>
注意：需要FOR UPDATE子句来获取可更新的JDBC ResultSet。<br/>
优化器能够使用索引，即使索引中的列正在更新。<br/>
<code>
SELECT RECEIVED, SOURCE, SUBJECT, NOTE_TEXT FROM SAMP.IN_TRAY FOR UPDATE;
</code>
<br/>
###FROM 指定查询的其他子句可以访问列用于表达式的表。
###句法
<code>
FROM table-expression [ , table-expression ] *
</code>
<br/>
描述<br/>
FROM子句是select-expression中的强制子句。它指定查询的其他子句可以访问列用于表达式的表（table-expression）。<br/>
###例
<code>
SELECT CUST_NAME FROM TRADE.CUSTOMERS WHERE CID < 5
<br/>
-- other types of TableExpressions<br/>
SELECT TABLENAME, ISINDEX 
<br/>
FROM SYS.SYSTABLES T, SYS.SYSCONGLOMERATES C
<br/>
WHERE T.TABLEID = C.TABLEID
<br/>
ORDER BY TABLENAME, ISINDEX
<br/>
-- force the join order<br/>
SELECT * FROM TRADE.SECURITIES S, TRADE.PORTFOLIO F WHERE SEC_ID = F.SID AND F.TID = ?
<br/>
-- a TableExpression can be a joinOperation. Therefore<br/>
-- you can have multiple join operations in a FROM clause<br/>
SELECT * FROM TRADE.CUSTOMERS C LEFT OUTER JOIN TRADE.PORTFOLIO F LEFT OUTER JOIN 
<br/>
TRADE.SELLORDERS SO ON F.CID = SO.CID ON C.CID= F.CID <br/>
 WHERE SO.SID < 100 AND SO.QTY > 500<br/>
</code>
###GROUP BY 
GROUP BY子句（SelectExpression的一部分）将结果分组为具有一个或多个列的匹配值的子集。在每个组中，没有两行对于分组列或列具有相同的值。对于分组目的，NULL被认为是等效的。<br/>
您通常将GROUP BY子句与聚合表达式结合使用。<br/>
column-name必须是当前查询范围的列; 在当前作用域之外的查询块中不能有任何列。例如，如果GROUP BY子句位于子查询中，则不能引用外部查询中的列。<br/>
RowStore不支持在数据类型BLOB，CLOB或LONG VARCHAR FOR BIT DATA的列上使用GROUP BY。<br/>
具有GROUP BY子句的SelectExpression中的SelectItem必须只包含聚合或分组列。<br/>
###例
<code>
-- find the quantity of units held by customer  grouped by customer ID<br/>
SELECT AVG (QTY), CID FROM TRADE.PORTFOLIO GROUP BY CID
<br/>
SELECT MAX(F.QTY), C.CID FROM TRADE.PORTFOLIO F, TRADE.CUSTOMERS C
<br/>
WHERE F.CID = C.CID GROUP BY C.CID
<br/>
SELECT (AVG(SUBTOTAL/QTY)) FROM TRADE.PORTFOLIO F WHERE F.SID = ? AND F.TID =? AND F.QTY <> 0
</code>
<br/>
###HAVING Restrict the results of a GROUP BY in a SelectExpression.
  在SelectExpression中限制GROUP BY的结果。<br/>
###句法
<code>HAVING searchCondition</code>
<br/>
###描述
HAVING子句限制组的结果通过在SelectExpression。HAVING子句应用于分组表的每个组，就像将WHERE子句应用于选择列表一样。如果没有GROUP BY子句，HAVING子句将作为单个组应用于整个结果。SELECT子句不能直接引用任何没有GROUP BY子句的列。但是，它可以引用常量，聚合和特殊寄存器。<br/>
searchCondition是一个专门的booleanExpression，它只能包含分组列（参见GROUP BY），作为聚合表达式一部分的列以及属于子查询的列。例如，以下查询是非法的，因为列AVAILQTY不是分组列，它不会出现在聚合中，并且它不在子查询中。HAVING子句中的聚合不需要出现在SELECT列表中。如果HAVING子句包含一个子查询，则当且仅当引用了一个分组列时，该子查询才能引用外部查询块。<br/>
###例
<code>
SELECT AVG (QTY), CID FROM TRADE.PORTFOLIO GROUP BY CID HAVING AVAILQTY > 150
<br/>
-- Find the average QTY held by a customer grouped on Customer ID,
<br/>
-- only when the customer holds atleast two different stocks
<br/>
SELECT AVG (QTY), CID FROM TRADE.PORTFOLIO GROUP BY CID HAVING COUNT(*) > 2
<br/>
</code>
###OFFSET and FETCH FIRST Clauses
 Limit the number of rows returned in a result set.限制结果集中返回的行数。<br/>
###句法
<code>
OFFSET integer-literal { ROW | ROWS }<br/>
FETCH { FIRST | NEXT } [integer-literal] { ROW | ROWS } ONLY  <br/>
</code>
###描述
注意：您不能在用于创建视图的查询中使用OFFSET或FETCH FIRST子句。请参阅CREATE VIEW。<br/>
OFFSET子句提供了在返回任何行之前跳过结果集中的多行的方法。<br/>
可以与OFFSET子句组合的FETCH FIRST子句限制了结果集中返回的行数。您可以使用FETCH FIRST子句从大型结果集中检索几行。FETCH FIRST经常与ORDER BY子句一起使用。<br/>
在这两个子句中，ROW是ROWS的代名词，而FIRST与NEXT是同义词。<br/>
在OFFSET子句中，整数字符必须等于0（如果没有提供OFFSET子句，则为默认值），否则必须为正数。如果OFFSET大于结果集中的行数，则不返回任何行。<br/>
在FETCH FIRST子句中，integer-literal必须为1或更大。您可以完全忽略整数字符，使用默认值1.使用默认值，RowStore返回结果集中的所有行（或仅使用剩余的那些行，如果使用OFFSET子句）。<br/>
###例子
<code>
SELECT * FROM T FETCH FIRST ROW ONLY<br/>
SELECT * FROM T ORDER BY I OFFSET 10 ROWS FETCH NEXT 10 ROWS ONLY<br/>
SELECT * FROM T OFFSET 100 ROWS<br/>
</code>
###ORDER BY   Specify the order in which rows appear in the ResultSet.
  指定行显示在ResultSet中的顺序。<br/>
###句法
<code>
ORDER BY { column-name | column-position | expression }
<br/>
   [ ASC | DESC ]
<br/>
   [ , column-name | column-position | expression
<br/>
   [ ASC | DESC ] ] * 
<br/>
</code>
###描述
ORDER BY子句是SELECT语句的一个可选元素。ORDER BY子句允许您指定行显示在ResultSet中的顺序。<br/>
注意：您不能在用于创建视图的查询中使用ORDER BY子句。请参阅CREATE VIEW。<br/>
注意： ORDER BY子句不支持聚合（集合函数）。但是，对于某些查询，您可以从SELECT语句的预计列中引用聚合函数的列位置，以将聚合包含在ORDER BY中。以列为单位查看示例。<br/>
RowStore不支持在数据类型BLOB，CLOB或LONG VARCHAR FOR BIT DATA的列上使用ORDER BY。<br/>
###列名
指从SELECT语句的底层查询中的select项目可见的名称。您在ORDER BY子句中指定的列 - 名称不需要是SELECT列表。<br/>
###列位置
一个整数，用于标识SELECT语句的底层查询中的select项目中的列数。列位置必须大于0且不大于结果表中的列数。换句话说，如果要按列排序，则必须在SELECT列表中指定该列。<br/>
尽管RowStore不支持在ORDER BY子句中包括聚合（集合函数），但可以使用列位置引用SELECT语句的投影列中的现有聚合。例如，在以下查询中，MAX（SALESDATE）集合通过使用其列位置为2：包含在ORDER BY中：<br/>
<code>
snappy> SELECT SALESNUMBER, MAX(SALESDATE) from SALES
<br/>
   GROUP BY SALESNUMBER
<br/>
   ORDER BY 2 DESC
<br/>
  OFFSET 10 ROWS FETCH NEXT 10 ROWS ONLY;
<br/>
</code>
  ##表达
###排序键表达式，如numeric，string和datetime表达式。表达式也可以是行值表达式，例如标量子查询或表达式。
 ## ASC
指定结果应按升序返回。如果未指定订单，则ASC为默认值。<br/>
##DESC
指定结果应按降序返回。<br/>
注意：如果指定SELECT DISTINCT或SELECT语句包含GROUP BY子句，则ORDER BY列必须在SELECT列表中。<br/>
注意： ORDER BY子句防止SELECT语句成为可更新游标。<br/>

###WHERE 
<code>
    Select rows based on a boolean expression.
</code><br/>
基于布尔表达式选择行。<br/>
###句法
<code>
WHERE Boolean expressions
</code>
###描述
WHERE子句是SelectExpression，DELETE语句或UPDATE语句的可选部分。WHERE子句允许您根据布尔表达式选择行。结果中只返回表达式求值为TRUE的行，或者在DELETE语句的情况下被删除，或者在更新UPDATE语句的情况下。
大多数通用表达式可以导致一个布尔值。另外还有更常见的布尔表达式<br/>
###例
<code>
-- find the flights where no business-class seats have<br/>
-- been booked<br/>
SELECT *
<br/>
FROM FlightAvailability<br/>
WHERE business_seats_taken IS NULL<br/>
OR business_seats_taken = 0<br/>
-- Join the EMP_ACT and EMPLOYEE tables<br/>
-- select all the columns from the EMP_ACT table and <br/>
-- add the employee's surname (LASTNAME) from the EMPLOYEE table<br/>
-- to each row of the result.<br/>
SELECT SAMP.EMP_ACT.*, LASTNAME<br/>
  FROM SAMP.EMP_ACT, SAMP.EMPLOYEE<br/>
  WHERE EMP_ACT.EMPNO = EMPLOYEE.EMPNO<br/>
-- Determine the employee number and salary of sales representatives <br/>
-- along with the average salary and head count of their departments.<br/>
-- This query must first create a new-column-name specified in the AS clause <br/>
-- which is outside the fullselect (DINFO) <br/>
-- in order to get the AVGSALARY and EMPCOUNT columns, <br/>
-- as well as the DEPTNO column that is used in the WHERE clause<br/>
SELECT THIS_EMP.EMPNO, THIS_EMP.SALARY, DINFO.AVGSALARY, DINFO.EMPCOUNT<br/>
 FROM EMPLOYEE THIS_EMP,<br/>
   (SELECT OTHERS.WORKDEPT AS DEPTNO,<br/>
           AVG(OTHERS.SALARY) AS AVGSALARY,<br/>
           COUNT(*) AS EMPCOUNT<br/>
    FROM EMPLOYEE OTHERS<br/>
    GROUP BY OTHERS.WORKDEPT<br/>
   )AS DINFO<br/>
 WHERE THIS_EMP.JOB = 'SALESREP'<br/>
           AND THIS_EMP.WORKDEPT = DINFO.DEPTNO<br/>

</code>

##关键字和标识符
 SQL语句作为字符串提供给RowStore，它必须遵循若干规则。<br/>

###关键词和标识符
SQL statements are provided as strings to RowStore that must follow several rules.<br/>
Permitted character set is unicode<br/>
Single quotation marks delimit character strings<br/>
Within a character string, a single quotation mark itself can be represented using two single quotation marks<br/>
SQL keywords and identifiers are case-insensitive<br/>
Double quotation marks denote delimited identifiers as in the next section<br/>
In accordance with SQL-92 standard, comments can be single-line or multi-line. Single-line 
<br/>
comments start with two dashes (–) and end with the newline character while multi-line <br/>comments start with forward slash star (/*) and end with start forward slash (*/).<br/>
Standard SQL Identifiers<br/>
Two categories of identifiers are defined in SQL-92 standard: ordinary and delimited.<br/>
The rules mentioned here for SQL identifiers apply to all names used in SQL statements <br/>including table-name, column-name, view-name, synonym-name, constraint-name, correlation-<br/>name, index-name, trigger-name, function-name and procedure-name. There are two <br/>categories of identifiers as defined in SQL-92 standard: ordinary and delimited. The <br/>ordinary names must follow the below mentioned rules:<br/>
Can contain only unicode letters and digits, or underscore characters ( _ )<br/>
Must begin with an alphabetic character<br/>
Should have a minimum length of one byte and a maximum of 128 bytes<br/>
Are case-insensitive (use delimited identifiers for case-sensitive)<br/>
Cannot contain quotation marks or spaces<br/>
Cannot be a reserved word<br/>
Delimited identifiers are those enclosed within double quotation (“) marks. If the <br/>identifier itself needs to have a double quotation mark then two consecutive double<br/> quotation marks should be used.<br/>
The following conventions are used in the syntax definitions in the remaining document:<br/>
Square brackets ([]) are used to enclose optional items.<br/>
Braces ({}) are used to group items and is not part of syntax as such.<br/>
A star (*) denotes that the preceding item can be repeated 0 or more number of times.<br/>
A pipe (|) is for ORing different elements i.e. one of the ORed items are needed.<br/>
An explicit bracket, brace, star or pipe will be shown enclosed in single quotes e.g. <br/>’*’ . Parentheses have no special meaning and are always literals.<br/>

SQL语句作为字符串提供给RowStore，它必须遵循若干规则。<br/>
允许的字符集是unicode<br/>
单引号将字符串分隔<br/>
在一个字符串中，单引号本身可以使用两个单引号来表示<br/>
SQL关键字和标识符不区分大小写<br/>
双引号表示分隔符号，如下一节所示<br/>
按照SQL-92标准，注释可以是单行或多行。单行注释以两条破折号（ - ）开始，以换行符结尾，多行注释以正斜杠（/ *）开头，以开始斜杠（* /）结束。<br/>


###标准SQL标识符
在SQL-92标准中定义了两类标识符：普通和定界。<br/>
这里提到的SQL标识符的规则适用于SQL语句中使用的所有名称，包括table-name，column-name，view-name，synonym-name，constraint-name，correlation-name，index-name，trigger-name，名称和过程名称。<br/>SQL-92标准中定义了两种类型的标识符：普通和定界。普通的名字必须遵循以下规则：<br/>
只能包含unicode字母和数字或下划线字符（_）<br/>
必须以字母开头<br/>
应该有一个字节的最小长度和最多128个字节<br/>
不区分大小写（使用分隔的标识符区分大小写）<br/>
不能包含引号或空格<br/>
不能是保留字<br/>
分隔标识符是双引号（“）标记中的标识符。如果标识符本身需要双引号，则应使用两个连续的双引号。<br/>
在剩余文档中的语法定义中使用以下约定：<br/>
方括号（[]）用于封装可选项。<br/>
大括号（{}）用于对项目进行分组，而不是语法的一部分。<br/>
星号（*）表示前一项可以重复0次以上。<br/>
管道（|）用于ORing不同的元素，即需要一个ORed项目。<br/>
显式括号，括号，星号或管道将显示为单引号，例如'*'。括号没有特殊含义，总是文字。<br/>
表达式<br/>
数值表达式<br/>
数值表达式是导致数值的表达式。大多数通用表达式可以导致数值。数值具有以下类型之一：<br/>
BIGINT<br/>
DECIMAL<br/>
DOUBLE PRECISION<br/>
INTEGER<br/>
REAL<br/>
SMALLINT<br/>




















