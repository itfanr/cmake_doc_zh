get_test_property
===

get_test_property 获取一个测试的属性。

  get_test_property(test VAR property)
　　从指定的测试中获取某种属性。属性值会被存储到变量VAR中。如果没有找到该属性，CMake将会报错。你可以使用命令cmake --help-property-list来获取标准属性的清单。

