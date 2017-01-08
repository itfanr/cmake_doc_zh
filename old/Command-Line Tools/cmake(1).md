# cmake(1)

标签（空格分隔）： cmake

---

网址：https://cmake.org/cmake/help/v3.4/manual/cmake.1.html

### 翻译参考：

* http://www.cnblogs.com/coderfenghc/archive/2012/06/16/CMake_ch_01.html
* http://www.cnblogs.com/coderfenghc/archive/2012/06/17/2552899.html

### 摘要

```
cmake [<options>] (<path-to-source> | <path-to-existing-build>)
cmake [(-D <var>=<value>)...] -P <cmake-script-file>
cmake --build <dir> [<options>] [-- <build-tool-options>...]
cmake -E <command> [<options>...]
cmake --find-package <options>...
```

### 描述
cmake二进制文件是CMake的命令行工具接口，可以用来以脚本的形式配置工程。项目配置设置可以通过在命令行用-D参数指定。

CMake是一个跨平台的构建系统生成器。在每个源码树的子目录都有一个名为CMakeLists.txt的平台独立的CMake listfiles，项目通过这些文件指定其的构建过程。用户通过使用CMake在他们的平台的本地工具来产生一个构建系统，从而构建一个项目。

### 选项

- -C <initial-cache>：预加载脚本，从而在缓存中分配空间。

当cmake是在一个空构建树上第一次运行时，它会创建CMakeCache.txt文件，并使用自定义的设置来在其中分配空间。这个选项可以用来指定一个文件，使得可以在第一次遍历项目的cmake listfiles之前加载缓存条目。加载的条目优先于项目的默认值。给定的文件应该是CMake的脚本，包含使用CACHE选项的SET命令，而不是一个缓存格式的文件。

- -D <var>:<type>=<value>, -D <var>=<value>：创建一个cmake cache条目。

当cmake是在一个空构建树上第一次运行时，它会创建CMakeCache.txt文件，并使用自定义的设置来在其中分配空间。这个选项可以用来指定一个文件，使得可以在第一次遍历项目的cmake listfiles之前加载缓存条目。加载的条目优先于项目的默认值。这个选项可以重复，有多少cache条目，就可以指定多少。

