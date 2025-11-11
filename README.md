# java-study
2025年11月10日
1.对于字符串可以使用前缀索引：create index (索引名) on 表名(字段(前缀数));//前缀要有较高的区分度
2.使用索引时，优先使用联合索引，可以使用覆盖查询来避免回表。
3.索引的设计原则：<img width="797" height="291" alt="image" src="https://github.com/user-attachments/assets/54c759fa-86db-4a04-af5a-4b95b135b6ad" />

插入优化：
1.批量插入。
2.手动事务提交。
3.主键顺序插入。
4.大规模插入时使用load插入。
load data local infile 文件名 into table 表名 fields terminated 字段分隔符 lines terminated 行分隔符。

主键设计原则：
1.主键长度不宜过长（导致二级索引的叶子节点占用空间太多）
2.主键顺序插入（聚集索引的叶子节点按顺序排列，不按顺序插入会导致也页分裂）
3.避免使用没有顺序的主键（如身份证，UUID）（没有顺序可言）
4.避免修改主键（修改主键会导致所有的二级索引需要修改，代价太大）

order by优化：
1.根据排序字段建立索引，遵循最左前缀法则。
2.尽量使用覆盖索引（反之会导致回表就不会使用索引排序）。
3.注意索引建立时字段的顺序（默认为顺序）
4.使using filesort时，可以增加sort_buffer_size的大小（默认256k）。

group by
1.满足最左前缀法则，同时使用where和group by也可以满足最左前缀法则。

limit ：覆盖索引+子查询
select id from tb_user order by id limit (99999,10);
select s.* from tb_user as s,(select id from tb_user order by id limit (99999,10)) a where s.id = a.id;   

count(主键)，取值id，不判断
count(字段) ,取值，count会判断该字段是否为null，不为bull自动加1
count(1)不取值
count(*)不取值
count(字段)<count(主键)<count(1)=count(*);

update注意事项：
1.条件使用索引时，使用行锁，锁住要修改的这一行，条件非索引时，使用表锁，锁住这一张表，其他线程无法修改这张表。
so 尽量使用索引字段作为更新判断的条件。
