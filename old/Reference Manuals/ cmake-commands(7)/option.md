option
===

option 为用户提供一个可选项。

  option(<option_variable> "描述选项的帮助性文字" [initial value])
　　该命令为用户提供了一个在ON和OFF中做出选择的选项。如果没有指定初始值，将会使用OFF作为初值。如果有些选项依赖于其他选项的值，参见CMakeDependentOption模块的帮助文件。

