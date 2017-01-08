add_definitions
===

add_definitions 为源文件的编译添加由-D引入的define flag。

```
add_definitions(-DFOO -DBAR ...)
```

在编译器的命令行上，为当前路径以及下层路径的源文件加入一些define flag。这个命令可以用来引入任何flag，但是它的原意是用来引入预处理器的定义。那些以-D或/D开头的、看起来像预处理器定义的flag，会被自动加到当前路径的COMPILE_DEFINITIONS属性中。为了后向兼容，非简单值（non-trival，指的是什么？——译注）的定义会被留在flags组（flags set）里，而不会被转换。关于在特定的域以及配置中增加预处理器的定义，参考路径、目标以及源文件的COMPILE_DEFINITIONS属性来获取更多的细节。

