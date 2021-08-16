[TOC]

# Spring Cloud Stream

用于构建高度可扩展的基于事件驱动的微服务

由于消息持有双方服务规定的业务数据，在一定程度上违背了封装的要义。换言之，生产与消费消息的双方都紧耦合于消息





## Spring Messaging：统一消息的编程模型

![](C:\Users\76111\Desktop\spring-messaging.png)

### Base模块

- Message：消息定义

  ![1624855828163](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624855828163.png)

- MessageHandler：消息处理

  ![1624855940507](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624855940507.png)

- MessageChannel： pipes-and-filters架构的管道

  ![1624856023904](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624856023904.png)

  

![1624856141597](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624856141597.png)

String、Json、Byte、Protobuf直接的转换

### 核心 Core

- 发送消息

  ![1624856766933](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624856766933.png)

- 接收消息

  ![1624856792877](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624856792877.png)

- 重试

  ![1624856819831](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624856819831.png)



## Spring Integration

![spring-integration](C:\Users\76111\Desktop\spring-integration.png)

**在系统内提供实现轻量级、事件驱动交互行为的框架**

**在系统间提供一种基于适配器的平台，以支持灵活的系统间交互**

是 Spring Messaging 的扩展

主要有Message、Channel、Message End-Point 组成

### Message：类似水

* 基础构件和核心，所有的流程都围绕着Message运转

### MessageChannel：类似管子

* QueueChannel：队列模式通道

  ![](C:\Users\76111\Desktop\messageChannel.jpg)

* PublishSubscribeChannel：发布订阅式通道

  ![img](https://upload-images.jianshu.io/upload_images/5891170-f2195ab401051398.png?imageMogr2/auto-orient/strip|imageView2/2/w/344/format/webp)

* PriorityChannel：优先级队列通道

* DirectChannel：最简单的点对点通道

  ![img](https://upload-images.jianshu.io/upload_images/5891170-607d0c2793809c34.png?imageMogr2/auto-orient/strip|imageView2/2/w/292/format/webp)

  ......



### Message End-Point

消息端点：真正处理消息的(Message)组件。

可以控制通道的路由。就像分水管，电表，水龙头开关

- Channel Adapter：就像各个地方的水都能接到管子里适配。单向的，分为入站(inbound)和出站(outbound)

![img](https://upload-images.jianshu.io/upload_images/5891170-b79a1b981aae1950.png?imageMogr2/auto-orient/strip|imageView2/2/w/766/format/webp)

- Gateway：**提供了双向的请求/返回集成方式**，也分为入站(inbound)和出站(outbound)

![img](https://upload-images.jianshu.io/upload_images/5891170-af9a87c4a3449f42.png?imageMogr2/auto-orient/strip|imageView2/2/w/777/format/webp)

![img](https://upload-images.jianshu.io/upload_images/5891170-017d10305b0c84bd.png?imageMogr2/auto-orient/strip|imageView2/2/w/751/format/webp)

* Router（**类似分水管，将水送往不同地方**）

* Filter（**就像工业用水和饮用水，需要过滤到不同地方**）

* Splitter（**饮用水也要分成多个管口给多人饮用**）

* Aggregator（**将废水集中起来处理**）

* Transformer（**就类似于净化水的程序一样**）

  

发送消息

![1624974885091](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624974885091.png)



End-Point

![1624974534780](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624974534780.png)

![1625017063855](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1625017063855.png)

![1625017668275](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1625017668275.png)

收到消息：

![1624974728811](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1624974728811.png)



## Spring Cloud Stream

SCS与各模块之间的关系是：

- SCS 在 Spring Integration 的基础上进行了封装，提出了 `Binder`, `Binding`, `@EnableBinding`, `@StreamListener` 等概念;

- SCS 与 Spring Boot Actuator 整合，提供了 `/bindings`, `/channels`endpoint;

- SCS 与 Spring Boot Externalized Configuration 整合，提供了 `BindingProperties`, `BinderProperties` 等外部化配置类;

- SCS 增强了消息发送失败的和消费失败情况下的处理逻辑等功能。

- SCS 是 Spring Integration 的加强，同时与 Spring Boot 体系进行了融合，也是 Spring Cloud Bus 的基础。它屏蔽了底层消息中间件的实现细节，希望以统一的一套 API 来进行消息的发送/消费，底层消息中间件的实现细节由各消息中间件的 Binder 完成

  

## 通信协议

没有实时协议这个东西，这类协议都是设计用于分发消息，同步数据和通过持久双向的连接来进行请求/响应

协议的目的分两类

- 纯消息：如 websocket
- 发布订阅- 如 AMQP、MQTT，STOMP

### MQTT：Message Queue Telemerty Transport

- 二进制协议，主要用于服务器和那些低功耗的物联网设备（IoT）之间的通信，适合用于不稳定的网络环境中

  

### STOMP：Streaming Text Oriented Messaging Protocol

- 流文本定向消息协议。是一种`以纯文本为载体`的协议

- WebSocket 通信标准。在通常的发布－订阅语义之上，它通过 begin/publish/commit 序列以及 acknowledgement 机制来提供消息可靠投递

  

  ![这里写图片描述](https://img-blog.csdn.net/20180414112152821?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXBlaWZlbmczNTE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  Stomp Broker只是负责接受和存储客户端发来的消息、只是按照客户端要求的路径转发消息，只是管理客户端连接和订阅：它并不负责根据消息内容做任何业务处理

### AMQP协议 

**AMQP工作在七层/五层网络模型的应用层，是一个典型的应用层协议，基本传输协议还是基于 TCP/IP**



![amqp](C:\Users\76111\Desktop\amqp.png)

- 不仅仅是发布-订阅，支持多种不同的消息和存储的模式
- 消费端限流、横向扩展、削峰、路由策略、任务分配策略等

#### 路由策略

* **Direct路由**
* **Fanout路由**
* **Topic路由**
  * param.#：param.text √， param.text.text ×
  * param.*：param.text.text √

#### 可靠性投递

* 消息落库，对消息状态进行打标

  ![可靠性投递](H:\可靠性投递.PNG)

* 消息的延迟投递，做二次确认，回调检查

#### 消费端限流

* 手动ACK
* perfetchCount：5

#### 死信队列

* 消息被拒绝
* 消息TTL过期
* 队列达到最大长度



补充：

*AMQP VS Http*

*会话和数据传输*

*Spring Cloud Bus*

*Event-Listener*