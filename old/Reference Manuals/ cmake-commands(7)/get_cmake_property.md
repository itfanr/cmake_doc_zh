get_cmake_property
===

get_cmake_property  获取一个CMake实例的属性。

  get_cmake_property(VAR property)
　　从指定的CMake实例中获取属性。属性的值存储在变量VAR中。如果属性不存在，CMake会报错。一些会被支持的属性包括：VATIABLES，COMMANDS，MACROS以及COMPONENTS。

