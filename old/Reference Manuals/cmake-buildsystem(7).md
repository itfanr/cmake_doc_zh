cmake-buildsystem(7)
===

翻译原文（校对完后删除）：https://cmake.org/cmake/help/v3.4/manual/cmake-buildsystem.7.html

##引言

基于Cmake的构建系统被组织为高层次的逻辑目标集合。每个目标符合一个可执行文件或者库，或者是一个自定义的包含自定义命令的目标。在构建系统中解释了目标之间的依赖，从而决定构建次序和当发生改变是重生成的规则。

##二进制目标

可执行文件和库使用`add_executable()`和`add_library()`命令定义。结果二进制文件有合适的前缀，后缀和目标平台的扩展。` target_link_libraries()`命令解释了二进制目标之间的依赖：

```
add_library(archive archive.cpp zip.cpp lzma.cpp)
add_executable(zipapp zipapp.cpp)
target_link_libraries(zipapp archive)
```

`archive`定义为静态库，一个包含archive.cpp、zip.cpp和lzma.cpp编译出来的目标文件的归档文件。`zipapp`定义为一个可执行文件，通过编译和链接zipapp.cpp来产生。当链接`zipapp`可执行文件时，`archive`静态库也被链接了。

### 二进制可执行文件

 add_executable()命令定义了可执行目标：
 
```
 add_executable(mytool mytool.cpp)
```

比如`add_custom_command()`这样的命令产生的规则可以在编译时刻运行，显然可以使用一个` EXECUTABLE `目标来作为COMMAND可执行文件。构建系统规则会确保可执行文件在尝试运行命令之前构建出来。

### 二进制库类型

#### 常规库 

默认情况下，除非指定了类型，`add_library() `命令定义为静态库。类型可能使用命令这样指定：

```
add_library(archive SHARED archive.cpp zip.cpp lzma.cpp)
```

```
add_library(archive STATIC archive.cpp zip.cpp lzma.cpp)
```

`BUILD_SHARED_LIBS `变量可能被启用来改变`add_library() `构建共享库的默认行为。

在构建系统的上下文的整体定义中，特定的库是SHARED还是STATIC很不相关——命令依赖特定的和其他API来类似地工作，不管库类型是什么。`module`库类型是不相似的，是因为它通常不被链接，它不会在`target_link_libraries() `命令的右边使用。它是使用运行时技术作为插件加载。

```
add_library(archive MODULE 7z.cpp)
```

##### 苹果框架

SHARED库可以使用FRAMEWORK目标属性来标识，并创建OSX 框架：

```
add_library(MyFramework SHARED MyFramework.cpp)
set_target_properties(MyFramework PROPERTIES
  FRAMEWORK 1
  FRAMEWORK_VERSION A
  )
```
  

####Object Libraries

`OBJECT `库类型也不会被链接。它定义了从给定的源文件编译得到的目标文件的非归档收集。目标文件收集可以作为到其他目标的源输入：

```
add_library(archive OBJECT archive.cpp zip.cpp lzma.cpp)

add_library(archiveExtras STATIC $<TARGET_OBJECTS:archive> extras.cpp)

add_executable(test_exe $<TARGET_OBJECTS:archive> test.cpp)
```

`OBJECT `库可能只在本地作为构建系统的源使用。它们可能不会安装、导出或者在`target_link_libraries() `的右侧使用。当使用`add_custom_command(TARGET) `命令签名时，它们也可能不会作为`TARGET `使用。

虽然目标库可能不会在调用`target_link_libraries() `时被直接命名，但是它们可以被直接『链接』通过使用一个`Interface Library`。Interface Library的`INTERFACE_SOURCES`目标属性可以被设置名字为$<TARGET_OBJECTS:objlib>。

##构建规范和使用要求

`target_include_directories()`、`target_compile_definitions()`和`target_compile_options()`命令指定构建的规范和二进制目标的使用所需。命令分别地填充了`INCLUDE_DIRECTORIES`、`COMPILE_DEFINITIONS`和`COMPILE_OPTIONS`目标属性以及`INTERFACE_INCLUDE_DIRECTORIES`、`INTERFACE_COMPILE_DEFINITIONS` 和`INTERFACE_COMPILE_OPTIONS` 目标属性。

