configure_file
===

configure_file: 将一份文件拷贝到另一个位置并修改它的内容。

  configure_file(<input> <output>
                 [COPYONLY] [ESCAPE_QUOTES] [@ONLY])
　　将文件<input>拷贝到<output>然后替换文件内容中引用到的变量值。如果<input>是相对路径，它被评估的基础路径是当前源码路径。<input>必须是一个文件，而不是个路径。如果<output>是一个相对路径，它被评估的基础路径是当前二进制文件路径。如果<output>是一个已有的路径，那么输入文件将会以它原来的名字放到那个路径下。

　　该命令替换掉在输入文件中，以${VAR}格式或@VAR@格式引用的任意变量，如同它们的值是由CMake确定的一样。 如果一个变量还未定义，它会被替换为空。如果指定了COPYONLY选项，那么变量就不会展开。如果指定了ESCAPE_QUOTES选项，那么所有被替换的变量将会按照C语言的规则被转义。该文件将会以CMake变量的当前值被配置。如果指定了@ONLY选项，只有@VAR@格式的变量会被替换而${VAR}格式的变量则会被忽略。这对于配置使用${VAR}格式的脚本文件比较有用。任何类似于#cmakedefine VAR的定义语句将会被替换为#define VAR或者/* #undef VAR */，视CMake中对VAR变量的设置而定。任何类似于#cmakedefine01 VAR的定义语句将会被替换为#define VAR 1或#define VAR 0，视VAR被评估为TRUE或FALSE而定。

（configure_file的作用是让普通文件也能使用CMake中的变量。——译注）

