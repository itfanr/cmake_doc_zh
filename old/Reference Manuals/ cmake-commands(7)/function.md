function
===

function  开始记录一个函数，为以后以命令的方式调用它做准备。

  function(<name> [arg1 [arg2 [arg3 ...]]])
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  endfunction(<name>)
　　定义一个名为<name>的函数，它以arg1 arg2 arg3 (...)为参数。在function之后，对应的endfunction之前列出的命令，在函数被调用之前，是不会被调用的。当函数被调用时，在函数中记录的那些命令首先会用传进去的参数替换掉形参（${arg1}）；然后跟正常命令一样去调用这些命令。除了形参，你还可以引用这些变量：ARGC为传递给函数的变量个数，ARGV0 ARGV1 ARGV2 ...表示传到函数中的实参值。这些变量为编写可选参数函数提供了便利。此外，ARGV保留了一个该函数所有实参的list，ARGN保留了函数形参列表以后的所有参数列表。

　　参见cmake_policy()命令文档中function内部策略行为的相关行为。

