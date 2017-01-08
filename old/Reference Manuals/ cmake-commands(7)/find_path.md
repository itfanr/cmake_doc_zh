find_path
===

find_path 搜索包含某个文件的路径

   find_path(<VAR> name1 [path1 path2 ...])
　　在多数情况下，使用上述的精简命令格式就足够了。它与命令find_path(<VAR> name1 [PATHS path1 path2 ...])等价。

   find_path(
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
　　该命令用于给定名字文件所在的路径。一条名为<VAR>的cache条目会被创建，并存储该命令的执行结果。如果在某个路径下发现了该文件，该结果会被存储到该变量中；除非该变量被清除，该次搜索不会继续进行。如果没有找到，存储的结果将会是<VAR>-NOTFOUND，并且当下一次以相同的变量名调用find_path命令时，该命令会再一次尝试搜索该文件。需要搜索的文件名通过在NAMES选项后面的列出来的参数来确定。附加的搜索位置可以在PATHS选项之后指定。如果在PATHS或者HINTS命令中还指定了ENV var选项，环境变量var将会被读取并从一个系统环境变量转换为一个cmake风格的路径list。比如，ENV PATH是列出系统path变量的一种方法。参数DOC将用来作为该变量在cache中的注释。PATH_SUFFIXES指定了在每个搜索路径下的附加子路径。

　　如果指定了NO_DEFAULT_PATH选项，那么没有其它附加的路径会被加到搜索过程中。如果并未指定NO_DEFAULT_PATH选项，搜索的过程如下：

　　1、搜索cmake专有的cache变量中的路径。这种用法是为了在命令行中用选项-DVAR=value指定搜索路径。如果指定了NO_CMAKE_PATH选项，该路径会被跳过。搜索路径还包括：

   对于每个在CMAKE_PREFIX_PATH中的<prefix>/，路径<prefix>/include 

   CMAKE_INCLUDE_PATH
   CMAKE_FRAMEWORK_PATH
　　2、搜索cmake专有的环境变量中指定的路径。这种用法是为了在用户的shell配置中设置指定的搜索路径。如果指定了NO_CMAKE_ENVIRONMENT_PATH选项，该路径会被跳过。搜索路径还包括：

   对于每个在CMAKE_PREFIX_PATH中的<prefix>/，路径<prefix>/include
   CMAKE_INCLUDE_PATH
   CMAKE_FRAMEWORK_PATH
　　3、搜索由HINTS选项指定的路径。这些路径应该是由系统内省时计算得出的路径，比如由其它已经发现的项目提供的线索。硬编码的参考路径应该在PATHS选项中指定。

　　4、搜索标准的系统环境变量。通过指定选项NO_SYSTEM_ENVIRONMENT_PATH可以跳过搜索环境变量。搜索的路径还包括：

    PATH
   INCLUDE
　　5、查找在为当前系统的平台文件中定义的cmake变量。如果指定了NO_CMAKE_SYSTEM_PATH选项，该路径会被跳过。搜索的路径还包括：

   对于每个在CMAKE_SYSTEM_PREFIX_PATH中的<prefix>，路径<prefix>/include 
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

　　默认的搜索顺序的设计逻辑是按照使用时从最具体到最不具体的路径。通过多次调用find_path命令以及NO_*选项，可以覆盖工程的这个默认顺序：

     find_path(<VAR> NAMES name PATHS paths... NO_DEFAULT_PATH)
    find_path(<VAR> NAMES name)
　　只要这些调用中的一个成功返回，结果变量就会被设置并且被存储到cache中；这样随后的调用都不会再行搜索。在搜索框架时，如果以A/b.h的格式指定文件，那么该框架搜索过程会搜索A.framework/Headers/b.h。如果找到了该路径，它将会被设置为框架的路径。CMake将把它转换为正确的-F选项来包含该文件。

===========================分==割==线=========================

最近工作比较忙，所以翻译没以前那么勤了，翻译的词句也只是保证能够勉强看懂而已。以后有时间了一定回过头来认真修订下，统一风格和措辞。


