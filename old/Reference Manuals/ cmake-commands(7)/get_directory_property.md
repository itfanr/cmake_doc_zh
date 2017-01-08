get_directory_property
===

get_directory_property  获取DIRECTORY域中的某种属性。

  get_directory_property(<variable> [DIRECTORY <dir>] <prop-name>)
　　在指定的变量中存储路径（directory）域中的某种属性。如果该属性没有被定义，将会返回空字符串。DIRECTORY参数指定了要取出的属性值的另一个路径。指定的路径必须已经被CMake遍历过了。

   get_directory_property(<variable> [DIRECTORY <dir>]
                         DEFINITION <var-name>)

　　该命令从一个路径中获取一个变量的定义。这种格式在从另一个路径中获取变量的定义时比较有用。

