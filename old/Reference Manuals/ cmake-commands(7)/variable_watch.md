variable_watch
===

variable_watch  监视CMake变量的改变。

  variable_watch(<variable name> [<command to execute>])
　　如果给定的变量发生了变化，关于正在被改写的变脸的消息会被打印出来。如果指定了command选项，这条命令会被执行。这条命令会接受这样的参数：COMMAND(<variable> <access> <value> <current list file> <stack>)

