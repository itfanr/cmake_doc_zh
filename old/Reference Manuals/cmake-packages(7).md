cmake-packages(7)
===

# cmake-packages(7)

标签（空格分隔）： cmake

网址：https://cmake.org/cmake/help/v3.4/manual/cmake-packages.7.html

---

##引言
包向基于CMake的构建系统提供了依赖信息。包可以通过find_package()命令找到。使用find_package的结果是一个IMPORTED的目标集合或者是对应于构建相关的信息的变量集合。

##使用包
CMake给两种形式的包提供了直接支持，Config-file包和Find-module包。 通过FindPkgConfig模块也提供给pkg-config包 间接的支持。在所有的场景下，find_package()这种基本形式的调用也是相同的：
```
find_package(Qt4 4.7.0 REQUIRED) # CMake provides a Qt4 find-module
find_package(Qt5Core 5.1.0 REQUIRED) # Qt provides a Qt5 package config file.
find_package(LibXml2 REQUIRED) # Use pkg-config via the LibXml2 find-module
```
在那些场景，通过上游知道包的配置文件，而且只有那个应该被使用，CONFIG关键词可能被传给find_package()：

```
find_package(Qt5Core 5.1.0 CONFIG REQUIRED)
find_package(Qt5Gui 5.1.0 CONFIG)
```

类似地，MODULE关键词提示要使用仅仅一个find模块：

```
find_package(Qt4 4.7.0 MODULE REQUIRED)
```
显示地指定包的类型改善了错误提示，如果包不存在的话。两种包的类型 也支持指定包的组件，或者在REQUIRED关键词后面：

```
find_package(Qt5 5.1.0 CONFIG REQUIRED Widgets Xml Sql)
```

或者作为一个COMPONENTS 列表：
```
find_package(Qt5 5.1.0 COMPONENTS Widgets Xml Sql)
```
或者作为一个OPTIONAL_COMPONENTS 列表：
```
find_package(Qt5 5.1.0 COMPONENTS Widgets
                       OPTIONAL_COMPONENTS Xml Sql
)
```
处理COMPONETS和OPTIONAL_COMPONENTS的方式是通过包定义的。
通过设置CMAKE_DISABLE_FIND_PACKAGE_<PackageName> 变量为TRUE，PackageName包不会被搜索，会一直是NOTFOND。

###配置文件包
配置文件包是一个上游给下游提供的文件集合。CMake为包配置文件搜索 大量的位置，在find_package()文档中描述。最简单的方式是告诉cmake在非标准前缀来为包搜索的办法是设置CMAKE_PREFIX_PATH 缓存变量。

配置文件包通过上游提供的，作为开发包的一部分，也就是说，它们属于头文件和任意的其他文件，用来辅助下游使用这个包。

提供了包状态信息的变量集合  也会使用配置包自动设置 。<Package>_FOND变量设为true或者false，依赖于包是否被找到。<Package>_DIR缓存变量根据包配置文件的位置而被设置。

###查找模块包
查找模块是一个带有规则集合的文件，  为了找到依赖的需要的东西——主要的头文件和库。典型地，当上游不是使用CMake构建时，需要查找模块，或者说上游不足CMake-aware来提供一个包配置文件。不像包配置文件一样，它不是用上游文件带着，而是由下游猜测文件的位置（通过平台特定的提示）来找到文件。

不像  上游提供包配置文件的场景那样，引用的非单点 标识包为找到，所以<Package>_FOND变量 不是通过find_packages()命令来自动设置的。但是仍然可以期望通过转换被设置，应该被Find-moudle的作者来设置。类似地，没有<Package>_DIR变量，但是  库文件的位置和头文件的位置提供分离的缓存变量。

可以查看cmake-developer手册来看更多关于创建查找模块的文件的信息。

##包层次
配置文件包由 包配置文件和可选的通过项目发布提供的包版本文件组成。

