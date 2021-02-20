[TOC]

| 正则网站              |
| --------------------- |
| https://regex101.com/ |
| https://tool.oschina.net/regex/ |
| http://tool.chinaz.com/regex/ |
| https://www.w3cschool.cn/tools/index?name=re |
| https://c.runoob.com/front-end/854 |
| http://tools.haokh.net/Regex |
| Windows 上推荐：RegexBuddy |
| Mac上推荐：Expressions |

例子:

* 提取出文章中所有的单词(引号内的算一个单词)

  * we found "the little cat" is in the hat, we like "the little cat"

  * ```
    \w+|"[^"]+"(?#引号里面是非引号出现一到多次)
    \w+|".+?"(?#量词后面添加 "?", 代表非贪婪匹配)
    第一种好: 不会出现点号将引号匹配上，再吐出的问题
    ```

* 连续单词去重

  * the little cat cat is in the hat hat hat, we like it

  * ```
    (\w+)(\s\1)+
    ```

* 日期的分组与替换

  * 2020-08-31 20:00:00

  * ```
    分组: ((\d{4})-(\d{2})-(\d{2})) ((\d{2}):(\d{2}):(\d{2}))
    替换: 日期:\1, 时间:\5, \2年\3月\4日 \6时\7分\8秒
    ```

* 提取 html便签里的内容(有换行符)

  * ```
    (?si)<head>(.*)<\/head>
    ```

* 连续单词去重进阶

  * the little cat cat2 is in the hat hat2, we like it

  * ```
    (\w+)(?:\s+\b\1\b)+
    ```
  
* 正则优化示例

  * ```
    将它进行优化:
    ^([A-Za-z0-9._()&'\- ]|[aAàÀảẢãÃáÁạẠăĂằẰẳẲẵẴắẮặẶâÂầẦẩẨẫẪấẤậẬbBcCdDđĐeEèÈẻẺẽẼéÉẹẸêÊềỀểỂễỄếẾệỆfFgGhHiIìÌỉỈĩĨíÍịỊjJkKlLmMnNoOòÒỏỎõÕóÓọỌôÔồỒổỔỗỖốỐộỘơƠờỜởỞỡỠớỚợỢpPqQrRsStTuUùÙủỦũŨúÚụỤưƯừỪửỬữỮứỨựỰvVwWxXyYỳỲỷỶỹỸýÝỵỴzZ])+$
    匹配字符串: this is a cat, cat
    ```

  * 

# RegExp

## 元字符

