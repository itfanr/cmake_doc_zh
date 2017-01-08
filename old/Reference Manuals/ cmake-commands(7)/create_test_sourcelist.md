create_test_sourcelist
===

create_test_sourcelist: 为构建测试程序创建一个测试驱动器和源码列表。

  create_test_sourcelist(sourceListName driverName
                         test1 test2 test3
                         EXTRA_INCLUDE include.h
                         FUNCTION function)
　　测试驱动器是一个将很多小的测试代码连接为一个单一的可执行文件的程序。这在为了缩减总的需用空间而用很多大的库文件去构建静态可执行文件的时候，特别有用。构建测试驱动所需要的源文件列表会在变量sourceListName中。DriverName变量是测试驱动器的名字。其它的参数还包括一个测试源代码文件的清单，中间可以用分号隔开。每个测试源码文件中应该有一个与去掉扩展名的文件名同名的函数（比如foo.cxx 文件里应该有int foo(int, char*[]);）（和main的函数签名一样——译注）。DriverName可以在命令行中按名字调用这些测试中的每一个。如果指定了EXTRA_INCLUDE，那么它后面的参数（即include.h——译注）会被包含到生成的文件里。如果指定了FUNCTION选项，那么它后面的参数（即function——译注）会被认为是一个函数名，传递给它的参数是一个指向argc的指针和argv。这个选项可以用来为每个测试函数添加额外的命令行参数处理过程。CMake变量CMAKE_TESTDRIVER_BEFORE_TESTMAIN用来设置在调用测试的main函数之前调用的代码。