###包配置文件
考虑一个项目Foo来安装如下的文件：
```
<prefix>/include/foo-1.2/foo.h
<prefix>/lib/foo-1.2/libfoo.a
```
可能提供了一个CMake包配置文件：
```
<prefix>/lib/cmake/foo-1.2/FooConfig.cmake
```
带有定义IMPORTED目标的文件，或者定义变量，比如：
```
# ...
# (compute PREFIX relative to file location)
# ...
set(Foo_INCLUDE_DIRS ${PREFIX}/include/foo-1.2)
set(Foo_LIBRARIES ${PREFIX}/lib/foo-1.2/libfoo.a)
```
在另外的项目中希望使用Foo，它唯一要做的就是确定FooConfig.cmake文件的位置，然后加载它，从而得到所有关于包内容的位置要知道的信息。既然包配置文件由包安装提供，它已经知道了所有的文件位置。

find_package()命令可能被使用来搜索包配置文件。这个命令构建了安装前缀的集合，然后在每个前缀下的几个位置搜索。给定名字Foo，它搜索叫作FooConfig.cmake或者foo-config.cmake的文件。位置的全部集合在find_package()命令文档中指定。一个位置 看起来是这样的：

```
<prefix>/lib/cmake/Foo*/
```
其中Foo*是大小写铭感的通配符表达式。在我们的例子中，通配符表达式会匹配`<prefix>/lib/cmake/foo-1.2`，包配置文件会被找到。

一旦找到，包配置文件马上被加载。它和包版本文件一起包含了所有的项目需要使用包的信息。

###包版本文件
当find_package()命令找到一个候选包配置文件，它会再找版本文件。版本文件被加载来测试 包版本是否是请求版本的一个可接受的匹配。如果版本文件声明了配置文件的兼容性是可接受的。否则会被忽略。

 包版本文件的名字必须匹配包配置文件的版本，除非在.cmake扩展名前和名字之后带有--veriion或则Version。比如文件是：
 
 ```
 <prefix>/lib/cmake/foo-1.3/foo-config.cmake
<prefix>/lib/cmake/foo-1.3/foo-config-version.cmake
 ```
 和
 ```
 <prefix>/lib/cmake/bar-4.2/BarConfig.cmake
<prefix>/lib/cmake/bar-4.2/BarConfigVersion.cmake
 ```
是包配置文件对和相应的包版本文件。
当find_package()命令加载了版本文件，它首先设置如下的变量：

- PACKAGE_FIND_NAME The <package> name
- PACKAGE_FIND_VERSION Full requested version string
- PACKAGE_FIND_VERSION_MAJOR Major version if requested, else 0
- PACKAGE_FIND_VERSION_MINOR Minor version if requested, else 0
- PACKAGE_FIND_VERSION_PATCH Patch version if requested, else 0
- PACKAGE_FIND_VERSION_TWEAK Tweak version if requested, else 0
- PACKAGE_FIND_VERSION_COUNT Number of version components, 0 to 4

版本文件必须使用这些变量来检查是否是兼容的或者准确的匹配请求的版本，然后设置如下的变量：

- PACKAGE_VERSION Full provided version string
- PACKAGE_VERSION_EXACT True if version is exact match
- PACKAGE_VERSION_COMPATIBLE True if version is compatible
- PACKAGE_VERSION_UNSUITABLE True if unsuitable as any version

版本文件加载在内部范围所以它们 可以随意设置任意的变量作为它们计算的一部分。find_package()命令清理这个范围，当版本文件已经完成且已经检查了输出变量时。当版本文件声明可以作为一个请求版本的可接受的匹配 ，   find_package命令设置如下的变量以被项目使用：


- <package>_VERSION
Full provided version string
- <package>_VERSION_MAJOR
Major version if provided, else 0
- <package>_VERSION_MINOR
Minor version if provided, else 0
- <package>_VERSION_PATCH
Patch version if provided, else 0
- <package>_VERSION_TWEAK
Tweak version if provided, else 0
- <package>_VERSION_COUNT
Number of version components, 0 to 4
 