每个命令有一个PRIVATE、PUBLIC 和 INTERFACE模式。PRIVATE模式仅仅填充了non-INTERFACE_目标属性的变量，INTERFACE模式仅仅填充了INTERFACE_变量。PUBLIC模式分别同时填充了目标属性。每个命令可能被使用了关键词的多个使用者触发。

```
target_compile_definitions(archive
  PRIVATE BUILDING_WITH_LZMA
  INTERFACE USING_ARCHIVE_LIB
)
```

注意，使用要求不是设计为作为一种便捷的方式，而不用使用特殊的`COMPILE_OPTIONS `或`COMPILE_DEFINITIONS `等。属性的内容是必须，并不仅仅是推荐或者为了方便。

查看cmake-packages(7) 手册中的Creating Relocatable Packages一节，其中说明了额外的注意项，即当创建包来重建时什么时候指定使用要求。

### 目标属性

当编译二进制目标的源码时，INCLUDE_DIRECTORIES、COMPILE_DEFINITIONS和COMPILE_OPTIONS目标属性被用来恰当地使用。

使用-I或者-isystem前缀，在INCLUDE_DIRECTORIES中的条目被加入到编译行，次序保存和属性值的出现一致。

使用-D或者/D前缀，在COMPILE_DEFINITIONS中的条目被加入到编译行，使用未指定的次序。DEFINE_SYMBOL目标属性也作为编译定义加入，这可以作为一种SHARED 和 MODULE 库目标的特殊的方便情形。

在COMPILE_OPTIONS中的条目脱离了shell，并以属性值的出现被加入。几个编译选项有特殊的分开处理，比如POSITION_INDEPENDENT_CODE。


 `INTERFACE_INCLUDE_DIRECTORIES`、`INTERFACE_COMPILE_DEFINITIONS` 和`INTERFACE_COMPILE_OPTIONS` 目标属性的内容是使用要求——它们指定的内容保证了消费者可以在它们出现的地方正确地编译和链接目标 。对于任意的二进制目标，在`target_link_libraries()`命令的每个目标上的每个INTERFACE_属性的内容是这样被使用的：
 
```
 set(srcs archive.cpp zip.cpp)
if (LZMA_FOUND)
  list(APPEND srcs lzma.cpp)
endif()
add_library(archive SHARED ${srcs})
if (LZMA_FOUND)
  # The archive library sources are compiled with -DBUILDING_WITH_LZMA
  target_compile_definitions(archive PRIVATE BUILDING_WITH_LZMA)
endif()
target_compile_definitions(archive INTERFACE USING_ARCHIVE_LIB)

add_executable(consumer)
# Link consumer to archive and consume its usage requirements. The consumer
# executable sources are compiled with -DUSING_ARCHIVE_LIB.
target_link_libraries(consumer archive)
```
 
 因为通常情况下要求源码目录和相应的编译目录被加入到`INCLUDE_DIRECTORIES`，所以`CMAKE_INCLUDE_CURRENT_DIR `变量可以被启用以便于可以方便地将相应的目录加入到所有目标的 `INCLUDE_DIRECTORIES`。变量 `CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE` 可以被启用以便于将相应的目录加入到所有目标的INTERFACE_INCLUDE_DIRECTORIES。这样一来，通过使用`target_link_libraries()`命令就可以方便地在多种不同的目录使用目标。
 
 ### 使用要求的可传递性
 
 目标的使用要求可以传递到它的依赖项上。target_link_libraries() 命令有PRIVATE,、INTERFACE 和PUBLIC关键词来控制蔓延。
 
```
 add_library(archive archive.cpp)
target_compile_definitions(archive INTERFACE USING_ARCHIVE_LIB)

add_library(serialization serialization.cpp)
target_compile_definitions(serialization INTERFACE USING_SERIALIZATION_LIB)

add_library(archiveExtras extras.cpp)
target_link_libraries(archiveExtras PUBLIC archive)
target_link_libraries(archiveExtras PRIVATE serialization)
# archiveExtras is compiled with -DUSING_ARCHIVE_LIB
# and -DUSING_SERIALIZATION_LIB

add_executable(consumer consumer.cpp)
# consumer is compiled with -DUSING_ARCHIVE_LIB
target_link_libraries(consumer archiveExtras)
```

