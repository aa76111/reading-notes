[TOC]

# ASM

**asm是assembly的缩写，是汇编的称号，对于java而言，asm就是字节码级别的编程。**



## 字节码简介

### 什么是字节码

由十六进制值组成，而JVM以两个十六进制值为一组，即以字节为单位进行读取

![图1 Java运行示意图](https://p0.meituan.net/travelcube/110b593ecf53866e0dec8df3618b0443257977.png)

### 字节码结构

![1628851184691](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1628851184691.png)



![图3 JVM规定的字节码结构](https://p0.meituan.net/travelcube/393097261d80d730f434561157e219c657820.png)

### 操作数栈和字节码



## 字节码增强

### ASM

**可以直接生产 class文件，也可以修改类行为**

* AOP
* 热部署
* 修改jar包
* ......

#### 访问者模式：稳定的数据结构和易变操作的解耦

* 封装某些作用于某种数据结构中各元素的操作

* 它可以在不改变数据结构的前提下定义作用于这些数据元素的新的操作

数据结构稳定不变，但对之的操作多变（jacoco打桩、代码混淆、链路追踪等等）。且这些操作，与对象的关系较弱，则可使用访问者模式，将这些操作封装到访问者中，避免操作污染对象。

![图17 ASM修改字节码](https://p0.meituan.net/travelcube/3c40b90c6d92499ad4c708162095fe3029983.png)

* classReader

  * 将字节数组或者 class 文件读入到内存当中，并以树的数据结构表示，树中的一个节点代表着 class 文件中的某个区域
  * 可以将 ClassReader 看作是 Visitor 模式中的访问者的实现类

* ClassVisitor（抽象类）

  * ClassReader 对象创建之后，调用 ClassReader#accept() 方法，传入一个 ClassVisitor 对象。在 ClassReader 中遍历树结构的不同节点时会调用 ClassVisitor 对象中不同的 visit() 方法，从而实现对字节码的修改。在 ClassVisitor 中的一些访问会产生子过程，比如 visitMethod 会产生 MethodVisitor 的调用，visitField 会产生对 FieldVisitor 的调用，用户也可以对这些 Visitor 进行自己的实现，从而达到对这些子节点的字节码的访问和修改。

  * 在 ASM 的访问者模式中，用户还可以提供多种不同操作的 ClassVisitor 的实现，并以责任链的模式提供给 ClassReader 来使用，而 ClassReader 只需要 accept 责任链中的头节点处的 ClassVisitor。

    ![1629107496131](C:\Users\76111\AppData\Roaming\Typora\typora-user-images\1629107496131.png)

* ClassWriter

  * ClassVisitor 的实现类，它是生成字节码的工具类，它一般是责任链中的最后一个节点，其之前的每一个 ClassVisitor 都是致力于对原始字节码做修改，而 ClassWriter 的操作则是老实得把每一个节点修改后的字节码输出为字节数组。

#### ASM工具

* [ASM ByteCode Outline](https://plugins.jetbrains.com/plugin/5918-asm-bytecode-outline)、Javassist

  **举个栗子（简单的方法前后AOP）**

## 运行时重载

### Instrument

### JVMTI & Agent & Attach API