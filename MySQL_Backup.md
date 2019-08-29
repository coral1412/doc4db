# 			MySQL_Backup

## mysqldump

- 简介

  ​	mysqldump命令帮助文档是这么描述的`Dumping structure and contents of MySQL databases and tables.`，即转储数据库数据表的结构和内容。mysqldump客户端组件执行逻辑备份，生成一组SQL语句（包括ddl和dml），可执行SQL语句还原数据库表对象的定义和数据内容，它能转储一个或者多个MySQL数据库，以便备份或传输到其它的MySQL服务器上，mysqldump命令工具还可以CSV或text、XML格式转储。

- 实现原理

  ​	mysqldump备份出来的文件内容即是SQL文本文件，本质上就是通过`show create database/table` 、`show table status like 'table'`、`select * from table`等查询结果实现获取表数据的。

- 权限要求

  ​	备份数据表，mysqldump至少需要select权限，show view权限备份view，trigger权限备份triggers，如不使用--single-transaction则需要lock tables权限，其它权限根据mysqldump使用的参数而定。

- 特点、缺点

  ​	mysqldump提供了非常灵活的备份功能，可根据库/表/视图/触发器/函数or存储过程等对象类型灵活备份，尤其在数据量相对较小的开发/测试环境的数据迁移，用户可查看或者编辑SQL脚本文件。但对于正式的生产环境，当数据量较大时，mysqldump工具并不是一个好的选择，即使备份耗时在合理范围内，但在数据恢复还原过程将可能消耗很长很长的时间，因为重播SQL涉及到磁盘IO、索引创建等影响，此外，对于>=MySQL5.7版本的多源主库复制的从库新建和使用TokuDB存储引擎的数据库表的备份，相较于物理备份工具Xtrabackup工具来说，操作更加简单方便。

- 重点参数

  **master-data**，记录当前执行备份库的binlog信息，binlog_file和binlog_pos信息，参数值有1和2，1是将change master写入备份文件，2是将change master注释形式写入备份文件。

  **dump-slave**，记录主库的binlog信息，如果是在从库执行备份，则记录的是show slave status的主库binlog信息。

  **single-transaction**，对于事务存储引擎（如innodb）数据表一致性备份，即`START TRANSACTION WITH CONSISTENT SNAPSHOT` ，保证在备份时，任意时刻执行select from table的数据与发出`WITH CONSISTENT SNAPSHOT`时是一致，注意`WITH CONSISTENT SNAPSHOT`必须是在RR(`REPEATABLE READ`)事务隔离级别下生效。

   	如果按照备份文件结果、用户等来区分，mysqldump使用的参数可区分如下几种类型的参数选项：

  	1. **连接选项**，如--user、--password、--port、--host、--bind-address、--compress等
   	2. **ddl选项**，如--add-drop-database、--add-drop-table、--add-drop-trigger、--all-tablespace、--no-create-db、--no-create-info等
   	3. **debug选项**，如--comments、--force、--log-error等
   	4. **复制选项**，如--apply-slave-statements、--dump-slave、--master-data、--set-gtid-purged等
   	5. **过滤选项**，--all-databases，--databases、--event、--ignore-table、--no-data、--routines、--triggers、--tables、--where等
   	6. **性能选项**，--disable-keys、--extended-insert、--insert-ignore、--max-allowed-packet、--net-buffer-length、--opt、--skip-opt、--quick 等
   	7. **事务选线**，--single-transaction、--flush-privileges、--flush-logs、--add-locks、--lock-all-tables、--lock-tables、--no-autocommit、--order-by-primary等

- 使用示例

  `mysqldump -uroot -ppasswd -R -E --triggesr --single-transaction=1 --master-data=2 --all-databases>/data/dumpfile/all.sql`

  `mysqldump  -uroot -ppasswd -R -E --triggers --single-transaction=1 --master-data=2 --databases test --ignore-table='test.test'  >/data/dumpfile/all_ignore_test_test.sql`

  `mysqldump  -uroot -ppasswd  --all-databases  --single-transaction=1  --no-data   --skip-add-drop-table >/data/dumpfile/all_schema.sql`

- 限制

  1. mysqldump默认是不备份information_schema、performance_schema、sys库的内容
  2. mysqldump默认是不备份NDB Cluster的`ndbinfo` information database
  3. 在使用mysqldump的时候，万万要注意其过滤选项，如不小心会导致目标库数据**恢复出厂设置**

## mysqlpump

- 简介
- 实现原理
- 权限要求
- 特点、缺点
- 重点参数
- 使用示例
- 注意事项

## mydumper/myloader

- 简介
- 实现原理
- 权限要求
- 特点、缺点
- 重点参数
- 使用示例
- 注意事项

## xtrabacupex

- 简介
- 实现原理
- 权限要求
- 特点、缺点
- 重点参数
- 使用示例
- 注意事项