因为archive是一个archiveExtras的PUBLILC依赖，所以它的使用要求也可以传递到consumer。因为serialization是一个archive的`PRIVATE`依赖，所以它的使用要求不会传递到consumer。

通常情况下，如果依赖仅仅被库的实现来使用的话，而且也不在头文件中，那么它应该使用带有`PRIVATE`关键词的`target_link_libraries()`来指定。如果依赖在库的头文件中额外被使用（比如类继承），那么它应该指定为PUBLIC依赖。如果依赖  不被库的实现使用，但是仅仅被头文件使用，应该指定为INTERFACE依赖。`target_link_libraries()`命令可能被每个关键词的多个使用而触发：

```
target_link_libraries(archiveExtras
  PUBLIC archive
  PRIVATE serialization
)
```

 使用要求可以通过从依赖读取目标属性的`INTERFACE_`变量和通过向operand的非INTERFACE_变量追加值来传递。比如，依赖的INTERFACE_INCLUDE_DIRECTORIES被读取并加入到operand的INCLUDE_DIRECTORIES。在那些顺序是相关的和保持的场合下，从调用target_link_libraries()导致的顺序会出现编译错误，然后使用一个合适的命令来直接设置属性可能会更新顺序。

比如，如果一个目标的链接库必须指定顺序为lib1、lib2、lib3，但是包含路径必须指定顺序为lib3、lib1、lib2：

```
target_link_libraries(myExe lib1 lib2 lib3)
target_include_directories(myExe
  PRIVATE $<TARGET_PROPERTY:lib3,INTERFACE_INCLUDE_DIRECTORIES>)
```

如果目标使用install(EXPORT)命令导出安装，为其指定使用要求的时候，请一定注意。查看Creating Packages得到更多信息。

 ### 兼容的接口属性
 
一些目标属性需要在目标和每个依赖的接口之间兼容。比如，POSITION_INDEPENDENT_CODE 目标属性可以指定一个目标是否需要编译为（位置独立的代码）position-independent-code ，代码具有平台相关的特色。目标也可能指定使用INTERFACE_POSITION_INDEPENDENT_CODE来说明消费者必须编译为位置独立的代码。

```
add_executable(exe1 exe1.cpp)
set_property(TARGET exe1 PROPERTY POSITION_INDEPENDENT_CODE ON)

add_library(lib1 SHARED lib1.cpp)
set_property(TARGET lib1 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)

add_executable(exe2 exe2.cpp)
target_link_libraries(exe2 lib1)
```

这里，exe1和exe2 将会编译为位置独立的代码。lib1 也会被编译为位置独立的代码，因为这是SHARED库的默认设置。如果依赖有冲突，不兼容的要求会报告一个诊断错误：

```
add_library(lib1 SHARED lib1.cpp)
set_property(TARGET lib1 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)

add_library(lib2 SHARED lib2.cpp)
set_property(TARGET lib2 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE OFF)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1)
set_property(TARGET exe1 PROPERTY POSITION_INDEPENDENT_CODE OFF)

add_executable(exe2 exe2.cpp)
target_link_libraries(exe2 lib1 lib2)
```

lib1 要求的变量INTERFACE_POSITION_INDEPENDENT_CODE与 exe1目标的POSITION_INDEPENDENT_CODE属性不是兼容的 。虽然可执行文件指定编译为位置独立的代码，但是库要求消费者编译为位置独立的代码，所以报告了诊断错误。

lib1和lib2要求不是兼容的。其中之一要求编译为位置独立的代码，但是另一个要求编译为位置独立的代码。因为exe2链接了它们，它们是冲突的，所以报告了错误。

为了兼容，如果设置了POSITION_INDEPENDENT_CODE 属性，那么必须也是相同的的布尔类型，和所有的及物地指定的依赖的INTERFACE_POSITION_INDEPENDENT_CODE属性相同，这些依赖上的属性被设置。（此段翻译不当，请帮忙翻译一下）

通过在 COMPATIBLE_INTERFACE_BOOL目标属性的内容上指定属性，“兼容的接口要求”的属性可以扩展到其他属性。  在消费目标和每个依赖上得到的使用INTERFACE_前缀相应的属性 之间，每个指定的数学必须是兼容的：

