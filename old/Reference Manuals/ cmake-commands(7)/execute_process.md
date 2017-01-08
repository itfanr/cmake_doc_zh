execute_process
===

execute_process 执行一个或更多个子进程。

  execute_process(COMMAND <cmd1> [args1...]]
                  [COMMAND <cmd2> [args2...] [...]]
                  [WORKING_DIRECTORY <directory>]
                  [TIMEOUT <seconds>]
                  [RESULT_VARIABLE <variable>]
                  [OUTPUT_VARIABLE <variable>]
                  [ERROR_VARIABLE <variable>]
                  [INPUT_FILE <file>]
                  [OUTPUT_FILE <file>]
                  [ERROR_FILE <file>]
                  [OUTPUT_QUIET]
                  [ERROR_QUIET]
                  [OUTPUT_STRIP_TRAILING_WHITESPACE]
                  [ERROR_STRIP_TRAILING_WHITESPACE])
　　运行一条或多条命令，使得前一条命令的标准输出以管道的方式成为下一条命令的标准输入。所有进程公用一个单独的标准错误管道。如果指定了WORKING_DIRECTORY选项，后面的路径选项将会设置为子进程的当前工作路径。如果指定了TIMEOUT选项，如果子进程没有在指定的秒数（允许分数）里完成，子进程会自动终止。如果指定了RESULT_VARIABLE选项，该变量将保存为正在运行的进程的结果；它可以是最后一个子进程的整数返回代码，也可以是一个描述错误状态的字符串。如果指定了OUTPUT_VARIABLE或者ERROR_VARIABLE，后面的变量将会被分别设置为标准输出和标准错误管道的值。如果两个管道都是用了相同的变量，它们的输出将会按产生的顺序被合并。如果指定了INPUT_FILE，OUTPUT_FILE 或 ERROR_FILE选项，其后的文件将会分别被附加到第一个进程的标准输入、最后一个进程的标准输出，或者所有进程的标准错误管道上。如果指定了OUTPUT_QUIET后者ERROR_QUIET选项，那么标准输出或标准错误的结果将会被静静的忽略掉。如果为同一个管道指定了多于一个的OUTPUT_*或ERROR_* 选项，优先级是没有指定的。如果没有指定OUTPUT_*或者ERROR_*选项，输出将会与CMake进程自身对应的管道共享。

　　execute_process命令是exec_program命令的一个较新的功能更加强大的版本。但是为了兼容性的原因，旧的exec_program命令还会继续保留。

