# RabbitMQ

`开源的消息代理和队列服务器,用来通过普通协议在完全不同的应用之间共享数据, 使用ErLang语言编写, 基于AMQP协议`

* 可靠性投递模式, 返回模式
* 与 spring AMQP完美整合
* 集群模式风湿, 表达式配置, HA模式, 镜像队列模型
* 保证数据不丢失的前提做到`高可靠, 高可用`

##### 高性能的原因:

* Erlang语言
  * 用于交换机领域, 使 RabbitMQ 在 Broker之间进行数据交互的性能优秀
  * 和原生 Socket 一样的延迟

### AMQP

`Advanced Message Queuing Protocol`: 高级消息队列协议



#### 可靠性投递

![1620480695060](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1620480695060.png)

### 幂等性: 运行n次, 结果都可预测

#### 消费端: 避免消息的重复消费

* 唯一ID + 指纹码机制, 数据库去重
  * 高并发下有数据库写入的瓶颈
    * 跟进ID进行分库分表策略, 进行算法路由
* 利用 Redis 原子性





#### 消费端限流

* 手工ACK和NACK

  * 业务异常, 可以进行日志记录, 然后进行补偿

  * 服务器宕机, 需要进行手工ACK, 保障消费端消费成功

    

#### DLX: 死信队列

​	当消息在一个队列中变成死信(dead message), 它能被重新publish到另一个Exchange, 这个Exchange就是DLX

消息变死信

* 消息被拒绝(nack)并且 requeue=false
* 消息TTL过期
* 队列达到最大长度