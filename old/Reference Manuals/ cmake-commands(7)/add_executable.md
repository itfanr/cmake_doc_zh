add_executable
===

add_executable: 使用给定的源文件，为工程引入一个可执行文件。

```add_executable(<name> [WIN32] [MACOSX_BUNDLE]
                 [EXCLUDE_FROM_ALL]
                 source1 source2 ... sourceN)
```

引入一个名为<name>的可执行目标，该目标会由调用该命令时在源文件列表中指定的源文件来构建。<name>对应于逻辑目标名字，并且在工程范围内必须是全局唯一的。被构建的可执行目标的实际文件名将根据具体的本地平台创建出来（比如<name>.exe或者仅仅是<name>）。

默认情况下，可执行文件将会在构建树的路径下被创建，对应于该命令被调用的源文件树的路径。如果要改变这个位置，查看RUNTIME_OUTPUT_DIRECTORY目标属性的相关文档。如果要改变最终文件名的<name>部分，查看OUTPUT_NAME目标属性的相关文档。

如果指定了MACOSX_BUNDLE选项，对应的属性会附加在创建的目标上。查看MACOSX_BUNDLE目标属性的文档可以找到更多的细节。

如果指定了EXCLUDE_FROM_ALL选项，对应的属性将会设置在被创建的目标上。查看EXCLUDE_FROM_ALL目标属性的文档可以找到更多的细节。

使用下述格式，add_executable命令也可以用来创建导入的（IMPORTED）可执行目标：

add_executable(<name> IMPORTED)

一个导入的可执行目标引用了一个位于工程之外的可执行文件。该格式不会生成构建这个目标的规则。该目标名字的作用域在它被创建的路径以及底层路径有效。它可以像在该工程内的其他任意目标一样被引用。导入可执行文件为类似于add_custom_command之类的命令引用它提供了便利。

关于导入的可执行文件的细节可以通过设置以IMPORTED_开头的属性来指定。这类属性中最重要的是IMPORTED_LOCATION（以及它对应于具体配置的版本IMPORTED_LOCATION_<CONFIG>）；该属性指定了执行文件主文件在磁盘上的位置。查看IMPORTED_*属性的文档来获得更多信息。

