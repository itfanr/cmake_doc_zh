set_source_files_properties
===

set_source_files_properties 源文件有一些属性来可以改变它们构建的方式。

  set_source_files_properties([file1 [file2 [...]]]
                              PROPERTIES prop1 value1
                              [prop2 value2 [...]])
　　以键/值对的方式设置与源文件相关的那些属性值。那些CMake中的源文件属性，参见关于属性的相关文档。不能被识别的属性将会被忽略。源文件属性只对同一路径（CMakeLists.txt）中添加的目标可见。

