cmake_minimum_required
===

cmake_minimum_required  设置一个工程所需要的最低CMake版本。

  cmake_minimum_required(VERSION major[.minor[.patch[.tweak]]]
                         [FATAL_ERROR])
　　如果CMake的当前版本低于指定的版本，它会停止处理工程文件，并报告错误。当指定的版本高于2.4时，它会隐含调用：

  cmake_policy(VERSION major[.minor[.patch[.tweak]]])
　　从而将cmale的策略版本级别设置为指定的版本。当指定的版本是2.4或更低时，这条命令隐含调用：

  cmake_policy(VERSION 2.4)
　　这将会启用对于CMake 2.4及更低版本的兼容性。

　　FATAL_ERROR选项是可以接受的，但是CMake 2.6及更高的版本会忽略它。如果它被指定，那么CMake 2.4及更低版本将会以错误告终而非仅仅给出个警告。

