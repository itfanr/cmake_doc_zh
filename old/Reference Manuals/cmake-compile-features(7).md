 

标签（空格分隔）： cmake

---

网址：https://cmake.org/cmake/help/v3.3/manual/cmake-compile-features.7.html

## 引言

项目源代码可能会依赖于或者有条件地依赖于，编译器的某个编译器的特性是否可用。有三个场景可能会出现：Compile Feature Requirements, Optional Compile Features 和 Conditional Compilation Options。

尽管特性在的编程语言标准中指定，但是CMake提供了一个主要的用户接口是基于细微的特色处理的，而不是基于包含在特色中的语言标准。

 CMAKE_C_KNOWN_FEATURES 和 CMAKE_CXX_KNOWN_FEATURES 两种全局属性包含所有CMake所了解的特性，不管编译器对这个特性支持是怎样的。CMAKE_C_COMPILE_FEATURES 和 CMAKE_CXX_COMPILE_FEATURES 变量包含所有的编译器所知道的CMake特性，不管语言支持或者使用它们需要的编译器标记是怎样的。
 
 CMake所知道的特性大多数是根据相同的转换和Clang特性测试宏命令而命名的。有一些异常，比如CMake使用cxx_final和xcc_override，而不是单一的Clang所使用的cxx_override_control。
 
## 编译特色要求

编译特色要求可以使用`target_compile_features() `命令来指定。比如，如果一个目标必须通过使用 `cxx_constexpr`编译支持来编译：

```
add_library(mylib requires_constexpr.cpp)
target_compile_features(mylib PRIVATE cxx_constexpr)
```

在处理cxx_constexpr特色的要求过程中，cmake(1)会确保  在使用中的C++编译器 有能力支持这个特色 ，会增加任意必须的标记，比如-std=gnu++11，到在mylib目标中的 C++文件的编译行。如果编译器不支持这个特色， 会报出FATAL_ERROR错误。

因为这个使用场景，故意不把准确的编译器标记和语言标准作为用户接口的一部分。通过考虑为每个目标指定的特色，CMake会计算恰当的编译标记来使用。

虽然不使用这个标记，编译器支持特殊的特色，但是这些编译标记被加入 。比如，GNU编译器支持可变的模板（带有一个warning），即使-std=gnu+98被使用。如果cxx_variadic_templates被作为一个要求指定，CMake会增加-std=gnu+11标记。

在上面的例子中，当mylib被自己构建mylib时，会要求cxx_constexpr，但是mylib的使用者不会被要求使用支持cxx_constexpr的编译器。如果mylib接口不要求cxx_constexpr特色（或者任意的其他知道的特色），这个特色可能 通过target_compile_features()的PUBLIC或者INTERFACE签名被指定。

```
add_library(mylib requires_constexpr.cpp)
# cxx_constexpr is a usage-requirement
target_compile_features(mylib PUBLIC cxx_constexpr)

# main.cpp will be compiled with -std=gnu++11 on GNU for cxx_constexpr.
add_executable(myexe main.cpp)
target_link_libraries(myexe mylib)
```

通过使用链接环节，特色要求被评估 。参考cmake-buildsystem(7)获得更多关于 构建属性和使用说明 的过渡行为。

因为 CXX_EXTENSIONS 目标属性默认为ON，默认情况下，CMake使用语言方言的扩展变体，比如-std=gnu+11，而不是-std=c++11。目标属性可能被设置为OFF来使用方言标记的非扩展变体。注意，因为多数的编译器默认激活了扩展，这可能会找出 在用户代码中或者在第三方依赖的头文件中的跨平台的bug。

## 可选的编译特色

在不创建一个硬要求的前提下，编译器特色可能是优先的，如果该特色可用的话。比如，一个库可能提供可替换的实现 ，依赖于cxx_variadic_templates特色是否可用：

```
#if Foo_COMPILER_CXX_VARIADIC_TEMPLATES
template<int I, int... Is>
struct Interface;

template<int I>
struct Interface<I>
{
  static int accumulate()
  {
    return I;
  }
};

template<int I, int... Is>
struct Interface
{
  static int accumulate()
  {
    return I + Interface<Is...>::accumulate();
  }
};
#else
template<int I1, int I2 = 0, int I3 = 0, int I4 = 0>
struct Interface
{
  static int accumulate() { return I1 + I2 + I3 + I4; }
};
#endif
```

这样的一个接口依赖于为编译器特色使用正确的预处理器定义 。CMake使用WriteCompilerDetectionHeader 模块，可以产生一个头文件，该头文件包含这样的定义。模块包括write_compiler_detection_header函数，该函数接受参数来控制产生的头文件的内容：

```
write_compiler_detection_header(
  FILE "${CMAKE_CURRENT_BINARY_DIR}/foo_compiler_detection.h"
  PREFIX Foo
  COMPILERS GNU
  FEATURES
    cxx_variadic_templates
)
```
这样的头文件可能被 在工程的源码的内部使用，在库代码的接口中可能被安装和使用 。

