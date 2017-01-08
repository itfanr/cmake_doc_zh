get_source_file_property
===

 get_source_file_property  为一个源文件获取一种属性值。

  get_source_file_property(VAR file property)
　　从一个源文件中获取某种属性值。这个属性值存储在变量VAR中。如果该属性没有被找到，VAR会被设置为NOTFOUND。使用set_source_files_proterties命令来设置属性值。源文件属性通常用来控制文件如何被构建。一个必定存在的属性是LOCATION。

