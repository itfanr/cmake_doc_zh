enable_language
===

enable_language 支持某种语言（CXX/C/Fortran/等）

  enable_language(languageName [OPTIONAL] )
　　该命令打开了CMake对参数中指定的语言的支持。这与project命令相同，但是不会创建任何project命令会产生的额外变量。可以选用的语言的类型有CXX，C，Fortran等。如果指定了OPTIONAL选项，用CMAKE_<languageName>_COMPILER_WORKS变量来判断该语言是否被成功支持。

