set_target_properties
===

set_target_properties  设置目标的一些属性来改变它们构建的方式。

  set_target_properties(target1 target2 ...
                        PROPERTIES prop1 value1
                        prop2 value2 ...)
　　为一个目标设置属性。该命令的语法是列出所有你想要变更的文件，然后提供你想要设置的值。你能够使用任何你想要的属性/值对，并且在随后的代码中调用GET_TARGET_PROPERTY命令取出属性的值。

　　影响一个目标输出文件的名字的属性详述如下。PREFIX和SUFFIX属性覆盖了默认的目标名前缀（比如lib）和后缀（比如.so）。IMPORT_PREFIX和IMPORT_SUFFIX是与之等价的属性，不过针对的是DLL（共享库目标）的导入库。在构建目标时，OUTPUT_NAME属性设置目标的真实名字，并且可以用来辅助创建两个具有相同名字的目标，即使CMake需要唯一的逻辑目标名。<CONFIG>_OUTPUT_NAME可以为不同的配置设置输出的目标名字。当目标在指定的配置名<CONFIG>（全部大写，例如DEBUG_POSTFIX）下被构建时，<CONFIG>_POSTFIX为目标的真实名字设置一个后缀。该属性的值在目标创建时被初始化为CMAKE_<CONFIG>_POSTFIX的值（可执行目标除外，因为较早的CMake版本不会为可执行文件使用这个属性。）

　　LINK_FLAGS属性可以用来为一个目标的链接阶段添加额外的标志。LINK_FLAGS_<CONFIG>将为配置<CONFIG>添加链接标志，例如DEBUG，RELEASE，MINSIZEREL，RELWITHDEBINFO。DEFINE_SYMBOL属性设置了编译一个共享库中的源文件时才会被定义的预处理器符号名。如果这个值没有被设置的话，那么它会被设置为默认值target_EXPORTS（如果目标不是一个合法的C标示符的话可以用一些替代标志）。这对于检测头文件是包含在它们的库以内还是以外很有帮助，从而可以合理设置dllexport/dllimport修饰符（注意，只有在编译到的时候，这个符号才会被定义；因此猜测在代码中，判断预处理符号是否被定义可以知道依赖库是导入的还是导出的——译注）。COMPILE_FLAGS属性可以设置附加的编译器标志，它们会在构建目标内的源文件时被用到。它也可以用来传递附加的预处理器定义。

　　LINKER_LANGUAGE属性用来改变链接可执行文件或共享库的工具。默认的值是设置与库中的文件相匹配的语言。CXX和C是这个属性的公共值。

　　对于共享库，VERSION和SOVERSION属性分别可以用来指定构建的版本号以及API版本号。当构建或者安装时，如果平台支持符号链接并且链接器支持so名字，那么恰当的符号链接会被创建。如果只指定两者中的一个，缺失的另一个假定为具有相同的版本号。对于可执行文件，VERSION可以被用来指定构建版本号。当构建或者安装时，如果该平台支持符号链接，那么合适的符号链接会被创建。对于在Windows系统而言，共享库和可执行文件的VERSION属性被解析成为一个"major.minor"的版本号。这些版本号被用做该二进制文件的镜像版本。

　　还有一些属性用来指定RPATH规则。INSTALL_RPATH是一个分号分隔的list，它指定了在安装目标时使用的rpath（针对支持rpath的平台而言）（-rpath在gcc中用于在编译时指定加载动态库的路径；优先级较系统库路径要高。详情参见http://www.cmake.org/Wiki/CMake_RPATH_handling#What_is_RPATH_.3F——译注）。INSTALL_RPATH_USE_LINK_PATH是一个布尔值属性，如果它被设置为真，那么在链接器的搜索路径中以及工程之外的目录会被附加到INSTALL_RPATH之后。SKIP_BUILD_RPATH是一个布尔值属性，它指定了是否跳过一个rpath的自动生成过程，从而可以从构建树开始运行。BUILD_WITH_INSTALL_RPATH是一个布尔值属性，它指定了是否将在构建树上的目标与INSTALL_RPATH链接。该属性要优先于SKIP_BUILD_RPATH，因此避免了安装之前的重新链接。INSTALL_NAME_DIR是一个字符串属性，它用于在Mac OSX系统上，指定了被安装的目标中使用的共享库的"install_name"域的目录部分。如果目标已经被创建，变量CMAKE_INSTALL_RPATH, CMAKE_INSTALL_RPATH_USE_LINK_PATH, CMAKE_SKIP_BUILD_RPATH, CMAKE_BUILD_WITH_INSTALL_RPATH和CMAKE_INSTALL_NAME_DIR的值会被用来初始化这个属性。

　　PROJECT_LABEL属性可以用来在IDE环境，比如visual studio，中改变目标的名字。 VS_KEYWORD可以用来改变visual studio的关键字，例如如果该选项被设置为Qt4VSv1.0的话，QT集成将会运行得更好。

　　VS_SCC_PROJECTNAME, VS_SCC_LOCALPATH, VS_SCC_PROVIDER可以被设置，从而增加在一个VS工程文件中对源码控制绑定的支持。

　　PRE_INSTALL_SCRIPT和POST_INSTALL_SCRIPT属性是在安装一个目标之前及之后指定运行CMake脚本的旧格式。只有当使用旧式的INSTALL_TARGETS来安装目标时，才能使用这两个属性。使用INSTALL命令代替这种用法。

　　EXCLUDE_FROM_DEFAULT_BUILD属性被visual studio生成器使用。如果属性值设置为1，那么当你选择"构建解决方案"时，目标将不会成为默认构建的一部分。


