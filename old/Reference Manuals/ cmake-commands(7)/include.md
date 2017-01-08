include
===

include 从给定的文件中读取CMake的列表文件。

  include(<file|module> [OPTIONAL] [RESULT_VARIABLE <VAR>]
                        [NO_POLICY_SCOPE])
　　从给定的文件中读取CMake的清单文件代码。在清单文件中的命令会被立即处理，就像它们是写在这条include命令展开的地方一样。如果指定了OPTIONAL选项，那么如果被包含文件不存在的话，不会报错。如果指定了RESULT_VARIABLE选项，那么var或者会被设置为被包含文件的完整路径，或者是NOTFOUND，表示没有找到该文件。

　　如果指定的是一个模块（module）而不是一个文件，查找的对象会变成路径CMAKE_MODULE_PATH下的文件<modulename>.camke。

　　参考cmake_policy()命令文档中关于NO_POLICY_SCOPE选项的讨论。

