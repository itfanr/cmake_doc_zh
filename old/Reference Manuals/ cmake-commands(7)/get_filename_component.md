get_filename_component
===

get_filename_component 得到一个完整文件名中的特定部分。

  get_filename_component(<VAR> FileName
                         PATH|ABSOLUTE|NAME|EXT|NAME_WE|REALPATH
                         [CACHE])
　　将变量<VAR>设置为路径(PATH)，文件名(NAME)，文件扩展名(EXT)，去掉扩展名的文件名(NAME_WE)，完整路径(ABSOLUTE)，或者所有符号链接被解析出的完整路径(REALPATH)。注意，路径会被转换为Unix的反斜杠(/)，并且没有结尾的反斜杠。该命令已经考虑了最长的文件扩展名。如果指定了CACHE选项，得到的变量会被加到cache中。

  get_filename_component(<VAR> FileName
                         PROGRAM [PROGRAM_ARGS <ARG_VAR>]
                         [CACHE])
　　在FileName中的程序将会在系统搜索路径中被查找，或者是一个完整路径。如果与PRPGRAM一起给定了PROGRAM_ARGS选项，那么任何在Filename字符串中出现的命令行中选项将会从程序名中分割出来并存储在变量<ARG_VAR>中。这可以用来从一个命令行字符串中分离程序名及其选项。