变量报告了包的版本确实被找到了。它们的名字的<package>部分 匹配了传给find_package()命令的参数。

##创建包
通常情况，上游依赖于CMake本身，可以使用CMake工具来创建包文件。考虑提供单一共享库的上游：

```
project(UpstreamLib)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE ON)

set(Upstream_VERSION 3.4.1)

include(GenerateExportHeader)

add_library(ClimbingStats SHARED climbingstats.cpp)
generate_export_header(ClimbingStats)
set_property(TARGET ClimbingStats PROPERTY VERSION ${Upstream_VERSION})
set_property(TARGET ClimbingStats PROPERTY SOVERSION 3)
set_property(TARGET ClimbingStats PROPERTY
  INTERFACE_ClimbingStats_MAJOR_VERSION 3)
set_property(TARGET ClimbingStats APPEND PROPERTY
  COMPATIBLE_INTERFACE_STRING ClimbingStats_MAJOR_VERSION
)

install(TARGETS ClimbingStats EXPORT ClimbingStatsTargets
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)
install(
  FILES
    climbingstats.h
    "${CMAKE_CURRENT_BINARY_DIR}/climbingstats_export.h"
  DESTINATION
    include
  COMPONENT
    Devel
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/ClimbingStats/ClimbingStatsConfigVersion.cmake"
  VERSION ${Upstream_VERSION}
  COMPATIBILITY AnyNewerVersion
)

export(EXPORT ClimbingStatsTargets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/ClimbingStats/ClimbingStatsTargets.cmake"
  NAMESPACE Upstream::
)
configure_file(cmake/ClimbingStatsConfig.cmake
  "${CMAKE_CURRENT_BINARY_DIR}/ClimbingStats/ClimbingStatsConfig.cmake"
  COPYONLY
)

set(ConfigPackageLocation lib/cmake/ClimbingStats)
install(EXPORT ClimbingStatsTargets
  FILE
    ClimbingStatsTargets.cmake
  NAMESPACE
    Upstream::
  DESTINATION
    ${ConfigPackageLocation}
)
install(
  FILES
    cmake/ClimbingStatsConfig.cmake
    "${CMAKE_CURRENT_BINARY_DIR}/ClimbingStats/ClimbingStatsConfigVersion.cmake"
  DESTINATION
    ${ConfigPackageLocation}
  COMPONENT
    Devel
)
```
 CMakePackageConfigHelpers模块提供了一个宏指令 来创建一个简单的ConfigVersion.cmake文件。这个文件设置了包的版本。它会被CMake读取，  当find_package()被调用来决定请求版本的兼容性的时候，然后设置一些版本特定的变量<Package>_VERSION，<Package>_VERSION_MAJOR和 <Package>_VERSION_MINOR等。install(EXPORT)命令被用来导出在ClimbingStatsTargets导出集合中的目标，它们被install(TARGETS)命令在前面定义。这个命令产生了ClimbingStatsTargets.cmake文件来包含IMPORTED目标（适合被下游来使用），和安排把它安装到lib/cmake/ClimbingStats。产生的ClimbingStatsConfigVersion.cmake和一个cmake/ClimbingStatsConfig.cmake文件被安装到相同的位置，完成了包。
 
产生的IMPORTED目标有合适的属性集合来定义它们的使用要求，比如INTERFACE_INCLUDE_DIRECTORIES、 INTERFACE_COMPILE_DEFINITIONS 和其他相关的内建的INTERFACE_属性。  在COMPATIBLE_INTERFACE_STRING和其他的Compatible Interface Properties中列出的用户定义属性的INTERFACE的不同，也会蔓延到生成的IMPORTED目标。在以上的场景中，ClimbingStats_MAJOR_VERSION 被定义为一个字符串，必须在任何depender的依赖中保持兼容。通过在这个版本设置这个自定义的用户属性，在  ClimbingStats的下个版本，cmake会提出一个诊断来判断 是否尝试同时使用版本3和版本4。包可以选择来使用这个模式，  如果包的不同的主版本被设计为不兼容的。 

