include_directories
===

include_directories 为构建树添加包含路径。

  include_directories([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
　　将给定的路径添加到编译器搜索包含文件（.h文件）的路径列表中。缺省情况下，该路径会被附加在当前路径列表的后面。这种缺省行为可以通过设置CMAKE_include_directories_BEFORE变量为ON被改变。通过将该变量改变为BEFORE或AFTER，你可以在追加和附加在前端这两种方式中选择，而不用理会缺省设置。如果指定了SYSTEM选项，编译器将会认为该路径是某种平台上的系统包含路径。

