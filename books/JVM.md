# JVM

前沿技术

| 技术                  | 技术                               |
| --------------------- | ---------------------------------- |
| 阿里多租户JVM(赵海平) | 每租户单空间<br />session based GC |

 
### G1

|              |                                                              |
| ------------ | ------------------------------------------------------------ |
| 追求吞吐量   | 100cpu --> 99 app + 1 GC                                     |
| 追求相应时间 | -XX:MaxGcPauseMillis 200                                     |
| 灵活         | 分Region回收<br />优先回收花费时间少、垃圾比例高的Region<br />动态调整新老年代比例【G1预测停顿时间的基准】 |

#### card table

由于做YGC时，会扫描整个OLD区，效率低。如果一个OLD区的CardTable有对象指向Y区，则设置为Dirty，下次扫描时，只需要扫描Dirty Card。DirtyCard用BitMap实现

#### CSet

一组可被回收的分区的集合（所有需要回收的card都会被记录到这里面来），待回收线程回收

#### RSet：RememberedSet【重要】

记录了其他Region中的对象到本Region的引用

**使得GC不需要扫描整个堆找到谁引用了当前分区中的对象 **

**详细到对象级别**

#### ZGC：颜色指针



#### 会产生FGC

应对方式：

1. 扩大内存
2. 提高CPU性能（回收的快）
3. 降低MixedGC触发的阈值，让MixedGC提高发生（默认是45%）

#### MixedGC

相当于一个CMS

### 并发标记算法

染色法并发标记的问题：引用的同时，其他引用消失

* incremental update
  * 增量更新，关注引用的增加：将黑色重新标记为灰色，下次重新扫描属性

**CMS使用 incremental update**

* SATB: snapshot at the beginning：关注引用的删除
  * 当 B-->D消失时，要把这个引用推到GC的堆栈，保证D还能被GC扫描到

**G1使用 SATB**，为什么？

有Rset的存在，不需要扫描整个堆查找指向白色的引用