当导出目标来安装时，双冒号的NAMESPACE被指定。双冒号的这个转化给CMake一个提示，那就是当它被下游的target_link_libraries()命令使用时，这个名字是一个IMPORTED目标。

在这种场景下，当使用install(TARGETS)时，IMCLUDES DESTINATION被指定。这导致IMPORTED目标  使得它们的INTERFACE_INCLUDE_DIRECTORIES产生在  CMAKE_INSTALL_PREFIX中的include目录中。当IMPORTED目标被下游使用时，它自动消费了来自那个属性中的条目。

###创建一个包配置文件
在这种场景下，ClimbingStatsConfig.cmake文件会很简单：
```
include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStatsTargets.cmake")
```
允许下游使用IMPORTED目标。如果任何宏定义应该被ClimbingStats包提供，它们应该在一个分离的文件，这个文件被安装在和ClimbingStatsConfig.cmake相同的位置，并被在那里被包含。

这个也可以被扩展，用来遮盖依赖：
```
# ...
add_library(ClimbingStats SHARED climbingstats.cpp)
generate_export_header(ClimbingStats)

find_package(Stats 2.6.4 REQUIRED)
target_link_libraries(ClimbingStats PUBLIC Stats::Types)
```
由于Stats::Types目标是ClimbingStats的一个PUBLIC依赖，下游必须也找到Stats包，然后连接到Stats::Types库。Stats包应该被在ClimbingStatsConfig.cmake文件中找到来确保这个事情。来自于CMakeFindDependencyMacro的find_dependency宏也有助于这个事情，即传递这个包是REQUIERD还是QUIET等。一个包的所有REQUIRED依赖应该可以在Config.cmake文件中找到。
```
include(CMakeFindDependencyMacro)
find_dependency(Stats 2.6.4)

include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStatsTargets.cmake")
include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStatsMacros.cmake")
```
find_dependency宏也会设置ClimbingStats_FOUND为False，如果依赖没有被找到，会带有一个诊断，即 没有Stats包，ClimbingStats包就不能被使用。

当下游使用find_packages()时，如果COMPONENTS被指定，它们会被在`<Package>_FIND_COMPONENTS`变量中列出。如果特殊的组件是非可选的，`<Package>_FIND_REQUIRED_<comp>`会被设置为true。这可以在包配置文件中进行逻辑测试：
```
include(CMakeFindDependencyMacro)
find_dependency(Stats 2.6.4)

include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStatsTargets.cmake")
include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStatsMacros.cmake")

set(_supported_components Plot Table)

foreach(_comp ${ClimbingStats_FIND_COMPONENTS})
  if (NOT ";${_supported_components};" MATCHES _comp)
    set(ClimbingStats_FOUND False)
    set(ClimbingStats_NOTFOUND_MESSAGE "Unsupported component: ${_comp}")
  endif()
  include("${CMAKE_CURRENT_LIST_DIR}/ClimbingStats${_comp}Targets.cmake")
endforeach()
```
在这里，ClimbingStats_NOTFOUND_MESSAGE被设置为一个诊断，即  包不能被找到，是因为无效的组件被指定了。  在任何场景下只要 _FOUND变量被设置为False，这个信息变量可以被设置，然后会被展示给用户。

####为构建树创建一个包配置文件
export(EXPORT)命令创建了一个IMPORTED目标定义文件，该文件是构建树特定的，不是可重定位的。这可以类似地通过合适的包配置文件和包版本文件被使用，来为构建树定义一个包，该构建树可能被免安装使用。构建树的消费者可以简单地确保 CMAKE_PREFIX_PATH 包含构建目录，或者设置`ClimbingStats_DIR`为缓存中的`<build_dir>/ClimbingStats` 。

###创建可重定位的包
可重定位的包必须不能引用包被构建电脑上的文件的绝对路径，因为包被安装的电脑可能没有该文件。

