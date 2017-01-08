set
===

set 将一个CMAKE变量设置为给定值。

  set(<variable> <value> [[CACHE <type> <docstring> [FORCE]] | PARENT_SCOPE])
　　将变量<variable>的值设置为<value>。在<variable>被设置之前，<value>会被展开。如果有CACHE选项，那么<variable>就会添加到cache中；这时<type>和<docstring>是必需的。<type>被CMake GUI用来选择一个窗口，让用户设置值。<type>可以是下述值中的一个：

  FILEPATH = 文件选择对话框。
  PATH     = 路径选择对话框。
  STRING   = 任意的字符串。
  BOOL     = 布尔值选择复选框。
  INTERNAL = 不需要GUI输入端。(适用于永久保存的变量)。
　　如果<type>是内部的(INTERNAL)，那么<value>总是会被写入到cache中，并替换任何已经存在于cache中的值。如果它不是一个cache变量，那么这个变量总是会写入到当前的makefile中。FORCE选项将覆盖cache值，从而去掉任何用户带来的改变。

　　如果指定了PARENT_SCOPE选项，变量<variable>将会被设置为当前作用域之上的作用域中。每一个新的路径或者函数都可以创建一个新作用域。该命令将会把一个变量的值设置到父路径或者调用函数中（或者任何类似的可用的情形中。）

　　如果没有指定<value>，那么这个变量就会被撤销而不是被设置。另见：unset()命令。

  set(<variable> <value1> ... <valueN>)
　　在这种情形下，<variable>被设置为一个各个值之间由分号分隔的list。

　　<variable>可以是环境变量，比如：

  set( ENV{PATH} /home/martink )
　　在这种情形下，环境变量将会被设置。

