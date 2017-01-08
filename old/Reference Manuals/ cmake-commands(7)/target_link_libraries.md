target_link_libraries
===

target_link_libraries  将给定的库链接到一个目标上。

  target_link_libraries(<target> [item1 [item2 [...]]] [[debug|optimized|general] <item>] ...)
　　为给定的目标设置连接时使用的库或者标志(flags)。如果一个库名字与工程中的另外一个目标相匹配，一个依赖关系会自动添加到构建系统中来，这样就可以在链接目标之前，保证正在被链接的库是最新的。以“-”开始，但不是“-l”或“-framework”的那些项，将会被当作链接器标志来处理。

　　关键字“debug”，“optimized”或者“general” 表示紧随关键字之后的库仅仅会被用到相应的构建配置上。“debug”关键字对应于调试配置（或者，如果全局属性DEBUG_CONFIGURATIONS被设置的话，就是DEBUG_CONFIGURATIONS中的名字所指定的配置）。“optimized”关键字对应于所有其他的配置类型。“general”关键字对应于所有的配置，并且纯粹是可选的（它是默认配置，可以省略）。通过创建并链接到导入库目标，可以对每种配置规则进行更细致的粒度控制。更多内容参见add_library命令的IMPORTED模式。

　　默认时，库之间的依赖性是可传递的。当这个目标被链接到其他目标上时，那么链接到这个目标上的库也会出现在其他目标的链接依赖上。参见LINK_INTERFACE_LIBRARIES属性的相关文档，其中有关于如何覆盖一个目标的链接依赖性传递设置的介绍。

  target_link_libraries(<target> LINK_INTERFACE_LIBRARIES [[debug|optimized|general] <lib>] ...)
　　对于LINK_INTERFACE_LIBRARIES模式，它将会把库附加在LINK_INTERFACE_LIBRARIES以及LINK_INTERFACE_LIBRARIES在不同配置下的等价目标属性，而不是用这些库去链接。指定为“debug”的库将会被附加到LINK_INTERFACE_LIBRARIES_DEBUG属性（或者是在DEBUG_CONFIGURATIONS全局属性中列出的配置，如果DEBUG_CONFIGURATIONS被设置的话）。指定为“optimized”库将会被附加到LINK_INTERFACE_LIBRARIES属性上。指定为“general”的库（或者没有任何关键字的库），将会被当做即被指定为“debug”又被指定为“optimized”对待。

　　库之间的依赖图通常是非循环图（DAG），但是如果出现互相依赖的静态库，CMake会允许依赖图中包含循环依赖（强连通分支）。当其它目标链接到这些库中的一个时，CMake会重复整个连通分支。例如，代码：

1
2
3
4
5
6
add_library(A STATIC a.c)
add_library(B STATIC b.c)
target_link_libraries(A B)
target_link_libraries(B A)
add_executable(main main.c)
target_link_libraries(main A)
将“main”链接到了“A B A B”。（虽然通常一次重复就足够了，但是病态对象文件以及符号排布可能需要多次重复。你可以通过在上一次target_link_libraries调用中手动重复该分支来处理这种情况。不过，如果两个归档文件确实是如此紧密的相互关联，它们可能会被合并为一个单一的归档文件。） 