```
add_library(lib1Version2 SHARED lib1_v2.cpp)
set_property(TARGET lib1Version2 PROPERTY INTERFACE_CUSTOM_PROP ON)
set_property(TARGET lib1Version2 APPEND PROPERTY
  COMPATIBLE_INTERFACE_BOOL CUSTOM_PROP
)

add_library(lib1Version3 SHARED lib1_v3.cpp)
set_property(TARGET lib1Version3 PROPERTY INTERFACE_CUSTOM_PROP OFF)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1Version2) # CUSTOM_PROP will be ON

add_executable(exe2 exe2.cpp)
target_link_libraries(exe2 lib1Version2 lib1Version3) # Diagnostic
```

非布尔类型的属性也可以 参与到“兼容的接口”计算中。在COMPATIBLE_INTERFACE_STRING 属性中指定的属性必须是未指定的或者 可以与所有及物指定的依赖相同的值比较。这样的好处是，可以确保多个不兼容的版本的库  不会 通过目标的及物的要求而链接到一起：

```
add_library(lib1Version2 SHARED lib1_v2.cpp)
set_property(TARGET lib1Version2 PROPERTY INTERFACE_LIB_VERSION 2)
set_property(TARGET lib1Version2 APPEND PROPERTY
  COMPATIBLE_INTERFACE_STRING LIB_VERSION
)

add_library(lib1Version3 SHARED lib1_v3.cpp)
set_property(TARGET lib1Version3 PROPERTY INTERFACE_LIB_VERSION 3)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1Version2) # LIB_VERSION will be "2"

add_executable(exe2 exe2.cpp)
target_link_libraries(exe2 lib1Version2 lib1Version3) # Diagnostic
```

 COMPATIBLE_INTERFACE_NUMBER_MAX目标属性指定了  内容将会被评估数值，所有指定的最大值都会被评估：

```
add_library(lib1Version2 SHARED lib1_v2.cpp)
set_property(TARGET lib1Version2 PROPERTY INTERFACE_CONTAINER_SIZE_REQUIRED 200)
set_property(TARGET lib1Version2 APPEND PROPERTY
  COMPATIBLE_INTERFACE_NUMBER_MAX CONTAINER_SIZE_REQUIRED
)

add_library(lib1Version3 SHARED lib1_v3.cpp)
set_property(TARGET lib1Version2 PROPERTY INTERFACE_CONTAINER_SIZE_REQUIRED 1000)

add_executable(exe1 exe1.cpp)
# CONTAINER_SIZE_REQUIRED will be "200"
target_link_libraries(exe1 lib1Version2)

add_executable(exe2 exe2.cpp)
# CONTAINER_SIZE_REQUIRED will be "1000"
target_link_libraries(exe2 lib1Version2 lib1Version3)
```

类似地，COMPATIBLE_INTERFACE_NUMBER_MIN 可以被用来计算从依赖得到的属性的最小值。

每个计算的“不兼容的”属性值可能被在生成阶段使用生成器语法 的消费者读取。

注意到，对每个依赖，在每个兼容的接口属性中指定的属性集合 不能和 其他属性的中指定的集合有交集。
 ### 属性原始的调试


因为构建说明可以通过依赖来决定， 创建了一个目标的代码  和 有责任  设置构建详细的代码 的位置 可能使得代码更难来说明白。cmake(1)提供了一个调试的方便方式来打印那些通过依赖决定的属性的原始内容。可以被调试的属性在CMAKE_DEBUG_TARGET_PROPERTIES 变量的文档中列出：

```
set(CMAKE_DEBUG_TARGET_PROPERTIES
  INCLUDE_DIRECTORIES
  COMPILE_DEFINITIONS
  POSITION_INDEPENDENT_CODE
  CONTAINER_SIZE_REQUIRED
  LIB_VERSION
)
add_executable(exe1 exe1.cpp)
```

