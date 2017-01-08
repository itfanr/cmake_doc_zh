link_directories
===

link_directories 指定连接器查找库的路径。

  link_directories(directory1 directory2 ...)
　　指定连接器搜索库文件时的路径。该命令仅仅能用在那些在它被调用后才生成的目标上。由于历史上的原因，为该命令指定的相对路径将会不加改变地传递给连接器（不像许多其他CMake命令那样解释为相对于当前源路径的相对路径。）

