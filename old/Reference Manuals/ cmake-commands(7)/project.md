project
===

project  为整个工程设置一个工程名。

  project(<projectname> [languageName1 languageName2 ... ] )
　　为本工程设置一个工程名。而且，该命令还将变量<projectName>_BINARY_DIR和<projectName>_SOURCE_DIR设置为对应值。后面的可选项还可以让你指定你的工程可以支持的语言。比如CXX(即C++)，C，Fortran，等等。在默认条件下，支持C和CXX语言。例如，如果你没有C++编译器，你可以通过列出你想要支持的语言，例如C，来明确地禁止对它的检查。使用特殊语言"NONE"，针对任何语言的检查都会被禁止。