通过install(EXPORT)命令创建的包被设计为可重定位的，使用包自己的位置的相对位置。当为EXPORT定义一个目标的接口时，记住包含目录应该被指定为相对路径，相对于 CMAKE_INSTALL_PREFIX：
```
target_include_directories(tgt INTERFACE
  # Wrong, not relocatable:
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_PREFIX}/include/TgtName>
)

target_include_directories(tgt INTERFACE
  # Ok, relocatable:
  $<INSTALL_INTERFACE:include/TgtName>
)
```

`$<INSTALL_PREFIX> `生成器表达式可能作为安装前缀的占位符被使用，而不会导致一个非可重用的包。如果复杂的生成器语法被使用，这是必要的：
```
target_include_directories(tgt INTERFACE
  # Ok, relocatable:
  $<INSTALL_INTERFACE:$<$<CONFIG:Debug>:$<INSTALL_PREFIX>/include/TgtName>>
)target_include_directories(tgt INTERFACE
  # Ok, relocatable:
  $<INSTALL_INTERFACE:$<$<CONFIG:Debug>:$<INSTALL_PREFIX>/include/TgtName>>
)
```
这也会被用于相对于外部依赖的的路径。不推荐 构成可能包含路径的任何依赖，比如 INTERFACE_INCLUDE_DIRECTORIES和INTERFACE_LINK_LIBRARIES，带有相对于依赖的路径。比如，这个代码对于可重定位的包可能不好用：

```
target_link_libraries(ClimbingStats INTERFACE
  ${Foo_LIBRARIES} ${Bar_LIBRARIES}
  )
target_include_directories(ClimbingStats INTERFACE
  "$<INSTALL_INTERFACE:${Foo_INCLUDE_DIRS};${Bar_INCLUDE_DIRS}>"
  )
```

被引用的变量可能包含库的绝对路径，和在包被构建的电脑的上的包含目录。这会创建一个包，这个包带有依赖的硬编码路径    ，而且不适用于可重定位。

理论上，这样的依赖应该通过它们自己的IMPORTED目标来使用，  这些目标有它们自己的IMPORTED_LOCATION和使用要求，比如恰当填充的`INTERFACE_INCLUDE_DIRECTORIES`。这些导入的目标可能为ClimbingStats被通过 target_link_libraries() 命令使用：

```
target_link_libraries(ClimbingStats INTERFACE Foo::Foo Bar::Bar)
```

通过这个方法，只有通过IMPORTED目标的名字，包引用它的外部依赖 。当一个消费者使用已经安装的包，消费者会允许合适的find_package()命令（通过上文描述的find_dependency宏定义）来找到依赖，并且使用它们自己的电脑的合适的路径填充导入的目标。

不幸的是，很多CMake带有的模块没有提供IMPORTED目标，因为它们的开发环境早于这个方法。这可能会随着时间改善。创建可重定位的包的工作区使用的模块包括：

- 当构建包的时候，指定每个Foo_LIBRARY缓存条目为仅仅一个库名字，比如-DFoo_LIBRARY=foo。这告诉相对应的查找模块仅仅用foo填充Foo_LIBRARIES，从而 请求链接器查找库，而不是硬编码的位置。

- 在安装包内容之后，但是在创建包安装重新发布的二进制之前，可以通过安装工具手工替代带有占位符的绝对路径，  当包被安装之后。 

##包注册
CMake提供了两种中心位置来注册在一个系统上的任何位置已经构建或者安装的包：

- 用户包注册
- 系统包注册

注册特别有用的是，可以帮助项目找到包 非标准的安装位置或者直接在它们自己的构建树。项目可能填充用户或者系统注册（使用它自己的方式，看下文）来参考它自己的位置。不管是哪种情况，包应该在注册了的位置存储 一个包配置文件（在一个系统上）和可选的一个包版本文件（<package>ConfigVersion.cmake）。

find_package()命令搜索两个包，这两个包注册为两种搜索步长，步长在它的文档中指定。如果它有充分的权限，它也移除陈旧的包注册条目，条目参考了 不存在或者不包含一个包匹配配置文件的目录。

