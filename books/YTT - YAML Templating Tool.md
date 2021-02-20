<h1 align="center">
    YTT - YAML Templating Tool
</h1>

* 面向结构
  * ytt模板是由节点（例如地图和数组）组成的普通YAML文档。ytt允许您在这些节点上设置值并附加语句（例如if和for循环）。这样就消除了诸如手动转义之类的文本插入问题，并使结构易于重用，从而提高了可读性
* 模块化配置
  * 将ytt编织到自己的配置中，将值外部化为变量，将重复的代码段提取为函数，并使整个部分成为条件部分。使用ytt叠加层在任何配置之上打补丁
* 确定性
  * ytt执行环境是无密封且无副作用的，无法访问文件系统，网络，时间，随机性或操作系统接口。这样可以确保模板使用相同的输入产生相同的输出



* Feature
  * 集成 starlark-go：https://github.com/google/starlark-go
  * Overlays：*Patch YAML structures*
  * Any Yaml

