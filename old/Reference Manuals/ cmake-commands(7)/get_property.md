get_property
===

get_property 获取一个属性值

  get_property(<variable>
               <GLOBAL             |
                DIRECTORY [dir]    |
                TARGET    <target> |
                SOURCE    <source> |
                TEST      <test>   |
                CACHE     <entry>  |
                VARIABLE>
               PROPERTY <name>
               [SET | DEFINED | BRIEF_DOCS | FULL_DOCS])
　　获取在某个域中一个对象的某种属性值。第一个参数指定了存储属性值的变量。第二个参数确定了获取该属性的域。域的选项仅限于：

GLOBAL 域是唯一的，它不接受域名字。
DIRECTORY域默认为当前目录，但是其他的路径（已经被CMake处理过）可以以相对路径或完整路径的方式跟在该域后面。
TARGET域后面必须跟有一个已有的目标名。
SOURCE域后面必须跟有一个源文件名。
TEST域后面必须跟有一个已有的测试。
CACHE域后面必须跟有一个cache条目。
VARIABLE域是唯一的，它不接受域名字。
　　PROPERTY选项是必须的，它后面紧跟要获取的属性名。如果该属性没有被设置，该命令将返回空值。如果给定了SET选项，那么返回值会被设置为一个布尔值，用来指示该属性是否被设置过。如果给定了DEFINED选项，那么返回值会被设置为一个布尔值，用来指示该属性是否被类似于define_property的命令定义过。如果指定了BRIEF_DOCS或者FULL_DOCS选项，那么该变量将会被设置为被查询属性的文档的字符串。如果被请求的属性的文档没有被定义，将返回NOTFOUND。

