include_regular_expression
===

include_regular_expression 设置用于依赖性检查的正则表达式。

  include_regular_expression(regex_match [regex_complain])
　　设置依赖性检查的正则表达式。这有匹配正则表达式regex_match的文件会成为依赖性跟踪的对象。只有匹配regex_complain的文件，在找不到它们的时候才会给出警告（标准头文件不会被搜索）。正则表达式的默认值是：

  　　regex_match    = "^.*$" (匹配所有文件)
　　　regex_complain = "^$" (仅匹配空字符串)

