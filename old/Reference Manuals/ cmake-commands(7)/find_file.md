find_file
===

find_file 查找一个文件的完整路径。

   find_file(<VAR> name1 [path1 path2 ...])
　　这是该命令的精简格式，对于大多数场合它都足够了。它与命令find_file(<VAR> name1 [PATHS path1 path2 ...])是等价的。

   find_file(
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
　　这条命令用来查找指定文件的完整路径。一个名字是<VAR>的缓存条目（参见CMakeCache.txt的介绍——译注）变量会被创建，用来存储该命令的结果。如果发现了文件的一个完整路径，该结果会被存储到该变量里并且搜索过程不会再重复，除非该变量被清除。如果什么都没发现，搜索的结果将会是<VAR>-NOTFOUND；并且在下一次以相同的变量调用find_file时，该搜索会重新尝试。被搜索的文件的文件名由NAMES选项后的名字列表指定。附加的其他搜索位置可以在PATHS选项之后指定。如果ENV var在HINTS或PATHS段中出现，环境变量var将会被读取然后被转换为一个系统级环境变量，并存储在一个cmake风格的路径list中。比如，使用ENV PATH将会将系统的path变量列出来。在DOC之后的变量将会用于cache中的文档字符串（documentation string）。PATH_SUFFIXES指定了在每个搜索路径下的需要搜索的子路径。

　　如果指定了NO_DEFAULT_PATH选项，那么在搜索时不会附加其它路径。如果没有指定NO_DEFAULT_PATH选项，搜索过程如下：

　　1、在cmake特有的cache变量中指定的搜索路径搜索。这些路径用于在命令行里用-DVAR=value被设置。如果使用了NO_CMAKE_PATH选项，该路径会被跳过。（此句翻译可能有误——译注。）搜索路径还包括：    

　　对于每个在CMAKE_PREFIX_PATH中的路径<prefix>，<prefix>/include  
　　变量：CMAKE_INCLUDE_PATH
　　变量：CMAKE_FRAMEWORK_PATH

　　2、在cmake特定的环境变量中指定的搜索路径搜索。该路径会在用户的shell配置中被设置。如果指定了NO_CMAKE_ENVIRONMENT_PATH选项，该路径会被跳过。搜索路径还包括：

　　对于每个在CMAKE_PREFIX_PATH中的路径<prefix>，<prefix>/include  
　　变量：CMAKE_INCLUDE_PATH
　　变量：CMAKE_FRAMEWORK_PATH

　　3、由HINTS选项指定的搜索路径。这些路径是由系统内省（introspection）时计算出来的路径，比如已经发现的其他项的位置所提供的痕迹。硬编码的参考路径应该使用PATHS选项指定。（HINTS与PATHS有何不同？比后者的优先级高？有疑问。——译注）

　　4、搜索标准的系统环境变量。如果指定NO_SYSTEM_ENVIRONMENT_PATH选项，搜索路径将跳过其后的参数。搜索路径包括环境变量PATH个INCLUDE。

　　5、查找在当前系统的平台文件中定义的cmake变量。如果指定了NO_CMAKE_SYSTEM_PATH选项，该路径会被跳过。其他的搜索路径还包括：

　　对于每个在CMAKE_PREFIX_PATH中的路径<prefix>，<prefix>/include  
　　变量：CMAKE_SYSTEM_INCLUDE_PATH
　　变量：CMAKE_SYSTEM_FRAMEWORK_PATH

　　6、搜索由PATHS选项指定的路径或者在命令的简写版本中指定的路径。这一般是一些硬编码的参考路径。在Darwin后者支持OS X框架的系统上，cmake变量CMAKE_FIND_FRAMWORK可以设置为空或者下述值之一：

   "FIRST"  - 在标准库或者头文件之前先查找框架。对于Darwin系统，这是默认的。
   "LAST"   - 在标准库或头文件之后再查找框架。
   "ONLY"   - 只查找框架。
   "NEVER" - 从不查找框架。
　　在Darwin或者支持OS X Application Bundles的系统上，cmake变量CMAKE_FIND_APPBUNDLE可以被设置为空，或者下列值之一：

   "FIRST"  - 在标准程序之前查找application bundles，这也是Darwin系统的默认选项。
   "LAST"   - 在标准程序之后查找application bundlesTry。
   "ONLY"   - 只查找application bundles。
   "NEVER" - 从不查找application bundles。
　　CMake的变量CMAKE_FIND_ROOT_PATH指定了一个或多个在所有其它搜索路径之前的搜索路径。该选项很有效地将给定位置下的整个搜索路径的最优先路径进行了重新指定。默认情况下，它是空的。当交叉编译一个指向目标环境下的根目录中的目标时，CMake也会搜索那些路径；该变量这时显得非常有用。默认情况下，首先会搜索在CMAKE_FIND_ROOT_PATH变量中列出的路径，然后才是非根路径。设置CMAKE_FIND_ROOT_PATH_MODE_INCLUDE变量可以调整该默认行为。该行为可以在每次调用时被手动覆盖。通过使用CMAKE_FIND_ROOT_PATH_BOTH变量，搜索顺序将会是上述的那样。如果使用了NO_CMAKE_FIND_ROOT_PATH变量，那么CMAKE_FIND_ROOT_PATH将不会被用到。如果使用了ONLY_CMAKE_FIND_ROOT_PATH变量，那么只有CMAKE_FIND_ROOT_PATH中的路径（即re-rooted目录——译注）会被搜索。

　　一般情况下，默认的搜索顺序是从最具体的路径到最不具体的路径。只要用NO_*选项多次调用该命令，工程就可以覆盖该顺序。
     find_file(<VAR> NAMES name PATHS paths... NO_DEFAULT_PATH)
     find_file(<VAR> NAMES name)

　　只要这些调用中的一个成功了，返回变量就会被设置并存储在cache中；然后该命令就不会再继续查找了。