在COMPATIBLE_INTERFACE_BOOL或者COMPATIBLE_INTERFACE_STRING列出的属性的情况下，调试输出显示出了哪些目标有责任设置属性和哪个依赖也定义了属性。在COMPATIBLE_INTERFACE_NUMBER_MAX 和 COMPATIBLE_INTERFACE_NUMBER_MIN情况下，调试输出显示出了每个依赖的属性的值和值是否决定了新的最大值。
 
 ### 使用生成器语法来指定构建
 

构建说明可能使用generator expressions，其包含的内容 可能是有条件的或者仅仅在生成阶段才知道。比如，一个属性的计算的“兼容”值  可能被使用TARGET_PROPERTY 表达式读取：

```
add_library(lib1Version2 SHARED lib1_v2.cpp)
set_property(TARGET lib1Version2 PROPERTY
  INTERFACE_CONTAINER_SIZE_REQUIRED 200)
set_property(TARGET lib1Version2 APPEND PROPERTY
  COMPATIBLE_INTERFACE_NUMBER_MAX CONTAINER_SIZE_REQUIRED
)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1Version2)
target_compile_definitions(exe1 PRIVATE
    CONTAINER_SIZE=$<TARGET_PROPERTY:CONTAINER_SIZE_REQUIRED>
)
```

在这种情形，exe1源文件可以使用-DCONTAINER_SIZE=200编译。

由配置决定的构建说明可能被使用CONFIG生成器语法方便地指定：

```
target_compile_definitions(exe1 PRIVATE
    $<$<CONFIG:Debug>:DEBUG_BUILD>
)
```

CONFIG参数以大小写不敏感的方式与构建产生的配置比较。在包含IMPORTED的目标中， MAP_IMPORTED_CONFIG_DEBUG的内容也会被这个语法考虑。

有些cmake(1)产生的构建系统在CMAKE_BUILD_TYPE中有一个预决定的构建配置集合。为Visual Studio和Xcode等IDE而产生的构建系统是独立于构建配置产生的。，实际的配置文件在编译阶段才能知道。因此，像这样的代码：

```
string(TOLOWER ${CMAKE_BUILD_TYPE} _type)
if (_type STREQUAL debug)
  target_compile_definitions(exe1 PRIVATE DEBUG_BUILD)
endif()
```   

可能对基于Makefile和Ninja生成器的场合工作良好，但是不适合IDE生成器。另外，IMPORTED配置映射不会考虑这样的代码，所以它应该被避免。

一元的TARGET_PROPERTY 生成器语法和TARGET_POLITY生成器语法通过消费者的目标上下文来评估。这意味着使用要求说明可能会因为消费者的不同而不同：

```
add_library(lib1 lib1.cpp)
target_compile_definitions(lib1 INTERFACE
  $<$<STREQUAL:$<TARGET_PROPERTY:TYPE>,EXECUTABLE>:LIB1_WITH_EXE>
  $<$<STREQUAL:$<TARGET_PROPERTY:TYPE>,SHARED_LIBRARY>:LIB1_WITH_SHARED_LIB>
  $<$<TARGET_POLICY:CMP0041>:CONSUMER_CMP0041_NEW>
)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1)

cmake_policy(SET CMP0041 NEW)

add_library(shared_lib shared_lib.cpp)
target_link_libraries(shared_lib lib1)
```

exe1可执行文件可以使用-DLIB1_WITH_EXE参数编译，虽然shared_lib共享库使用-DLIB1_WITH_SHARED_LIB 和 -DCONSUMER_CMP0041_NEW 编译。因为 在shared_lib目标被创建时，CMP0041策略是新的。

BUILD_INTERFACE 语法封装了这样的要求 ，只有当被同一个构建系统中的一个目标消费时使用，或者当被   使用`export`命令导出到构建目录的目标消费时。`INSTALL_INTERFAE`语法封装了这样的要求，当被使用install(EXPORT) 命令安装和导出的目标而消费的时，才被使用：