###用户包注册表
用户包注册表在pre-user位置存储。export(PACKAGE)命令可能被用来在用户包注册信息中注册项目构建树。CMake当前没有提供接口来增加安装树到用户的包注册表。安装者必须被手动  教会注册它们的包，如果被希望的话。

在Windows平台，用户包注册信息存储在Windows注册表的HKEY_CURRENT_USER的值中。

<package>可能在注册键下面出现：

```
HKEY_CURRENT_USER\Software\Kitware\CMake\Packages\<package>
```

作为REG_SZ值，带有任意的名字，该名字指定了包含包配置文件的目录。

在UNIX平台，用户包注册表存储在用户的home目录，在~/.cmake/packages下面。<package>可能作为一个文件在目录：

```
~/.cmake/packages/<package>
```

下面出现，带有任意的名字，文件的内容指定了包含配置文件的目录。

###系统包注册

系统包注册表存储在系统范围的位置。CMake当前没有提供接口来增加到系统包注册表。安装者必须被手动交给注册他们的包，如果希望的话。

在Windows上，系统包注册表存储在Windows注册表，在HKEY_LOCAL_MACHINE的key下面。<package>可能出现在注册表键下面：

```
HKEY_LOCAL_MACHINE\Software\Kitware\CMake\Packages\<package>
```

作为一个REG_SZ值，带有任意的名字，该名字指定了包含包配置文件的目录。

在非windows平台上没有系统包注册表。

###禁用包注册表

在一些场合，使用包注册表是不可取的。CMake允许禁用它们通过使用下面的变量：

- CMAKE_EXPORT_NO_PACKAGE_REGISTRY 禁用了export(PACKAGE)命令。
- CMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY 在所有的find_package()调用禁用了用户包注册表中。
- CMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY在所有的find_packages()中 禁用了系统包注册表。

###包注册表例子

一种命名包注册表条目简单的转换方式是使用内容哈希。它们是确定性的，不可能碰撞（export(PACKAGE)使用这种方法）。  把特定的目录的条目的名字 是简单的目录路径自身的内容哈希。

如果项目为包注册表条目的存在做准备，比如：

```
> reg query HKCU\Software\Kitware\CMake\Packages\MyPackage
HKEY_CURRENT_USER\Software\Kitware\CMake\Packages\MyPackage
 45e7d55f13b87179bb12f907c8de6fc4 REG_SZ c:/Users/Me/Work/lib/cmake/MyPackage
 7b4a9844f681c80ce93190d4e3185db9 REG_SZ c:/Users/Me/Work/MyPackage-build
```

或者

```
$ cat ~/.cmake/packages/MyPackage/7d1fb77e07ce59a81bed093bbee945bd
/home/me/work/lib/cmake/MyPackage
$ cat ~/.cmake/packages/MyPackage/f92c1db873a1937f3100706657c63e07
/home/me/work/MyPackage-build
```

然后CMakeLists.txt代码是：

```
find_package(MyPackage)
```
会 为包配置文件（MyPackageConfig.cmake）搜索注册了的位置。在包注册条目中的为单一的包的搜索次序不是特定的，而且 条目名字（在这个例子中哈希）没有意义。 

注册位置可能包含包版本文件（MyPackageConfigVersion.cmake）来告诉find_package() 指定的位置是否适于版本请求。

###包注册表所有权
包注册表条目是单独地被它们引用的项目的安装位置拥有。包安装者器有责任增加它自己的条目，对应的卸载器负责卸载它。

export(PACKAGE)命令  用项目构建树的位置  构建了用户包注册表。构建树趋向于被开发者删除 ，而且 必须没有可能触发卸载它们的条目的卸载事件。为了保持注册表干净，find_package()命令自动移除它遇到的陈旧的条目， 如果它有充分的权限的话。一旦export(PACKAGE)已经被触发，CMake没有提供接口来移除条目 引用一个存在的构建树。但是，如果项目从构建树移除了它的包配置文件，然后引用到位置的条目会被认为陈旧的。






