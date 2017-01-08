find_library
===

find_library 查找一个库文件

   find_library(<VAR> name1 [path1 path2 ...])
　　这是该命令的简写版本，在大多数场合下都已经够用了。它与命令find_library(<VAR> name1 [PATHS path1 path2 ...])等价。

   find_library(
             <VAR>
             name | NAMES name1 [name2 ...]
             [HINTS path1 [path2 ... ENV var]]
             [PATHS path1 [path2 ... ENV var]]
             [PATH_SUFFIXES suffix1 [suffix2 ...]]
             [DOC "cache documentation string"]
             [NO_DEFAULT_PATH]
             [NO_CMAKE_ENVIRONMENT_PATH]
             [NO_CMAKE_PATH]
             [NO_SYSTEM_ENVIRONMENT_PATH]
             [NO_CMAKE_SYSTEM_PATH]
             [CMAKE_FIND_ROOT_PATH_BOTH |
              ONLY_CMAKE_FIND_ROOT_PATH |
              NO_CMAKE_FIND_ROOT_PATH]
            )
　　该命令用来查找一个库文件。一个名为<VAR>的cache条目会被创建来存储该命令的结果。如果找到了该库文件，那么结果会存储在该变量里，并且搜索过程将不再重复，除非该变量被清空。如果没有找到，结果变量将会是<VAR>-NOTFOUND，并且在下次使用相同变量调用find_library命令时，搜索过程会再次尝试。在NAMES参数后列出的文件名是要被搜索的库名。附加的搜索位置在PATHS参数后指定。如果再HINTS或者PATHS字段中设置了ENV变量var，环境变量var将会被读取并从系统环境变量转换为一个cmake风格的路径list。例如，指定ENV PATH是获取系统path变量并将其转换为cmake的list的一种方式。在DOC之后的参数用来作为cache中的注释字符串。PATH_SUFFIXES选项指定了每个搜索路径下待搜索的子路径。

　　如果指定了NO_DEFAULT_PATH选项，那么搜索的过程中不会有其他的附加路径。如果没有指定该选项，搜索过程如下：

　　1、搜索cmake特有的cache变量指定的路径。这些变量是在用cmake命令行时，通过-DVAR=value指定的变量。如果指定了NO_CMAKE_PATH选项，这些路径会被跳过。搜索的路径还包括：

   对于每个在CMAKE_PREFIX_PATH中的<prefix>，路径<prefix>/lib 
   CMAKE_LIBRARY_PATH
   CMAKE_FRAMEWORK_PATH
　　2、搜索cmake特有的环境变量指定的路径。这些变量是用户的shell配置中设置的变量。如果指定了NO_CMAKE_ENVIRONMENT_PATH选项，这些路径会被跳过。搜索的路径还包括：

   对于每个在CMAKE_PREFIX_PATH中的<prefix>，路径<prefix>/lib 
   CMAKE_LIBRARY_PATH
   CMAKE_FRAMEWORK_PATH
　　3、搜索由HINTS选项指定的路径。这些路径是系统内省（introspection）估算出的路径，比如由另一个已经发现的库文件的地址提供的参考信息。硬编码的推荐路径应该通过PATHS选项指定。

　　4、查找标准的系统环境变量。如果指定了NO_SYSTEM_ENVIRONMENT_PATH选项，这些路径会被跳过。搜索的路径还包括：

   PATH
   LIB
　　5、查找在为当前系统的平台文件中定义的cmake变量。如果指定了NO_CMAKE_SYSTEM_PATH选项，该路径会被跳过。搜索的路径还包括：

   对于每个在CMAKE_SYSTEM_PREFIX_PATH中的<prefix>，路径<prefix>/lib 
   CMAKE_SYSTEM_LIBRARY_PATH
   CMAKE_SYSTEM_FRAMEWORK_PATH
　　6、搜索PATHS选项或者精简版命令指定的路径。这些通常是硬编码的推荐搜索路径。

　　在Darwin或者支持OS X 框架的系统上，cmake变量CMAKE_FIND_FRAMEWORK可以用来设置为空，或者下述值之一：

   "FIRST"  - 在标准库或头文件之前查找框架。在Darwin系统上这是默认选项。
   "LAST"   - 在标准库或头文件之后查找框架。
   "ONLY"   - 仅仅查找框架。
   "NEVER" - 从不查找框架。
　　在Darwin或者支持OS X Application Bundles的系统，cmake变量CMAKE_FIND_APPBUNDLE可以被设置为空或者下面这些值中的一个：

   "FIRST"  - 在标准库或头文件之前查找application bundles。在Darwin系统上这是默认选项。
   "LAST"   - 在标准库或头文件之后查找application bundles。
   "ONLY"   - 仅仅查找application bundles。
   "NEVER" - 从不查找application bundles。
　　CMake变量CMAKE_FIND_ROOT_PATH指定了一个或者多个优先于其他搜索路径的搜索路径。该变量能够有效地重新定位在给定位置下进行搜索的根路径。该变量默认为空。当使用交叉编译时，该变量十分有用：用该变量指向目标环境的根目录，然后CMake将会在那里查找。默认情况下，在CMAKE_FIND_ROOT_PATH中列出的路径会首先被搜索，然后是“非根”路径。该默认规则可以通过设置CMAKE_FIND_ROOT_PATH_MODE_LIBRARY做出调整。在每次调用该命令之前，都可以通过设置这个变量来手动覆盖默认行为。如果使用了NO_CMAKE_FIND_ROOT_PATH变量，那么只有重定位的路径会被搜索。

　　默认的搜索顺序的设计逻辑是按照使用时从最具体到最不具体。通过多次调用find_library命令以及NO_*选项，可以覆盖工程的这个默认顺序：

    find_library(<VAR> NAMES name PATHS paths... NO_DEFAULT_PATH)
   find_library(<VAR> NAMES name)
　　只要这些调用中的一个成功返回，结果变量就会被设置并且被存储到cache中；这样随后的调用都不会再行搜索。如果那找到的库是一个框架，VAR将会被设置为指向框架“<完整路径>/A.framework” 的完整路径。当一个指向框架的完整路径被用作一个库文件，CMake将使用-framework A，以及-F<完整路径>这两个选项将框架连接到目标上。