![img](https://static001.geekbang.org/resource/image/ff/52/ffd9cd49a79599e7efa4aba069faee52.png)

## 量词与贪婪

![img](https://static001.geekbang.org/resource/image/1a/75/1ad3eb0d011ba4fc972b9e5191a9f275.png)

### 贪婪匹配（Greedy）

![img](https://static001.geekbang.org/resource/image/a7/ca/a7d62eee986938327d31e170cdd3caca.jpg)

![img](https://static001.geekbang.org/resource/image/63/97/63e5c750b66f6eb914c73befdba43f97.jpg)

### 非贪婪匹配（Lazy）

![img](https://static001.geekbang.org/resource/image/3f/d1/3f95a3648980c1eb3c550fb34b46fad1.png)

![img](https://static001.geekbang.org/resource/image/40/79/40c03d7a2cb990b35e4801589eca1379.png)

### 独占模式（Possessive)

不管是贪婪模式，还是非贪婪模式，都需要发生回溯才能完成相应的功能

独占模式: 类似贪婪匹配，但匹配过程不会发生回溯

![img](https://static001.geekbang.org/resource/image/1d/b7/1dbf7d9fed42390edb3bf9ef9e0da7b7.jpg)

## 分组与引用

### 不保存子组

​	括号里面使用 ?: 不保存子组

![img](https://static001.geekbang.org/resource/image/d6/18/d6a3d486a8c575bc1961b7db5a153d18.png)

### 括号嵌套

![img](https://static001.geekbang.org/resource/image/08/40/083b6a8af68f56f3120b7c8875329340.png)

### 命名分组

​	格式为(?P<分组名>正则)。

### 分组引用

![img](https://static001.geekbang.org/resource/image/c4/94/c4eef43e2ccf55978b949a194a175594.jpg)

## 匹配模式

![img](https://static001.geekbang.org/resource/image/f3/a5/f36c2bca74f9bfcc54bb3e4ed53d4aa5.png)

### 不区分大小写模式（Case-Insensitive）: (?i)

### 点号通配模式（Dot All）[单行匹配]: (?s)

### 多行匹配模式（Multiline）: (?m)

```
通常情况下，^匹配整个字符串的开头，$ 匹配整个字符串的结尾。多行匹配模式改变的就是 ^ 和 $ 的匹配行为
```

点号通配: 将多行的字符串看为一行字符串进行匹配

多行匹配: 每一行每一行都进行匹配

![img](https://static001.geekbang.org/resource/image/e3/19/e3bf8bd8f9d594472a940d4a7e4f2f19.png)

## 断言

![img](https://static001.geekbang.org/resource/image/54/4e/54f61311f5fd506e2822992500fadf4e.png)

### 单词边界（Word Boundary）:  \b

![img](https://static001.geekbang.org/resource/image/4d/11/4d6c0dc075aebb6023ebcd791e787d11.jpg)

### 环视（ Look Around)

![img](https://static001.geekbang.org/resource/image/00/dd/00e823943baa33cd8a5e4690cfe44edd.png)

左尖括号代表看左边，没有尖括号是看右边，感叹号是非的意思

![img](https://static001.geekbang.org/resource/image/3c/81/3cc80c66f73c3192e05a65b6b7abd181.png)

环视是表示对文本左右环境的要求，即环视只匹配位置，不匹配文本内容



## 转义

![img](https://static001.geekbang.org/resource/image/54/4e/54f61311f5fd506e2822992500fadf4e.png)

## 流派

POSIX 流派 与 PCRE 流派

## Unicode

## 原理及优化

![img](https://static001.geekbang.org/resource/image/bd/0b/bddcyy348af2539aeedd72a7ebe5390b.png)

### 有穷状态自动机

* 一个系统具有有穷个状态，不同的状态代表不同的意义
* 自动机是指系统可以根据相应的条件，在不同的状态下进行转移
* 从一个初始状态，根据对应的操作（比如录入的字符集）执行状态转移，最终达到终止状态（可能有一到多个终止状态）

有穷自动机的具体实现称为正则引擎，主要有 DFA 和 NFA 两种，其中 NFA 又分为传统的 NFA 和 POSIX NFA

* DFA：确定性有穷自动机（Deterministic finite automaton）
* NFA：非确定性有穷自动机（Non-deterministic finite automaton）

```
for example
字符串: abbbba
正则: a(?:bb)+a
```

![img](https://static001.geekbang.org/resource/image/e3/4f/e3e052fac55209937cfd20ab5117f24f.png)

### DFA& NFA 工作机制

* NFA 引擎的工作方式是，先看正则，再看文本，而且以正则为主导
  * 以正则为主导，反复测试字符串，这样字符串中同一部分，有可能被反复测试很多次
* DFA 会先看文本，再看正则表达式，是以文本为主导的

### POSIX NFA 与 NFA

* NFA: “急于”报告匹配结果，找到第一个匹配上的就返回 [左匹配]
* POSIX NFA: 会尽可能找最长的，如果分支一样长，以最左边的为准

### 优化

* 正则
  * 对于多个分支, 提取公共的判断
  * 使用独占模式 (需要考虑)
  * 将多个分支是用 [] 的形式合并为一个分支
  * 准确表示匹配范围
  * 出现可能性大的放左边
  * 慎用嵌套的子组重复, 如: (.*)*