```
add_library(ClimbingStats climbingstats.cpp)
target_compile_definitions(ClimbingStats INTERFACE
  $<BUILD_INTERFACE:ClimbingStats_FROM_BUILD_LOCATION>
  $<INSTALL_INTERFACE:ClimbingStats_FROM_INSTALLED_LOCATION>
)
install(TARGETS ClimbingStats EXPORT libExport ${InstallArgs})
install(EXPORT libExport NAMESPACE Upstream::
        DESTINATION lib/cmake/ClimbingStats)
export(EXPORT libExport NAMESPACE Upstream::)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 ClimbingStats)
```
在这种场景下，`exe1`可执行文件会被带有`-DClimbingStats_FROM_BUILD_LOCATION`参数编译。导出命令生成`IMPORTED`目标，带有`INSTALL_INTERFACE`或者省略`BUILD_INTERFACE`,去掉`*_INTERFACE`标记。消费`ClimbingStats`包的分离的项目会包含：

```
find_package(ClimbingStats REQUIRED)

add_executable(Downstream main.cpp)
target_link_libraries(Downstream Upstream::ClimbingStats)
```
依赖于`CkunbubgStats`包被使用从构建位置或者安装位置`，`Downstream`目标会被使用参数`-DClimbingStats_FROM_BUILD_LOCATION `或者 `-DClimbingStats_FROM_INSTALL_LOCATION`。想看更多关于包和导出的信息，可以参考`camke-packages`手册。

#### 包含目录和使用要求

包含目录要求一些特殊的考虑，当被指定为使用要求和当被生成器表达式使用时。`target_include_directories()`命令接受相对和绝对的包含目录：

```
add_library(lib1 lib1.cpp)
target_include_directories(lib1 PRIVATE
  /absolute/path
  relative/path
)
```

相对路径被解析为相对于命令出现的源码路径。相对路径不允许在 `IMPORTED`目标的`INTERFACE_INCLUDE_DIRECTORIES`中使用。

在  non-trivial 生成器表达式被使用的场合中，`INSTALL_PREFIX`表达式可能被带有`INSTALL_INTERFACE`参数时使用。它是一个代表标记 ，当被消费项目时导入时展开为安装前缀。

包含目录使用要求通常在构建树和安装树之间不同。`BUILD_UBTERFACE`和`INSTALL_INTERFAE` 生成器表达式可以基于使用位置被使用来描述分离的使用要求。  带有`INSTALL_INTERFACE`表达式允许相对路径，相对路径 被解析为相对于安装前缀。比如：

```
add_library(ClimbingStats climbingstats.cpp)
target_include_directories(ClimbingStats INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/generated>
  $<INSTALL_INTERFACE:/absolute/path>
  $<INSTALL_INTERFACE:relative/path>
  $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/$<CONFIG>/generated>
)
```

关于包含目录使用要求，提供了两个比较方便的API 。`CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE`变量可以被激活，带有等效的效果：

```
set_property(TARGET tgt APPEND PROPERTY INTERFACE_INCLUDE_DIRECTORIES
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR};${CMAKE_CURRENT_BINARY_DIR}>
)
```

影响到的每个目标。安装的目标的方便是，`INCLUDES DESTINATION`组件带有`install(TARGETS)`命令：

```
install(TARGETS foo bar bat EXPORT tgts ${dest_args}
  INCLUDES DESTINATION include
)
install(EXPORT tgts ${other_args})
install(FILES ${headers} DESTINATION include)
```
这等效为添加`${CMAKE_INSTALL_PREFIX}/include`到每个安装的`IMPORTED`目标的`INTERFACE_INCLUDE_DIRECTORIES`，这些目标被`install(EXPORT)`生成的。

当导入的目标的`INTERFACE_INCLUDE_DIRECTORIES`被消费，属性中的条目被当做`SYSTEM`包含目录，如果他们在依赖中的`INTERFACE_SYSTEM_INCLUDE_DIRECTORIES`中列出。这会使得忽略掉这些目录中的头文件找不到的编译警告。导入的目标的行为可以通过带有`NO_SYSTEM_FROM_IMPORTED`目标属性来控制。

如果二进制目标过渡地链接到一个Mac OX 框架，`Headers`目录  框架的  也会被当做一个使用要求。这与传递框架目录为一个包含目录是一样的效果。

 
 ### 链接库和生成器语法

 就像编译规范一样，`link libraries`可能被通过生成器表达式环境指定。但是，因为使用说明的消费基于从链接的依赖集合，有一个额外的限制是链接依赖必须形成一个有向无环图。也就是说，如果链接为一个目标依赖于一个目标属性的值，那个目标属性可能不会依赖于链接的依赖：

```
add_library(lib1 lib1.cpp)
add_library(lib2 lib2.cpp)
target_link_libraries(lib1 PUBLIC
  $<$<TARGET_PROPERTY:POSITION_INDEPENDENT_CODE>:lib2>
)
add_library(lib3 lib3.cpp)
set_property(TARGET lib3 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 lib1 lib3)
```

 由于`exe1`目标的`POSITION_INDEPENDENT_CODE`属性的值依赖于链接库（lib3），而且链接`exe1`的边缘是由同样的`POSITION_INDEPENDENT_CODE`属性决定的，上面的依赖图包含一个圆圈。cmake(1)描述了如何诊断这种情况。


 ### 输出组件
通过`add_library()`和`add_executable()`命令创建的构建系统的目标  创建了规则来创建二进制输出。 二进制的精确的输出位置只能在生成阶段确定，因为它可以依赖于构建配置和链接依赖的依赖语言等。`TARGET_FILE`、`TARGET_LINKER_FILE`和相关的表达式可以被用来接入名字和产生二进制的位置。但是，这些表达式不会为`OBJECT`库工作，因为没有一个单文件被关于表达式的库产生。

有三种输出组件可能被构建被下面的章节列出的目标。他们的归类  区别于 DLL平台和非-DLL平台。所有包括Cygwin的基于windows的系统是DLL平台。

 
 #### 运行时输出组件
 构建系统的目标的运行时的输出组件可以是：

- 通过`add_executable`命令创建的可执行的目标的可执行文件（比如.exe）。
- 在DLL平台：通过带有SHARED选项的`add_library()`命令创建的共享库目标的可执行文件（比如.dll）。

`RUNTIME_OUTPUT_DIRECORY`和`RUNTIME_OUTPUT_NAME`目标属性可以被用来控制运行时构建树中的输出组件位置和名字。 


#### 库输出组件

构建系统目标的库输出组件可以是：
- 通过带有`MODULE`的`add_library()`命令创建的模块库目标的可加载的模块文件（比如.dll或者.so）。
- 在非DLL平台：通过带有`SHARED`的`add_library()`命令创建的共享库目标的共享库文件（比如.so或者.dylib）。

`LIBRARY_OUTPUT_DIRECTORY`和`LIBRARY_OUTPUT_NAME`目标属性可以被用来控制构建树种的库输出组件位置和名字。
 
 #### 归档输出组件
 构建目标的归档输出组件可以是：
 - 静态文件
 - 在DLL平台：
 - 在DLL平台：
 `ARCHIVE_OUTPUT_DIRECTORIY`和`ARCHIVE_OUTPUT_NAME`目标属性可以被用来控制在构建树种的归档输出组件的位置和名字。
 
 ###  目录作用域的命令
 `target_include_directories()`、`target_compile_definitions()`和` target_compile_options() `命令一次只能 在一个目标上产生作用。命令`add_definitions()`、` add_compile_options() `和`include_directories()有类似的函数，但是为了方便，操作的是目录域而不是目标域。
 
 ## 伪目标
 一些目标类型不会代表构建系统中的输出，但是只有比如外部依赖 的输入 、别名或者其他非构建组件。伪目标在产生的构建系统中不会被代表。
 
 ### 导入的目标
 导入的目标代表预存在的依赖。通常，这些目标被上流包定义，应该看作是不可变的。不可能使用导入的目标，在target_compile_definitions(),\target_include_directories(),、target_compile_options() 或者target_link_libraries() 命令的左边，因为那会尝试修改它。导入的目标被设计用来仅仅是这些命令的右边使用。
 
 导入目标可能有相同的填充为二进制目标的使用要求属性，比如INTERFACE_INCLUDE_DIRECTORIES、INTERFACE_COMPILE_DEFINITIONS,、INTERFACE_COMPILE_OPTIONS,、INTERFACE_LINK_LIBRARIES,和INTERFACE_POSITION_INDEPENDENT_CODE.。
 
 LOCATIION可以从导入的目标读取，尽管有很少的原因来这么做。比如`add_cunstom_command`可以透明地使用作为COMMAND可执行的IMPORTED EXECUTABLE目标。
 
 导入目标的定义范围是它被定义的目录。它可以被从子目录获取和使用，但是不会从父目录或者兄弟目录（获取和使用）。范围类似于cmake变量的范围。
 
 也可能定义一个全局导入的目标，这个目标在构建系统中是全局可以获得的。
 
 查看cmake-packages(7)手册获取更多关于带有IMPORTED目标的创建包过程的信息。
 
 ### 别名目标
 
 别名目标是一个名字，可能被用来作为只读的上下文中的二进制目标的名字。一个`ALIAS`目标的主要的使用场景是伴随一个库的可执行文件的单元测试，可能是同一个构建系统中的一部分或者基于用户的配置分开构建。
 
