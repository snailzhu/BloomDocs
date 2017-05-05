#创建DISKSTORE
##磁盘存储为需要溢出或持久化的表和队列提供磁盘存储（例如，当使用异步write-back侦听器时）。
###句法

<code>
CREATE DISKSTORE diskstore_name<br/>

    [ MAXLOGSIZE integer-constant ]<br/>
    [ AUTOCOMPACT boolean-constant ]<br/>
    [ ALLOWFORCECOMPACTION boolean-constant ]<br/>
    [ COMPACTIONTHRESHOLD integer-constant ]<br/>
    [ TIMEINTERVAL integer-constant ]<br/>
    [ WRITEBUFFERSIZE integer-constant ]<br/>
    [ QUEUESIZE integer-constant ]<br/>
    [ ( 'dir-name' [ integer-constant ] [,'dir-name' [ integer-constant ] ]* ) ]<br/>
</code>
###描述
执行CREATE DISKSTORE命令时，RowStore会尝试预分配oplog文件。有关详细信息，请参阅防止磁盘完全错误。
定位到同一磁盘存储的所有表共享该磁盘存储的持久性属性。不指定命名磁盘存储的表使用默认磁盘存储进行溢出或持久化。默认情况下，RowStore使用成员的工作目录作为默认磁盘存储。<br/>
请参阅将表数据持久化到RowStore磁盘存储。<br/>
MAXLOGSIZE<br/>
RowStore在操作日志（oplog）文件中记录DML语句。此选项设置在RowStore自动滚动到新文件之前，oplog可以达到的最大大小（以兆字节为单位）。这个大小是.crf和.drf oplog文件的组合大小。当RowStore创建一个oplog文件时，它会立即保留这一数量的文件空间。RowStore只会在一个干净的关闭（例如，snappy-shell rowstore server stop或snappy-shell shut-down-all）上截断未使用的空间。<br/>
默认值为1 GB。<br/>
自动化<br/>
将此选项设置为“true”（默认值）以自动压缩磁盘文件。如果不需要压缩，或者如果要使用snappy-shell实用程序手动压缩磁盘文件，请将选项设置为“false” 。<br/>
RowStore通过删除DML语句在oplog文件中生成的“垃圾”数据来执行压缩。<br/>
ALLOWFORCECOMPACTION<br/>
将此选项设置为“true”以使用该snappy-shell实用程序启用在线压缩oplog文件。默认情况下，此选项设置为“false”（禁用）。<br/>
COMPACTIONTHRESHOLD<br/>
在RowStore启动自动压缩之前，设置oplog中可能存在的“垃圾”数据量的阈值。创建垃圾数据作为DML操作创建，更新和删除表中的行。阈值定义为百分比（从0-100的整数）。默认值为50.当“垃圾”数据的数量超过此百分比时，如果启用了AUTOCOMPACT，磁盘存储就可以进行自动压缩。<br/>
时间间隔<br/>
设置RowStore异步将数据刷新到磁盘之前可以经过的毫秒数。TIMEINTERVAL仅用于使用asynchronousCREATE TABLE语句的persistence子句中的选项创建的表。请参见CREATE TABLE。默认值为1000毫秒（1秒）。<br/>
WRITEBUFFERSIZE<br/>
设置将数据持久存储到磁盘时使用的缓冲区大小（以字节为单位）。默认值为32768字节。<br/>
QUEUESIZE<br/>
将RowStore异步队列的最大行数设置为磁盘。在排队这个异步操作数之后，额外的异步操作将被阻塞，直到现有的写入被刷新到磁盘。单个DML操作可能会影响多行，每行修改，插入和删除都被视为单独的操作。默认的QUEUESIZE值为0，不指定任何限制。<br/>
名称<br/>
可选dir-name条目定义用于磁盘存储的特定主机系统目录。您可以dir-name使用以下语法包含一个或多个条目：
[ ( 'dir-name' [ integer-constant ] [,'dir-name' [ integer-constant ] ]* ) ]
在每个条目中：<br/>
dir-name指定要用于磁盘存储的目录的名称。如有必要，将在每个成员上创建磁盘存储目录。如果不指定绝对路径，则RowStore会在每个成员的工作目录中创建或使用命名目录（或者在由sys-disk-dir boot属性指定的值中定义）。如果指定绝对路径，那么在执行命令时，路径中的所有父目录都必须存在。<br/>
注意： RowStore使用“无共享”磁盘存储设计，您不能使用单个磁盘存储目录来存储来自多个RowStore成员的oplog文件。<br/>
integer-constant可选地指定用于该目录中的磁盘存储的最大空间量（以兆字节为单位）。使用的空间计算为目录中所有oplog文件的组合大小。<br/>
如果不指定整数常量值，则RowStore不会对该目录中的磁盘存储文件使用的空间量进行限制。如果您指定了限制，则大小必须足够大以容纳磁盘存储oplog文件（MAXLOGSIZE默认值为1 GB），并在目录中留出足够的可用空间，以避免较低的磁盘空间警告（请参阅gemfire.DISKSPACE_WARNING_INTERVAL） 。如果指定的大小不能容纳oplog文件并维持足够的可用空间，RowStore无法创建具有SQLState错误XOZ33的磁盘存储：无法创建大小为{0} MB的大小为{1} MB的大小的最大大小{1} MB的oplogs存储目录“{2}”。<br/>
您可以dir-name在CREATE DISKSTORE语句中指定任意数量的条目。数据在目录中的活动磁盘文件之间均匀分布，保持在您设置的任何限制之内。<br/>
###例
此示例使用默认基本目录和参数值来创建一个命名的磁盘存储：<br/>
<code>
snappy> CREATE DISKSTORE STORE1;
</code>
<br/>
此示例配置磁盘存储参数并指定存储目录：<br/>
<code>
snappy> CREATE DISKSTORE STORE1 <br/>
    MAXLOGSIZE 1024 <br/>
    AUTOCOMPACT TRUE<br/>
    ALLOWFORCECOMPACTION  FALSE <br/>
    COMPACTIONTHRESHOLD  80<br/>
    TIMEINTERVAL  223344<br/>
    WRITEBUFFERSIZE 19292393<br/>
    QUEUESIZE 17374<br/>
    ('dir1' 456);<br/>
</code>
此示例为oplog文件指定多个存储目录和目录大小：<br/>
<code>
snappy> CREATE DISKSTORE STORE1 <br/>
    WRITEBUFFERSIZE 19292393<br/>
    QUEUESIZE 17374<br/>
    ('dir1' 456 , 'dir2', 'dir3' 532 );<br/>
</code>
在CREATE TABLE语句中指定表持久性和指定的磁盘存储。例如：<br/>
<code>
CREATE TABLE Orders(OrderId INT NOT NULL,ItemId INT ) <br/>
   persistent 'OrdersDiskStore' asynchronous<br/>
</code>