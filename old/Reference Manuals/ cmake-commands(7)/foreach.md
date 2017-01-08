foreach
===

foreach  对一个list中的每一个变量执行一组命令。

  foreach(loop_var arg1 arg2 ...)
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  endforeach(loop_var)
　　所有的foreach和与之匹配的endforeach命令之间的命令会被记录下来而不会被调用。等到遇到endforeach命令时，先前被记录下来的命令列表中的每条命令都会为list中的每个变量调用一遍。在每次迭代中，循环变量${loop_var}将会被设置为list中的当前变量值。

  foreach(loop_var RANGE total)
  foreach(loop_var RANGE start stop [step])
　　foreach命令也可以遍历一个人为生成的数据区间。遍历的方式有三种：

　　*如果指定了一个数字，区间是[0, total]。

　　*如果指定了两个数字，区间将会是第一个数字到第二个数字。

　　*第三个数字是从第一个数字遍历到第二个数字时的步长。

  foreach(loop_var IN [LISTS [list1 [...]]]
                      [ITEMS [item1 [...]]])
　　该命令的含义是：精确遍历一个项组成的list。LISTS选项后面是需要被遍历的list变量的名字，包括空元素（一个空字符串是一个零长度list）。ITEMS选项结束了list参数的解析，然后在迭代中引入所有在其后出现的项。（猜测是用list1中的项item1，依次类推，为循环变量赋值。——译注）