```
 add_library(lib1 lib1.cpp)
install(TARGETS lib1 EXPORT lib1Export ${dest_args})
install(EXPORT lib1Export NAMESPACE Upstream:: ${other_args})

add_library(Upstream::lib1 ALIAS lib1)
```

 在另外的目录，我们可以无条件地链接到Upstream:lib1目标，可能是一个包的IMPORTED目标或者一个`ALIAS`目标，如果是作为同一个构建系统的一部分的话。
 
```
 if (NOT TARGET Upstream::lib1)
  find_package(lib1 REQUIRED)
endif()
add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 Upstream::lib1)
```

 `ALIAS`目标不是易变的，可安装的或者可导出的。他们完全是本地化的，相对于构建系统描述来说。通过读取`ALIASED_TARGET`属性，我们可以测试一个名字是否是别名：
 
```
 get_target_property(_aliased Upstream::lib1 ALIASED_TARGET)
if(_aliased)
  message(STATUS "The name Upstream::lib1 is an ALIAS for ${_aliased}.")
endif()
```
 
 ### 接口库
 `INTERFACE`目标没有LOCATION，而且是可变的，但是和IMPORTED目标很类似。
 
 可能指定使用要求，使用INTERFACE_INCLUDE_DIRECTORIES、INTERFACE_COMPILE_DEFINITIONS,、INTERFACE_COMPILE_OPTIONS,、INTERFACE_LINK_LIBRARIES, INTERFACE_SOURCES和INTERFACE_POSITION_INDEPENDENT_CODE。只有target_include_directories()、target_compile_definitions()、target_compile_options()、target_sources()和target_link_libraries() 命令的接口模型才可能带有INTERFACE库被使用。
 
 INTERFACE库的主要的使用场景是只有头文件的库：
 
