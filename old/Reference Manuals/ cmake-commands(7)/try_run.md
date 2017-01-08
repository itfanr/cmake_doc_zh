try_run
===

try_run  尝试编译并运行某些代码。

  try_run(RUN_RESULT_VAR COMPILE_RESULT_VAR
          bindir srcfile [CMAKE_FLAGS <Flags>]
          [COMPILE_DEFINITIONS <flags>]
          [COMPILE_OUTPUT_VARIABLE comp]
          [RUN_OUTPUT_VARIABLE run]
          [OUTPUT_VARIABLE var]
          [ARGS <arg1> <arg2>...])
　　尝试编译一个源文件srcfile。通过变量COMPILE_RESULT_VAR返回TRUE或者FALSE来反应编译是否失败。如果构建出了可执行文件，但是不能运行，那么RUN_RESULT_VAR会被设置为FAILED_TO_RUN。COMPILE_OUTPUT_VARIABLE变量指定了一个变量，这个变量存储了构建步骤输出的信息。RUN_OUTPUT_VARIABLE指定了一个变量，这个变量存储了运行可执行文件时的输出。出于兼容性的考虑，OUTPUT_VARIABLE还会被支持，它包含了包含编译和运行阶段的输出信息。

交叉编译相关问题

　　当运行交叉编译时，第一步中编译出的可执行文件通常不能在编译宿主机上直接运行。try_run()函数会检查CMAKE_CROSSCOMPILING变量来检测CMake是否是交叉编译模式。如果是的话，CMake还是会尝试编译可执行文件，但是它不会尝试运行可执行文件。相反，他会创建一些cache变量，这些变量必须由用户填充，或者在某个CMake脚本中预先设置为那些在真实目标机平台上执行的结果。这些变量有：RUN_RESULT_VAR (解释参见上文)，以及如果使用了RUN_OUTPUT_VARIABLE (或者OUTPUT_VARIABLE) ，还有一个附加的cache变量RUN_RESULT_VAR__COMPILE_RESULT_VAR__TRYRUN_OUTPUT。该变量是为了保存执行过程中stdout和stderr的输出。

　　为了让交叉编译更加容易些，必要时再使用try_run命令。如果你使用了try_run命令，那么只有必要时才使用RUN_OUTPUT_VARIABLE（或者OUTPUT_VARIABLE）变量。在交叉编译时，使用这些变量需要cache变量必须被手动设置为可执行文件的输出。你也可以用if(CMAKE_CROSSCOMPILING)将try_run的调用“保护”起来，同时还要为这种情形给定一个易于预先设置的备选方案。

