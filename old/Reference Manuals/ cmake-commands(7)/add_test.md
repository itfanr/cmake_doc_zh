add_test
===

add_test 以指定的参数为工程添加一个测试。

  add_test(testname Exename arg1 arg2 ... )
　　如果已经运行过了ENABLE_TESTING命令，这个命令将为当前路径添加一个测试目标。如果ENABLE_TESTING还没有运行过，该命令啥事都不做。测试是由测试子系统运行的，它会以指定的参数执行Exename文件。Exename或者是由该工程构建的可执行文件，也可以是系统上自带的任意可执行文件（比如tclsh）。该测试会在CMakeList.txt文件的当前工作路径下运行，这个路径与二进制树上的路相对应。 

  add_test(NAME <name> [CONFIGURATIONS [Debug|Release|...]]
           COMMAND <command> [arg1 [arg2 ...]])
　　如果COMMAND选项指定了一个可执行目标（用add_executable创建），它会自动被在构建时创建的可执行文件所替换。如果指定了CONFIGURATIONS选项，那么该测试只有在列出的某一个配置下才会运行。

　　在COMMAND选项后的参数可以使用“生成器表达式”，它的语法是"$<...>"。这些表达式会在构建系统生成期间，以及构建配置的专有信息的产生期间被评估。合法的表达式是：

  $<CONFIGURATION>          = 配置名称
  $<TARGET_FILE:tgt>        = 主要的二进制文件(.exe, .so.1.2, .a)
  $<TARGET_LINKER_FILE:tgt> = 用于链接的文件(.a, .lib, .so)
  $<TARGET_SONAME_FILE:tgt> = 带有.so.的文件(.so.3)
　　其中，"tgt"是目标的名称。目标文件表达式TARGET_FILE生成了一个完整的路径，但是它的_DIR和_NAME版本可以生成目录以及文件名部分：

  $<TARGET_FILE_DIR:tgt>/$<TARGET_FILE_NAME:tgt>
  $<TARGET_LINKER_FILE_DIR:tgt>/$<TARGET_LINKER_FILE_NAME:tgt>
  $<TARGET_SONAME_FILE_DIR:tgt>/$<TARGET_SONAME_FILE_NAME:tgt>
　　用例：

1  add_test(NAME mytest
2            COMMAND testDriver --config $<CONFIGURATION>
3                               --exe $<TARGET_FILE:myexe>)
　　这段代码创建了一个名为mytest的测试，它执行的命令是testDriver工具，传递的参数包括配置名，以及由目标生成的可执行文件myexe的完整路径。

