cmake-developer(7)
===


##引言
这个手册旨在给那些想修改CMake源码本身的开发者提供参考。

##被允许的C++子集

CMake被要求使用古老的C++编译器和标准库实现 构建。一些公共的C++构造可能不会被在CMake中使用，为了使用这样的工具链。

###std::auto_ptr

 一些实现有一个std::auto_ptr，它不能被用作函数的返回值。std::auto_ptr可能不被使用。可以使用cmsys:aotu_ptr替代。
 
### size_t
 
各种实现有size_t的不同实现方式。当给一个容器的.size() 赋值时，比如，结果应该被赋值给size_t，而不是std::size_t、unsigned int或者类似的类型。 
 
## 增加编译特色

如果知道了编译器 的特色  不会报告 可以支持一个特殊的请求特色，CMake报告一个错误。一个编译器被认为已经知道特色，如果它报告支持至少一个特色。
 
 当增加一个新的编译器特色到CMake，因此  有必要为所有的Compilerids列出支持的特色，这些编译器已经有一个或更多的特色支持，如果新的特色对编译器的任意版本是可用的。
 
 当增加第一个支持的特色到特别的Compilerid，有必要列出被cmake知道的所有的特色（参考 `CMAKE_C_COMPILE_FEATURES` 和 `CMAKE_CXX_COMPILE_FEATURES`），对编译器是可用的。确保`CMAKE_<LANG>_STANDARD_DEFAULT`被设置，这是为了计算内部的变量`CMAKE_<LANG>_STANDARD_COMPUTED_DEFAULT`为应该被支持的编译器版本 。
 
 为一个特殊Compilerid大多数的最新版本的记录特色，然后可以向后兼容，这是很明智的。 尝试创建编译器的特色发布的一个连续范围的版本，这是明智的。在范围内的差异表明了为中间发布版本而记录的不正确的特色。 
 
 通常情况下，为一个特殊的版本特色被设置为可用的 ，如果编译器开发者记录了那个版本的特色的可用性。注意有时候部分实现的特色 好像是在前一个版本可以使用（比如cxx_constexpr在GNU 4.6，尽管它在GNU 4.7才写入文档），有时候编译器开发者记录了特色的可用性，尽管支持体系并不完善（比如__has_feature(cxx_generic_lambdas)表明了在CLang3.4中不可用，尽管文档里写的是可用。在Clang3.5修复了这个bug）。需要调查 其他编译器和版本的类似的例子，在扩展到CMake来支持它们之前。
 
 当一个开发者发布已知的编译器的一个新版本，该版本支持之前不支持的特色 。如果已经知道编译器的特色 ，那么 应该尽快列出 CMake 为编译器的那个版本 支持的特色。
 
 标准特定的/编译器特定的变量，比如CMAKE_CXX98_COMPILE_FEATURES  故意没有写入到文档。它们只在编译器特定的实现存在，为需要的编译器增加-std编译器标记 。
 
##Help

### 组成构建

### CMake域

### 相互参考

### 类型

#### 类型

#### Style: Section Headers
#### Style: Whitespace
#### Style: Line Length
#### Style: Prose
#### Style: Starting Literal Blocks
#### Style: CMake Command Signatures
#### Style: Boolean Constants
#### Style: Inline Literals
#### Style: Cross-References
#### Style: Referencing CMake Concepts
#### Style: Referencing CMake Domain Objects

##Modules
### Module Documentation

### Find Modules
#### Standard Variable Names
####A Sample Find Module

