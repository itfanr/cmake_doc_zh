try_compile
===

try_compile 尝试编译一些代码。

  try_compile(RESULT_VAR bindir srcdir
              projectName <targetname> [CMAKE_FLAGS <Flags>]
              [OUTPUT_VARIABLE var])
　　尝试编译一个程序。在这种格式时，srcdir路径下应该包含一个完整的CMake工程，包括CMakeLists.txt文件以及所有的源文件。在该命令运行完之后，路径bindir和srcdir不会被删除。如果指定了<target name>，那么CMake将只构建那个目标；否则，目标all或ALL_BUILD将会被构建。

  try_compile(RESULT_VAR bindir srcfile
              [CMAKE_FLAGS <Flags>]
              [COMPILE_DEFINITIONS <flags> ...]
              [OUTPUT_VARIABLE var]
              [COPY_FILE <filename> )
　　尝试编译一个srcfile。在这种情况下，用户仅仅需要提供源文件。CMake会创建合适的CMakeLists.txt文件来构建源文件。如果使用了COPY_FILE选项，编译出的文件将会被拷贝到给定的文件那里。

　　在这个版本里，所有在bindir/CMakeFiles/CMakeTmp文件夹下的文件，将会被自动清除；通过向CMake传递调试选项--debug-trycompile可以避免这个清除步骤。另外一些可以包含的额外标志有：INCLUDE_DIRECTORIES, LINK_DIRECTORIES, 和LINK_LIBRARIES。COMPILE_DEFINITIONS是通过-Ddefinations选项设置的预定义符号，这会传递到编译器命令行。try_compile命令在构建过程中伴随创建出的CMakeLists.txt看起来像这样：

  add_definitions( <expanded COMPILE_DEFINITIONS from calling cmake>)
  include_directories(${INCLUDE_DIRECTORIES})
  link_directories(${LINK_DIRECTORIES})
  add_executable(cmTryCompileExec sources)
  target_link_libraries(cmTryCompileExec ${LINK_LIBRARIES})
　　在该命令的这两种版本里，如果指定了OUTPUT_VARIABLE，那么构建过程的输出会存储到给定的变量里。编译成功或失败的结果，会通过RESULT_VAR返回。CMAKE_FLAGS可以用来向正在构建的CMake传递-DVAR:TYPE = VALUE 符号。


