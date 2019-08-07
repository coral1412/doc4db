# 			MySQL_Logical_Backup

## mysqldump

- 简介

  mysqldump客户端组件执行逻辑备份，生成一组SQL语句，可执行SQL语句还原数据对象定义和表数据，它能转储一个或者多个MySQL数据库，以便备份或传输至其它MySQL服务器，mysqldump命令工具还可以CSV或text、XML格式转储。

- 实现原理

  mysqldump备份出来的文件内容即是SQL文本文件，

- 权限要求

  备份数据表，mysqldump至少需要select权限，show view权限备份view，trigger权限备份triggers，如不使用--single-transaction则需要lock tables权限，其它权限根据mysqldump使用的参数而定。

- 特点、缺点

  mysqldump提供了非常灵活的备份功能，可根据库/表/视图/触发器/函数or存储过程等对象类型灵活备份，尤其在数据量相对较小的开发/测试环境的数据迁移，用户可查看或者编辑SQL脚本文件。但对于正式的生产环境，当数据量较大时，mysqldump工具并不是一个好的选择，即使备份耗时在合理范围内，但在数据恢复还原过程将可能消耗很长很长的时间，因为重播SQL涉及到磁盘IO、索引创建等影响，此外，对于>=MySQL5.7版本的多源主库复制的从库新建和使用TokuDB存储引擎的数据库表的备份，相较于物理备份工具Xtrabackup工具来说，操作更加简单方便。

- 重点参数

- 使用示例

- 注意事项

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

