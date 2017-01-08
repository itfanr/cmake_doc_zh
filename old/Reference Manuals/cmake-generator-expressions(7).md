cmake-generator-expressions(7)
===

# cmake-generator-expressions(7)

标签（空格分隔）： cmake

---

##引言

生成器表达式是在构建系统生成期间求值的，并生成特定于每个构建配置的信息。

生成器表达式在很多目标属性的上下文允许使用，比如 LINK_LIBRARIES、INCLUDE_DIRECTORIES、COMPILE_DEFINITIONS和其它命令。当使用命令来填充这些属性时，它们也可以被使用 ，比如`target_link_libraries()`、 `target_include_directories()`、 `target_compile_definitions()`和其它。

这意味着 它们激活了条件链接、当编译时的条件定义和条件包含目录等。条件可能基于构建配置、目标属性、平台信息或者任意其他可查询的信息。

## 逻辑表达式

逻辑表达式被用来创建条件输出。基本的表达式是0和1表达式。因为其它逻辑表达式求值为0或者1，所以它们可以被组合起来创建条件输出：

```
- $<- $<CONFIG:Debug>:DEBUG_MODE>
```

当Debug配置被使用时，展开为DEBUG_MODE  ，否则展开为空。

可用的逻辑表达式如下:

- $<BOOL:...>
 
如果...是true，就为1，否则为0。

- $<AND:?[,?]...>
 
如果所有的？都是1，就为1，否则为0。
 
？必须为boolean表达式的0或1。

- $<OR:?[,?]...>
0 if all ? are 0, else 1
如果所有的？都是0，就为0，否则为1。

- $<NOT:?>
如果?是1，那么为0，否则为1

- $<STREQUAL:a,b>
如果a和b字符串相等，那么就为1，否则为0

- $<EQUAL:a,b>
如果a和b数字当做数字相比较结果相等，那么就为1，否则为0

- $<CONFIG:cfg>
如果config是cfn，就为1，否则为0。这是大小写敏感的比较。在MAP_IMPORTED_CONFIG_<CONFIG>中的映射 也被通过这个表达式考虑，当它在IMPORTED的目标的属性上评估的时候。

- $<PLATFORM_ID:comp>
如果平台的CMake-id匹配comp，就为1，否则为0。

- $<C_COMPILER_ID:comp>
1 if the CMake-id of the C compiler matches comp, otherwise 0.
如果C编译器的CMake-id匹配comp，就为1，否则为0。

- $<CXX_COMPILER_ID:c- $<omp>
1 if the CMake-id of the CXX compiler matches comp, otherwise 0.
如果CXX编译器的CMake-id匹配comp，就为1，否则为0。

- $<VERSION_GREATER:v1,v2>
1 if v1 is a version greater than v2, else 0.
如果v1的版本高于V2，就为1，否则为0。

- $<VERSION_LESS:v1,v2>
如果v1的版本低于V2，就为1，否则为0。

- $<VERSION_EQUAL:v1,v2>
如果v1的版本和V2的相同，就为1，否则为0。

- $<C_COMPILER_VERSION:ver>
1 if the version of the C compiler matches ver, otherwise 0.
如果C编译器的版本匹配ver，就为1，否则为0。

- $<CXX_COMPILER_VERSION:ver>
如果CXX编译器的版本匹配ver，就为1，否则为0。

- $<TARGET_POLICY:pol>
当head目标被创建的时候，如果策略pol是NEW，就为1，否则为0。如果策略没有被设置，会发出策略的警告信息。这个生成器表达式只为策略的子集工作。

- $<COMPILE_FEATURES:feature[,feature]...>
如果所有的特色都是可可以用于head目标，就为1，否则为0。如果这个表达式被使用 当评估 一个目标的连接实现 和 如果任意的依赖为head目标而增加所要求的C_STANDARD或者 CXX_STANDARD ，会报告一个错误。参考cmake-compile-features(7)手册来获得编译特色的信息和支持的编译器列表。

