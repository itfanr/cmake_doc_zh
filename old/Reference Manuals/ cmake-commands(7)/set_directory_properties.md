set_directory_properties
===

set_directory_properties  设置某个路径的一种属性。

  set_directory_properties(PROPERTIES prop1 value1 prop2 value2)
　　为当前的路径及其子路径设置一种属性。如果该属性不存在，CMake将会报告一个错误。属性包括：INCLUDE_DIRECTORIES, LINK_DIRECTORIES, INCLUDE_REGULAR_EXPRESSION, 以及ADDITIONAL_MAKE_CLEAN_FILES共四种。ADDITIONAL_MAKE_CLEAN_FILES是一个文件名的list，其中包含有"make clean"阶段会被清除掉的文件。

