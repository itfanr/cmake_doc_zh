
向源码文件的编译过程增加编译选项。

```
add_compile_options(<option> ...)
```

向当前目录以及下面的目标的编译命令行增加编译选项，这些选项在编译命令被触发后被加入。

这个命令可以被用来增加任何的选项，除了已存在的预处理定义命令`(target_compile_definitions() 和 add_definitions()`以及包含目录命令`(target_include_directories() 和 include_directorie()`。

add_compile_options的参数可以使用“生成表达式”，使用的语法是`$<...>`。可以查看cmake-generator-expressions指南来获取可用的表达式。可以查看cmake-buildsystem指南获取更多定义编译系统的属性。