- $<COMPILE_LANGUAGE:lang>
当被用于单元编译的语言匹配lang，就为1，否则为0。这个表达式用于为一个目标中的特殊的包的源码指定编译选项 。比如，为了指定 -fno-exceptions编译选项（省略编译id检测）：

```
add_executable(myapp main.cpp foo.c bar.cpp)
target_compile_options(myapp
  PRIVATE $<$<COMPILE_LANGUAGE:CXX>:-fno-exceptions>
)
```

这个生成器表达式使用有限制，因为不可能把和Visual Studio生成器一起使用。便携的构建系统不会使用这个表达式，而是会每个源文件语言而创建独立的库：

```
add_library(myapp_c foo.c)
add_library(myapp_cxx foo.c)
target_compile_options(myapp_cxx PUBLIC -fno-exceptions)
add_executable(myapp main.cpp)
target_link_libraries(myapp myapp_c myapp_cxx)
```

基于Ninjia和Makefile的生成器也可以使用这个表达式来指定编译语言特定的编译定义和包含目录：

```
add_executable(myapp main.cpp foo.c bar.cpp)
target_compile_definitions(myapp
  PRIVATE $<$<COMPILE_LANGUAGE:CXX>:COMPILING_CXX>
)
target_include_directories(myapp
  PRIVATE $<$<COMPILE_LANGUAGE:CXX>:/opt/foo/cxx_headers>
)
```

## 信息表达式
这些表达式展开为一些信息。信息可能被直接使用，比如：'

```
include_directories(/usr/include/$<CXX_COMPILER_ID>/)
```

展开为/usr/include/GNU/ 或者/usr/include/Clang/等，依赖于编译器的ld。

这些表达式也可能于逻辑表达式组合：

```
$<$<VERSION_LESS:$<CXX_COMPILER_VERSION>,4.2.0>:OLD_COMPILER>
```
展开为OLD_COMPILER，如果CMAKE_CXX_COMPILER_VERSION 小于4.2.0。

所有的信息表达式为：

- $<CONFIGURATION>
Configuration name. Deprecated. Use CONFIG instead.
配置名字。废弃。使用CONFIG替代。

-  $<CONFIG>
配置名字。

- $<PLATFORM_ID>
平台的CMake-id。可以看CMAKE_SYSTEM_NAME 选项。


- $<C_COMPILER_ID>
使用的C编译器的CMake-id。可以看`CMAKE_<LANG>_COMPILER_ID`变量。

- $<CXX_COMPILER_ID>
使用的CXX编译器的CMake-id。可以看`CMAKE_<LANG>_COMPILER_ID`变量。

- $<C_COMPILER_VERSION>
使用的C编译器的版本的版本。可以参考`CMAKE_<LANG>_COMPILER_VERSION `变量。

- $<CXX_COMPILER_VERSION>
使用的C编译器的版本的版本。可以参考`CMAKE_<LANG>_COMPILER_VERSION `变量。

- $<TARGET_FILE:tgt>
main文件（.exe ，.so.1.2，.a）的全路径，在该路径tgt是目标的名字。

- $<TARGET_FILE_NAME:tgt>
main文件（.exe ，.so.1.2，.a）的名字。

- $<TARGET_FILE_DIR:tgt>
main文件（.exe ，.so.1.2，.a）的目录。

- $<TARGET_LINKER_FILE:tgt>
文件被用来链接（.a，.lib，.so），tgt是目标的名字

- $<TARGET_LINKER_FILE_NAME:tgt>
被用来链接的文件(.a, .lib, .so)的名字。

- $<TARGET_LINKER_FILE_DIR:tgt>
被用来链接的文件（.a，.lib，.so）的目录。

- $<TARGET_SONAME_FILE:tgt>
带有so字样的文件，tgt是目标的名字。

- $<TARGET_SONAME_FILE_NAME:tgt>
带有so字样 (.so.3)的文件的名字。

