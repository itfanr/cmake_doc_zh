cmake-generators(7)
===

# cmake-generators(7)

标签（空格分隔）： 未分类

---

网址：https://cmake.org/cmake/help/v3.3/manual/cmake-generators.7.html

CMake生成器是用来负责为本地构建系统来写输入文件的。准确地说，CMake生成器的每个必须被选择来为构建树决定使用哪个本地构建系统。可选地，外部生成器的其中之一可能被选择为一些命令行构建工具生成器的变种，来为辅助IDE产生项目文件。

CMake生成器是平台特定的，所以有可能会只能在特定平台上可用。cmake(1)命令行工具--help输出了一个在当前平台上可用的生成器列表。使用-G选项来为一个新的构建树的指定生成器。cmake-gui(1)提供了当创建新的构建树时的可交互的选项。

##CMake生成器

### 命令行构建工具生成器

#### Makefile生成器

#### Ninja Generator

### IDE生成器

#### Visual Studio 生成器

#### 其他生成器

### 额外生成器


在CMake 2.8.3平台上，CMake支持下列生成器：

Borland Makefiles: 生成Borland makefile。
MSYS Makefiles: 生成MSYS makefile。
生成的makefile用use /bin/sh作为它的shell。在运行CMake的机器上需要安装msys。

MinGW Makefiles: 生成供mingw32-make使用的make file。
生成的makefile使用cmd.exe作为它的shell。生成它们不需要msys或者unix shell。

NMake Makefiles: 生成NMake makefile。
NMake Makefiles JOM: 生成JOM makefile。
Unix Makefiles: 生成标准的UNIX makefile。
在构建树上生成分层的UNIX makefile。任何标准的UNIX风格的make程序都可以通过默认的make目标构建工程。生成的makefile也提供了install目标。

Visual Studio 10: 生成Visual Studio 10 工程文件。
Visual Studio 10 Win64: 生成Visual Studio 10 Win64 工程文件。
Visual Studio 6: 生成Visual Studio 6 工程文件。
Visual Studio 7: 生成Visual Studio .NET 2002 工程文件。
Visual Studio 7 .NET 2003: 生成Visual Studio .NET 2003工程文件。
Visual Studio 8 2005: 生成Visual Studio .NET 2005 工程文件。
Visual Studio 8 2005 Win64: 生成Visual Studio .NET 2005 Win64工程文件。
Visual Studio 9 2008: 生成Visual Studio 9 2008 工程文件。
Visual Studio 9 2008 Win64: 生成Visual Studio 9 2008 Win64工程文件。
Watcom WMake: 生成Watcom WMake makefiles。
CodeBlocks - MinGW Makefiles: 生成CodeBlock工程文件。
在顶层目录以及每层子目录下为CodeBlocks生成工程文件，生成的CMakeList.txt的特点是都包含一个PROJECT()调用。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

CodeBlocks - NMake Makefiles: 生成CodeBlocks工程文件。
在顶层目录以及每层子目录下为CodeBlocks生成工程文件，生成的CMakeList.txt的特点是都包含一个PROJECT()调用。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

CodeBlocks - Unix Makefiles: 生成CodeBlocks工程文件。
在顶层目录以及每层子目录下为CodeBlocks生成工程文件，生成的CMakeList.txt的特点是都包含一个PROJECT()调用。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

Eclipse CDT4 - MinGW Makefiles: 生成Eclipse CDT 4.0 工程文件。
在顶层目录下为Eclipse生成工程文件。在运行源码外构建时，一个连接到顶层源码路径的资源文件会被创建。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

Eclipse CDT4 - NMake Makefiles: 生成Eclipse CDT 4.0 工程文件。
在顶层目录下为Eclipse生成工程文件。在运行源码外构建时，一个连接到顶层源码路径的资源文件会被创建。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

Eclipse CDT4 - Unix Makefiles: 生成Eclipse CDT 4.0 工程文件。
在顶层目录下为Eclipse生成工程文件。在运行源码外构建时，一个连接到顶层源码路径的资源文件会被创建。除此之外还会在构建树上生成一套层次性的makefile。通过默认的make目标，正确的make程序可以构建这个工程。makefile还提供了install目标。

参考：

http://www.cnblogs.com/coderfenghc/archive/2012/06/18/2553964.html


