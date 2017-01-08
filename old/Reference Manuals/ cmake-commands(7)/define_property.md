define_property
===

define_property: 定义并描述（Document）自定义属性。

 　　define_property(<GLOBAL | DIRECTORY | TARGET | SOURCE |
                    TEST | VARIABLE | CACHED_VARIABLE>
                    PROPERTY <name> [INHERITED]
                    BRIEF_DOCS <brief-doc> [docs...]
                    FULL_DOCS <full-doc> [docs...])

　　在一个域（scope）中定义一个可以用set_property和get_property命令访问的属性。这个命令对于把文档和可以通过get_property命令得到的属性名称关联起来非常有用。第一个参数确定了这个属性可以使用的范围。它必须是下列值中的一个：

  GLOBAL    = 与全局命名空间相关联
  DIRECTORY = 与某一个目录相关联
  TARGET    = 与一个目标相关联
  SOURCE    = 与一个源文件相关联
  TEST      = 与一个以add_test命名的测试相关联
  VARIABLE  = 描述（document）一个CMake语言变量
  CACHED_VARIABLE = 描述（document）一个CMake语言缓存变量
　　注意，与set_property和get_property不相同，不需要给出实际的作用域；只有作用域的类型才是重要的。PROPERTY选项必须有，它后面紧跟要定义的属性名。如果指定了INHERITED选项，那么如果get_property命令所请求的属性在该作用域中未设置，它会沿着链条向更高的作用域去搜索。DIRECTORY域向上是GLOBAL。TARGET，SOURCE和TEST向上是DIRECTORY。

　　BRIEF_DOCS和FULL_DOCS选项后面的参数是和属性相关联的字符串，分别作为变量的简单描述和完整描述。在使用get_property命令时，对应的选项可以获取这些描述信息。