- $<TARGET_SONAME_FILE_DIR:tgt>
带有so字样(.so.3)的目录。

- $<TARGET_PDB_FILE:tgt>
链接器产生程序数据库文件(.pdb)的全路径，tgt是目标的名字。

可以看PDB_NAME和PDB_OUTPUT_DIRECTORY目标属性和它们的配置指定的变量`PDB_NAME_<CONFIG>` 和`PDB_OUTPUT_DIRECTORY_<CONFIG>`。

- $<TARGET_PDB_FILE_NAME:tgt>
链接器产生的程序数据库文件（.pdb）的名字。

- $<TARGET_PDB_FILE_DIR:tgt>
链接器产生的程序数据库文件（.pdb）的目录。

- $<TARGET_PROPERTY:tgt,prop>
在目标tgt上的prop属性的值。

注意这个表达式在某个目标上评估时，tgt没有被作为目标的依赖加入。

- $<TARGET_PROPERTY:prop>
目标上的prop属性的值，生成器语法在这个目标上评估。

 $<INSTALL_PREFIX>
Content of the install prefix when the target is exported via install(EXPORT) and empty otherwise.

安装前缀的内容  当目标被导出通过install（EXPORT）和empty

- $<COMPILE_LANGUAGE>
当评估编译选项时的源码文件的编译器语言。可以看一元版本，获得关于这个生成器语法的便携性的注释。

## 输出表达式

这些表达式产生了蔬菜，在一些场景下依赖输入。这个表达式可能与其他的信息表达式或者逻辑表达式组合：

```
-I$<JOIN:$<TARGET_PROPERTY:INCLUDE_DIRECTORIES>, -I>
```

生成一个在INCLUDE_DIRECTORIES 目标属性的条目的字符串，每个条目在-I选项之后。注意，在这个情况下，更加完整的用法是请求先检查INCLUDE_DIRECTORIES属性是非空的：

```
$<$<BOOL:${prop}>:-I$<JOIN:${prop}, -I>>
```

${prop}引用帮助变量：

```
set(prop "$<TARGET_PROPERTY:INCLUDE_DIRECTORIES>")
```

所有可用的变量是:

 - $<0:...>
空字符串（忽略...）

 - $<1:...>
...的内容

 - $<JOIN:list,...>
使用...的内容加入到列表

 - $<ANGLE-R>
语法>.  比如，用来比较包含>的字符串 。

 - $<COMMA>
语法,。比如用来比较包含,的字符串。

 - $<SEMICOLON>
语法；。比如用来比较包含；的字符串。

 - $<TARGET_NAME:...>
Marks ... as being the name of a target. This is required if exporting targets to multiple dependent export sets. The ... must be a literal name of a target- it may not contain generator expressions.

 - $<LINK_ONLY:...>
Content of ... except when evaluated in a link interface while propagating Transitive Usage Requirements, in which case it is the empty string. Intended for use only in an INTERFACE_LINK_LIBRARIES target property, perhaps via the target_link_libraries() command, to specify private link dependencies without other usage requirements.

 - $<INSTALL_INTERFACE:...>
Content of ... when the property is exported using install(EXPORT), and empty otherwise.

 - $<BUILD_INTERFACE:...>
Content of ... when the property is exported using export(), or when the target is used by another target in the same buildsystem. Expands to the empty string otherwise.

 - $<LOWER_CASE:...>
Content of ... converted to lower case.

 - $<UPPER_CASE:...>
Content of ... converted to upper case.

 - $<MAKE_C_IDENTIFIER:...>
Content of ... converted to a C identifier.

 - $<TARGET_OBJECTS:objLib>
List of objects resulting from build of objLib. objLib must be an object of type OBJECT_LIBRARY. This expression may only be used in the sources of add_library() and add_executable() commands.

 - $<SHELL_PATH:...>
Content of ... converted to shell path style. For example, slashes are converted to backslashes in Windows shells and drive letters are converted to posix paths in MSYS shells. The ... must be an absolute path.

