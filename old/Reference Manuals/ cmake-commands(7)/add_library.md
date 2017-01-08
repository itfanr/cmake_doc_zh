add_library
===

add_library 使用指定的源文件向工程中添加一个库。

```
add_library(<name> [STATIC | SHARED | MODULE]
              [EXCLUDE_FROM_ALL]
              source1 source2 ...)
```

添加一个名为<name>的库文件，该库文件将会根据调用的命令里列出的源文件来创建。<name>对应于逻辑目标名称，而且在一个工程的全局域内必须是唯一的。待构建的库文件的实际文件名根据对应平台的命名约定来构造（比如lib<name>.a或者<name>.lib）。

指定STATIC，SHARED，或者MODULE参数用来指定要创建的库的类型。STATIC库是目标文件的归档文件，在链接其它目标的时候使用。SHARED库会被动态链接，在运行时被加载。MODULE库是不会被链接到其它目标中的插件，但是可能会在运行时使用dlopen-系列的函数动态链接。如果没有类型被显式指定，这个选项将会根据变量BUILD_SHARED_LIBS的当前值是否为真决定是STATIC还是SHARED。

默认状态下，库文件将会在于源文件目录树的构建目录树的位置被创建，该命令也会在这里被调用。查阅ARCHIVE_OUTPUT_DIRECTORY，LIBRARY_OUTPUT_DIRECTORY，和RUNTIME_OUTPUT_DIRECTORY这三个目标属性的文档来改变这一位置。查阅OUTPUT_NAME目标属性的文档来改变最终文件名的<name>部分。

如果指定了EXCLUDE_FROM_ALL属性，对应的一些属性会在目标被创建时被设置。查阅EXCLUDE_FROM_ALL的文档来获取该属性的细节。

使用下述格式，add_library命令也可以用来创建导入的库目标：

add_library(<name> <SHARED|STATIC|MODULE|UNKNOWN> IMPORTED)

导入的库目标是引用了在工程外的一个库文件的目标。没有生成构建这个库的规则。这个目标名字的作用域在它被创建的路径及以下有效。他可以向任何在该工程内构建的目标一样被引用。导入库为类似于target_link_libraries命令中引用它提供了便利。关于导入库细节可以通过指定那些以IMPORTED_的属性设置来指定。其中最重要的属性是IMPORTED_LOCATION（以及它的具体配置版本，IMPORTED_LOCATION_<CONFIG>），它指定了主库文件在磁盘上的位置。查阅IMPORTED_*属性的文档获取更多的信息。


