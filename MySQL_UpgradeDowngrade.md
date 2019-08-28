# 									MySQL  Upgrade/Downgrade

​	MySQL的版本升级或降级，有大小版本升级/降级，有就地和逻辑升级/降级之分。通常来说升级操作的场景需求更普遍，此外对于小版本（如MySQL5.7.23<->MySQL5.7.26）的升级/降级使用就地（原地）方式更为便捷，反之大版本（MySQL5.6<->MySQL5.7）的升级/降级使用逻辑方式更为便捷安全，尤其是对于大版本的降级操作来说，而且就地升级对于跨大版本的升级/降级不支持，如MySQL5.1->MySQL5.6，下述是对就地升级和就地降级的操作说明。

# *升级/降级前*

1. 升级/降级操作前，全量备份数据，备份内容包括MySQL的系统库表
2. 参考官网文档，明确可升级/降级的操作路径，如直接跨大于一个大版本的升级不支持
3. 升级前了解原版本和目标版本的特性变化，各版本之间增加/删除/不支持的特性（参数?）各有哪些?
4. 升级之前，了解原实例的安装方式，升级应该使用适用于当前实例的安装方式，如二进制安装方式
5. 根据实际情况，权衡评估升级方式的工作，如根据**数据量大小**或**升级耗时**选择原地还是逻辑升级方式?
6. 评估升级版本对程序的影响?如MySQL的接口？是否跟着升级或重新安装

# *升级/降级路径*

1. 所有升级/降级操作的支持，必须是GA版本之间
2. 支持诸如MySQL5.6->MySQL5.7，但建议在升级到5.7之前，将原实例升级至5.6的最新稳定版本，如当前的5.6.45
3. 使用逻辑降级方式，支持MySQL5.7->MySQL5.6
4. 不支持跳版本的升级，如~~MySQL5.5->MySQL5.7~~
5. 支持发布系列中的版本升级，如MySQL5.7.x->MySQL5.7.y，同时也支持跨版本升级，如MySQL5.7.x->MySQL5.7.z

# *Upgrade*

版本升级相对于版本降级更容易，因为软件版本通常来说都是向后兼容的。对于MySQL的版本升级也不例外，不管是大版本或小版本升级，下面就大小版本的就地升级方式做操作说明，其操作方式都是一致的，实操验证ok。

1. 如有需要备份数据文件，主要是为了安全起见，尤其是单实例，注意此备份内容包括系统库表数据

2. 设置innodb_fast_shutdown选项，安全关闭MySQL

   `mysql -uroot -ppasswd --execute="set global innodb_fast_shutdown=0"`

3. 关闭MySQL

   `mysql -uroot -ppasswd --execute="shutdown"`

4. 替换原实例的basedir目录

   `mv /usr/local/mysql  /usr/local/mysql5722`  

   `mv /usr/local/mysql5726  /usr/local/mysql`

   `chown -R mysql:mysql /usr/local/mysql`

5. 启动MySQL，观察errorlog有无异常，注意大版本升级，各版本之间的特性，参数差异

   `/etc/init.d/mysqld start`

   `tailf  /data/3306/log/error.log`

6. 检查所有库表并修复兼容性问题，执行升级MySQL系统库，如all ok即可

   `mysql_upgrade -uroot -ppasswd`

7. 重启MySQL再次验证升级是否OK

   `/etc/init.d/mysqld restart`

   `tailf /data/3306/log/error.log`

上述就地升级版本的操作步骤流程，不管是MySQL5.6->MySQL5.7，还是MySQL5.7.22->MySQL5.7.26，测试OK

# *Downgrade*

版本降级的需求，我暂时没想到有什么场景会有这样的需求，因为是向上（后）兼容的特性，降级操作带来的问题是新版本向下（前）面临诸如表存储格式的不兼容，对于大版本的降级，通过备份恢复（mysqldump）方式来实现。下述是对小版本的就地降级方式的操作说明

1. 操作前备份备份备份，尤其是对于大变更或者高危操作等等尤其重要，注意此备份内容包括系统库表数据

2. 设置innodb_fast_shutdown，安全关闭innodb

   `mysql -uroot -ppasswd  --execute="set global innodb_fast_innodb=0"`

3. 关闭MySQL

   `/etc/init.d/mysqld stop or mysql -uroot -ppasswd -e "shutdown"`

4. 替换原实例的basedir目录

   `mv /usr/local/mysql /usr/local/mysql5726`

   `mv /usr/local/mysql5723  /usr/local/mysql`

   `chown -R mysql:mysql /usr/local/mysql`

5. 启动MySQL，并观察errorlog有无异常，各版本之间的特性，参数差异

   `/etc/init.d/mysqld start  or mysqld_safe  --defautls-file=/etc/my.cnf  &`

   `tailf /data/3306/log/error.log`

6. 检查所有库表并修复兼容性问题，执行升级MySQL系统库，观察有无异常

   `mysql_upgrade -uroot -ppasswd`

7. 再次重启MySQL，验证升级是否OK

   `/etc/init.d/mysqld start  or mysqld_safe  --defautls-file=/etc/my.cnf  &`

   `tailf /data/3306/log/error.log`

8. 简单验证数据在降级前后是否一致

   `mysql -uroot -ppasswd -e "show databases"`