对于每个在FEATURES中列出的特色，在头文件中预处理定义 被创建和定义 为1或0。

另外，一些特色需要额外的定义，比如cxx_final和cxx_overried特色。final关键词不是在#ifdef代码中被使用，而是被抽象为一个符号，该符号被定义为final（一个编译器特定的等效值）或者空。
 这样，编写的C++代码可以无条件地使用这个符号，编译器的支持情况决定它被展开为什么：
 
```
struct Interface {
  virtual void Execute() = 0;
};

struct Concrete Foo_FINAL {
  void Execute() Foo_OVERRIDE;
};
```

这个例子中，如果编译器支持Foo_FINAL这个关键词，Foo_FINAL会被展开为final，，否则为空。

 在这个使用场景中，CMake代码会希望开启一个特殊的语言标准，如果在编译器中可用的话。CXX_STANDARD目标属性变量可能为特殊的目标被设置为期望的语言标准 ，同时CMAKE_CXX_STANDARD可能被设置为影响下面所有的目标：
 
 ```
 write_compiler_detection_header(
  FILE "${CMAKE_CURRENT_BINARY_DIR}/foo_compiler_detection.h"
  PREFIX Foo
  COMPILERS GNU
  FEATURES
    cxx_final cxx_override
)

# Includes foo_compiler_detection.h and uses the Foo_FINAL symbol
# which will expand to 'final' if the compiler supports the requested
# CXX_STANDARD.
add_library(foo foo.cpp)
set_property(TARGET foo PROPERTY CXX_STANDARD 11)

# Includes foo_compiler_detection.h and uses the Foo_FINAL symbol
# which will expand to 'final' if the compiler supports the feature,
# even though CXX_STANDARD is not set explicitly.  The requirement of
# cxx_constexpr causes CMake to set CXX_STANDARD internally, which
# affects the compile flags.
add_library(foo_impl foo_impl.cpp)
target_compile_features(foo_impl PRIVATE cxx_constexpr)
 ```  

write_compiler_detection_header函数也为有标准等效值的其他特色创建了兼容的代码。 比如，cxx_static_assert特色通过一个模板被评估和通过<PREFIX>_STATIC_ASSERT 和<PREFIX>_STATIC_ASSERT_MSG 函数宏被抽象。

## 有条件的编译选项

依赖于请求的编译器特色，库可能提供完全不同的头文件。

比如，头文件with_variadics/interface.h可能包含以下内容：

```
template<int I, int... Is>
struct Interface;

template<int I>
struct Interface<I>
{
  static int accumulate()
  {
    return I;
  }
};

template<int I, int... Is>
struct Interface
{
  static int accumulate()
  {
    return I + Interface<Is...>::accumulate();
  }
};
```

虽然头文件no_variadics/interface.h 可能包含：

```
template<int I1, int I2 = 0, int I3 = 0, int I4 = 0>
struct Interface
{
  static int accumulate() { return I1 + I2 + I3 + I4; }
};
```

可能写一个抽象头文件interface.h，包含这样的内容：

```
#include "foo_compiler_detection.h"
#if Foo_COMPILER_CXX_VARIADIC_TEMPLATES
#include "with_variadics/interface.h"
#else
#include "no_variadics/interface.h"
#endif
```

然而，这个可能不可维护的，如果没有很多文件来抽象。你需要做的是根据于编译器的兼容性使用选择性的包含目录。

CMake提供了COMPILE_FEATURES generator expression 来实现这样的条件。这可能被与build-property命令一起使用，比如target_include_directories() 和 target_link_libraries()命令来设置合适的构建系统属性：

```
add_library(foo INTERFACE)
set(with_variadics ${CMAKE_CURRENT_SOURCE_DIR}/with_variadics)
set(no_variadics ${CMAKE_CURRENT_SOURCE_DIR}/no_variadics)
target_include_directories(foo
  INTERFACE
    "$<$<COMPILE_FEATURES:cxx_variadic_templates>:${with_variadics}>"
    "$<$<NOT:$<COMPILE_FEATURES:cxx_variadic_templates>>:${no_variadics}>"
  )
```
假设和通常一样代码简单地链接到foo目标，然后使用特色合适的包含目录：

```
add_executable(consumer_with consumer_with.cpp)
target_link_libraries(consumer_with foo)
set_property(TARGET consumer_with CXX_STANDARD 11)

add_executable(consumer_no consumer_no.cpp)
target_link_libraries(consumer_no foo)
```


## 支持的编译器
 
 CMake当前已经掌握了很多语言标准和编译特色，目前可用的是如下的编译id作为版本，版本被指定为每个：

AppleClang: Apple Clang for Xcode versions 4.4 though 6.2.
Clang: Clang compiler versions 2.9 through 3.4.
GNU: GNU compiler versions 4.4 through 5.0.
MSVC: Microsoft Visual Studio versions 2010 through 2015.
SunPro: Oracle SolarisStudio version 12.4.



 