如果`:<type>`的份额指定了，它必须是通过[set()](https://cmake.org/cmake/help/v3.4/command/set.html#command:set)命令指定的类型中的其中一个。如果`:<type>`被省略，那么条目会被创建为无类型的，如果它不是一个存在的类型。如果在项目中的命令设置type为`PATH`或者`FILEPATH`，那么`<value>`会被转换为绝对路径。

这个选项也可以用一个参数来指定：`-D<var>:<type>=<value> `或者`-D<var>=<value>`。

- -U <globbing_expr> ：从CMake cache中删除匹配的条目。

这个选项可能会被用来从CMakeCache.txt文件中删除一个或者多个变量，通配符语法`*`和`？`都支持。这个选项可能因为可能的缓存条目而重复多次。

小心使用，否则你可能会把CMakeCache.txt弄得不能正常使用了。

- -G <generator-name>：指定一个构建系统生成器。

CMake可能支持多种在特定的平台的本地构建系统 。生成器有责任产生一个特殊的构建系统。可能的生成器名字在cmake-generators(7)手册中指定。

- -T <toolset-name>：指定被生成器支持工具集名字 。

相同的CMake生成器支持把一个工具集名字 给本地构建系统选择一个编译器。这仅仅被特定的生成器支持：

```
Visual Studio >= 10
Xcode >= 3.0
```

参考本地构建系统文档获得允许的工具集名字。

- -A <platform-name>：指定被生成器支持的平台名字。

一些CMake生成器支持把平台名字给本地构建系统  来选择一个编译器或者SDK。这仅仅被在特定的生成器支持：

```
Visual Studio >= 8
```

参考本地构建系统文档获得允许的平台名字。

- -Wno-dev ：抑制开发者警告。

抑制用于CMakeLists.txt文件的作者的警告。

- -Wdev ：激活开发者警告。

激活用于CMakeLists.txt文件的作者的警告。

-  -E <command> [<options>...]：参考Command-Line Tool Mode.。

- -L[A][H]：列出非高级的缓存变量。

列出会运行CMake缓存变量和列出来自不被标记为INTERNAL或者ADVANCED的CMake缓存变量 。这会高效地显示当前的CMake设置，该设置可以通过-D选项来改变。改变变量的一些会导致更多的变量被创建。如果`A`被指定，它也会显示高级变量。如果`H`被指定，它也会显示每个变量的help信息。

- --build <dir>：构建由CMake生成的项目二进制树。

这使用如下的选项抽象了本地构建工具的命令行接口：

```
<dir>          = Project binary directory to be built.
--target <tgt> = Build <tgt> instead of default targets.
--config <cfg> = For multi-configuration tools, choose <cfg>.
--clean-first  = Build target 'clean' first, then build.
                 (To clean only, use --target 'clean'.)
--use-stderr   = Ignored.  Behavior is default in CMake >= 3.0.
--             = Pass remaining options to the native tool.
```

运行`cmake -build`命令，不带有参数，来获得help。

- -N：仅是浏览模式。

只会加载缓存。不要真正的运行配置和产生步骤。

- -P <file> 处理脚本模式。


处理给定的作为脚本的cmake文件，使用CMake语言编写。没有配置或者生成步骤被执行，而且缓存不会被修改。如果变量被用`-D`定义，这必须被在`-P`参数前面完成。

- --find-package：运行在类似pkg-config模式。


搜索一个包 使用find_ppackage()，然后打印结果标记到终端输出。这可以被用来使用cmake，而不是使用pkg-config来 在普通的基于Makefile项目或者在基于aotuconf的项目（通过share/aclocal/cmake.m4） 搜索安装了的库 。

- --graphviz=[file]：创建依赖的graphviz。

参考CMakeGraphVizOptions.cmake获得更多信息

产生一个graphviz输入文件 会包含在项目中的所有的库和可执行文件依赖 。参考CMakeGraphVizOptions.cmake的文档获得更多细节。

- --system-information [file]：显示关于这个系统的信息。


输出关于当前系统的宽泛的信息。如果在CMake项目的二进制树的顶层运行，它会输出额外的信息，比如cache、log等。

- --debug-trycompile: 不删除“尝试编译”路径。


不删除那些为try_compile调用生成的路径。这在调试失败的try_compile文件时比较有用。不过，因为上一次“尝试编译”生成的旧的垃圾输出文件也许会导致一次不正确通过/不通过，且该结果与上次测试的结果不同，所以该选项可能会改变“尝试编译”的结果。对于某一次“尝试编译”，该选项最好只用一次；并且仅仅在调试时使用。

- --debug-output: 将cmake设置为调试模式。


在cmake运行时，打印额外的信息；比如使用message(send_error)调用得到的栈跟踪信息。

- --trace: 将cmake设置为跟踪模式。


用message(send_error )调用，打印所有调用生成的跟踪信息，以及这些调用发生的位置。（这句话含义不是很确定—译注。）

- --trace-expand：将cmake设置为跟踪模式。


就像--trace一样，但是会把变量展开。

- --warn-uninitialized：警告未初始化的值。


当未初始化的值被使用时，打印警告。

- --warn-unused-vars：警告未使用的变量。

查找声明或者赋值了的变量，但是没被使用。

- --no-warn-unused-cli ：不要警告命令行选项。

不要警告那些在命令行声明了但是没被使用的变量。

- --check-system-vars：根据在系统文件中的变量使用说明找错误 。

正常情况下，只在CMAKE_SOURCE_DIR和CMAKE_BINARY_DIR中的未使用和未初始化的变量会被搜索 。这个标识告诉CMake也警告其他文件。

- --help,-help,-usage,-h,-H,/?：打印使用信息，然后退出。

使用说明描述了基本的命令行借和它的选项。

- --version,-version,/V [<f>]：显示程序名字/版本标记，然后退出。

如果文件被指定，版本号会写入到该文件。这个帮助信息被打印到名为<f>的文件之中，如果提供的话。

- --help-full [<f>]：打印所有的帮助指南，然后退出。 

所有的指南打印为适合人类阅读的文本格式。这个帮助信息被打印到名为<f>的文件之中，如果提供的话。

- --help-manual <man> [<f>]：打印一个帮助指南，然后退出。

指定的指南打印为适合人类阅读的文本格式。这个帮助信息被打印到名为<f>的文件之中，如果提供的话。

- --help-manual-list [<f>]：列出帮助指南变量，然后退出。

这个列表包含所有的指南，该帮助可能通过在指南名字后面使用--help-manual选项获得。这个帮助信息被打印到名为<f>的文件之中，如果提供的话。

- --help-command cmd [file]: 打印单个命令cmd的帮助信息，然后退出。

显示给定的命令的完整的文档。如果指定了[file]参数，该文档会写入该文件，其输出格式由该文件的后缀名确定。支持的文件类型有：man page，HTML，DocBook以及纯文本。

- --help-command-list [file]: 列出所有可用命令的清单，然后退出。
该选项列出的信息含有所有命令的名字；其中，每个命令的帮助信息可以使用--help-command选项后跟一个命令名字得到。如果指定了[file]参数，帮助信息会写到file中，输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-commands [file]: 打印所有命令的帮助文件，然后退出。
显示所有当前版本的命令的完整文档。如果指定了[file]参数，帮助信息会写到file中，输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-module <mod> [<f>]：打印一个模块的帮助信息，然后退出。

 <mod>的cmake-modules(7)指南条目 以适合人类阅读的文本格式打印。这个帮助信息被打印到名为<f>的文件之中，如果提供的话。

- --help-module-list [file]: 列出所有可用模块名，然后退出。

列出的清单包括所有模块的名字；其中，每个模块的帮助信息可以使用--help-module选项，后跟模块名的方式得到。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-modules [file]: 打印所有模块的帮助信息，然后退出。
显示关于所有模块的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-policy <cmp> [file]: 打印单个策略的帮助信息，然后退出。
显示给定的策略的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-policies [file]: 打印所有策略的帮助信息，然后退出。
显示所有策略的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-property <prop> [file]: 打印单个属性的帮助信息，然后退出。
显示指定属性的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-property-list [file]: 列出所有可用的属性，然后退出。
该命令列出的清单包括所有属性的名字；其中，每个属性的帮助信息都可以通过--help-property选项后跟一个属性名的方式获得。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-properties [file]: 打印所有属性的帮助信息，然后退出。
显示所有属性的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-variable var [file]: 打印单个变量的帮助信息，然后退出。
显示指定变量的完整文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-variable-list [file]: 列出文档中有记录的变量，然后退出。
该命令列出的清单包括所有变量的名字；其中，每个变量的帮助信息都可以通过--help-variable选项后跟一个变量名的方式获得。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

- --help-variables [file]: 打印所有变量的帮助信息，然后退出。
显示所有变量的完整帮助文档。如果指定了[file]参数，帮助信息会写到file中，且输出格式依赖于文件名后缀。支持的文件格式包括：man page，HTML，DocBook以及纯文本。

 
 ### 命令行模式
 
 cmake提供了内建的命令行工具，通过签名：
 
 ```
 cmake -E <command> [<options>...]
 ```
 
 运行cmake -E或者cmake -E help获得命令的概要。可用的命令是：
 
 ```
 chdir <dir> <cmd> [<arg>...]
 ```
 
 改变当前的工作目录，然后运行一个命令。
 
 ```
 compare_files <file1> <file2>
 ```
 检查file1是否和file2相同。

```
copy <file> <destination>
```
复制文件到目的地（另外的名字或者目录）。

```
copy_directory <source> <destination>
```
Copy directory ‘source’ content to directory ‘destination’.
复制目录'source'内容到目录'destination'。

```
copy_if_different <in-file> <out-file>
```
Copy file if input has changed.
复制文件，如果输入已经改变了。

```
echo [<string>...]
```
Displays arguments as text.
以文本形式显示参数。

```
echo_append [<string>...]
```
Displays arguments as text but no new line.
以文本形式显示参数，但是不产生新的一行。

```
env [--unset=NAME]... [NAME=VALUE]... COMMAND [ARG]...
```
Run command in a modified environment.
在修改过的环境运行命令。

environment
显示当前的环境。

```
make_directory <dir>
````
创建一个目录。
 
md5sum [<file>...]
计算文件的md5值。

```
remove [-f] [<file>...]
```
删除文件，使用-f参数强制删除。

```
remove_directory <dir>
```
删除目录和它的内容。

```
rename <oldname> <newname>
```
重命名一个文件或者目录。

sleep <number>...
睡眠给定的秒。

``` 
 tar [cxt][vf][zjJ] file.tar [<options>...] [--] [<file>...]
```
创建或者解压tar或者zip压缩包。选项是：

* --
停止解析选项，然后把存在的所有参数看作文件文件名，尽管它们以-开头。

* --files-from=<file>
从指定的文件读取名字。空白行被忽略。行可能不以—开头，除了--add-file=<name>来增加以-开头的名字的文件。

* --mtime=<date>
指定记录在tarall条目中的修改时间。

* --format=<format>
指定要创建的压缩包的格式。支持的格式是：`7zip`、`gnutar`、`pax`、`paxr`（默认限制为pax）和zip

```
time <command> [<args>...]
```
运行命令，然后返回花费的时间。

```
touch<file>
 ```
创建文件。

```
touch_nocreate <file>
```
创建文件，如果存在就不创建。
 
 ### UNIX特定的命令行
 下面的cmake -E命令只在UNIX平台上可用：
 
 ```
 create_symlink <old> <new>
 ```
 创建一个<old>文件的符号链接<new>。
 
 ### windows特定的命令行
  下面的cmake -E命令只在UNIX平台上可用：
  
```
 delete_regv <key>
 ```
 删除windows注册表的值。
 
 ```
 env_vs8_wince <sdkname>
 ```
 显示一个批处理文件，该文件设置了环境变量，为在安装的VS2005中的WindowsCE SDK。
 
  ```
 env_vs9_wince <sdkname>
 ```
 显示一个批处理文件，该文件设置了环境变量，为在安装的VS2008中的WindowsCE SDK。
 
 ```
 write_regv <key> <value>
 ```
 写入windows注册表值。
 
 ### 还可以看
 
 从以下的资源可以获得使用CMake的帮助：
 
 Home Page
https://cmake.org

The primary starting point for learning about CMake.

Frequently Asked Questions
https://cmake.org/Wiki/CMake_FAQ

A Wiki is provided containing answers to frequently asked questions.

Online Documentation
https://cmake.org/documentation

Links to available documentation may be found on this web page.

Mailing List
https://cmake.org/mailing-lists

For help and discussion about using cmake, a mailing list is provided at cmake@cmake.org. The list is member-post-only but one may sign up on the CMake web page. Please first read the full documentation at https://cmake.org before posting questions to the list.
 