```
 add_library(Eigen INTERFACE)
target_include_directories(Eigen INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include/Eigen>
)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 Eigen)
```
 
 这里，来自于Eigen目标的使用要求当编译的时候可以被消费和使用，但是在连接的时候没有作用。
 
 另一个使用场景是为了使用为了使用要求的完全关注于目标的设计：

 
```
 add_library(pic_on INTERFACE)
set_property(TARGET pic_on PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)
add_library(pic_off INTERFACE)
set_property(TARGET pic_off PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE OFF)

add_library(enable_rtti INTERFACE)
target_compile_options(enable_rtti INTERFACE
  $<$<OR:$<COMPILER_ID:GNU>,$<COMPILER_ID:Clang>>:-rtti>
)

add_executable(exe1 exe1.cpp)
target_link_libraries(exe1 pic_on enable_rtti)
```
 
 这种方式，exe1的构建描述被表述为链接目标，编译器指定的灵活度标识封装在INTERFACE库目标里面。
 
 从INTERFACE库中允许被设置后者读取的属性是：
 
 - 匹配INTERFACE_*的属性
 - 匹配COMPATIBLE_INTERFACE_*的内建属性
 - EXPORT_NAME
 - IMPORTED
 - NAME
 - 匹配MAP_IMPORTED_CONFIG_*的属性

 
 INTERFACE库可能被安装后导出。他们引用的任意的内容必须单独安装：


```
 add_library(Eigen INTERFACE)
target_include_directories(Eigen INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include/Eigen>
)

install(TARGETS Eigen EXPORT eigenExport)
install(EXPORT eigenExport NAMESPACE Upstream::
  DESTINATION lib/cmake/Eigen
)
install(FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/eigen.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/vector.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/matrix.h
  DESTINATION include/Eigen
)
```
 





