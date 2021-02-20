# TimeStamp

1. 一张表中同时多个默认当前时间的timestamp 字段。

CREATE TABLE test
(
  id            INT(10) UNSIGNED PRIMARY KEY   NOT NULL,

  updatedAt     TIMESTAMP  DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  createdAt     TIMESTAMP

}

2. timestamp的范围

   timestamp 虽然有上限限制，但是它保存的是时间戳，可以不用去考虑时区的问题，如果是需要处理与时区相关的需求，
   解决 2038 限制的时候，建议将 timestamp改为整数类型，用来保存时间戳，在程序中再进行转换（这个方案没有实施过，仅仅是建议，慎用！！）

   ![img](https://images2018.cnblogs.com/blog/578448/201807/578448-20180725113426291-1388161252.png)

   解决思路①: 使用 datatime 类型替换 timestamp（需要注意，原来的 timestamp的默认值，这个也需要加上）

   ```
   ALTER TABLE `student` CHANGE `entry_date` `temp_entry_date` timestamp NOT NULL default '0000-00-00 00:00:00';
   ALTER TABLE `student` ADD `entry_date` DATETIME NOT NULL default '0000-00-00 00:00:00';
   UPDATE `student` SET `entry_date` = `temp_entry_date`;
   ALTER TABLE `student` DROP `temp_entry_date`;
   ```

   

   | timestamp                                                    | datetime                                                     |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 可自动更新和初始化，默认显示格式相同YYYY-MM-dd HH:mm:ss      | 一样                                                         |
   | `'1970-01-01 00:00:01'` UTC to `'2038-01-19 03:14:07'` UTC<br />自动时区转化，实际存储毫秒数，4字节存储 | `'1000-01-01 00:00:00'` to `'9999-12-31 23:59:59'`<br />不支持时区，8字节存储 |

3. 默认值

   | DEFAULT CURRENT_TIMESTAMP                                    | 创建新记录的时候把这个字段设置为当前时间                     |                                                              |
   | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | ON UPDATE CURRENT_TIMESTAMP                                  | 修改现有记录的时候对这个数据列刷新                           | **执行update语句，并未改变列值(或者说设置值为当前值), 则不会更新** |
   | TIMESTAMP ON UPDATE CURRENT_TIMESTAMP                        | 在创建新记录的时候把这个字段设置为0000-00-00 00:00:00，以后修改时刷新它 |                                                              |
   | TIMESTAMP DEFAULT ‘yyyy-mm-dd hh:mm:ss’ ON UPDATE CURRENT_TIMESTAMP | 在创建新记录的时候把这个字段设置为给定值，以后修改时刷新它   |                                                              |
   | mysql不会给timestamp设置默认值，除非显式设置default约束或者可空null | 特例：mysql会给表第一个timestamp类型的字段同时添加default current_timestamp和on update timestamp | 禁止特例:<br />1. 设置`explicit_defaults_for_timestamp`为enable<br />2. 显式设置该字段default或者null |

   1. `timestamp列默认not null`。没有显式指定nullable,那么default null不合法

   2. 去除timestamp的默认值:

      alter table xx alter column yy drop default

   3. sql语句去除timestamp列的更新行为:

      alter table xx change yy yy timestamp [null default null] / not null