add_dependencies
===

add_dependencies 为顶层目标引入一个依赖关系。

```
add_dependencies(target-name depend-target1
                   depend-target2 ...)
```

让一个顶层目标依赖于其他的顶层目标。一个顶层目标是由命令ADD_EXECUTABLE，ADD_LIBRARY，或者ADD_CUSTOM_TARGET产生的目标。为这些命令的输出引入依赖性可以保证某个目标在其他的目标之前被构建。查看ADD_CUSTOM_TARGET和ADD_CUSTOM_COMMAND命令的DEPENDS选项，可以了解如何根据自定义规则引入文件级的依赖性。查看SET_SOURCE_FILES_PROPERTIES命令的OBJECT_DEPENDS选项，可以了解如何为目标文件引入文件级的依赖性。

