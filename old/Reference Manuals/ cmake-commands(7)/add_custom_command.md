add_custom_command
===

add_custom_command 为生成的构建系统添加一条自定义的构建规则。
　　add_custom_command命令有两种主要的功能；第一种是为了生成输出文件，添加一条自定义命令。

  add_custom_command(OUTPUT output1 [output2 ...]
                     COMMAND command1 [ARGS] [args1...]
                     [COMMAND command2 [ARGS] [args2...] ...]
                     [MAIN_DEPENDENCY depend]
                     [DEPENDS [depends...]]
                     [IMPLICIT_DEPENDS <lang1> depend1 ...]
                     [WORKING_DIRECTORY dir]
                     [COMMENT comment] [VERBATIM] [APPEND])

　　这种命令格式定义了一条生成指定的文件（文件组）的生成命令。在相同路径下创建的目标（CMakeLists.txt文件）——任何自定义命令的输出都作为它的源文件——被设置了一条规则：在构建的时候，使用指定的命令来生成这些文件。如果一个输出文件名是相对路径，它将被解释成相对于构建树路径的相对路径，并且与当前源码路径是对应的。注意，MAIN_DEPENDENCY完全是可选的，它用来向visual studio建议在何处停止自定义命令。对于各种类型的makefile而言，这条命令创建了一个格式如下的新目标：

  OUTPUT: MAIN_DEPENDENCY DEPENDS
　　　　COMMAND
　　如果指定了多于一条的命令，它们会按顺序执行。ARGS参数是可选的，它的存在是为了保持向后兼容，以后会被忽略掉。

　　第二种格式为一个目标——比如一个库文件或者可执行文件——添加一条自定义命令。这种格式可以用于目标构建前或构建后的一些操作。这条命令会成为目标的一部分，并且只有目标被构建时才会执行。如果目标已经构建了，该目标将不会执行。

  add_custom_command(TARGET target
                     PRE_BUILD | PRE_LINK | POST_BUILD
                     COMMAND command1 [ARGS] [args1...]
                     [COMMAND command2 [ARGS] [args2...] ...]
                     [WORKING_DIRECTORY dir]
                     [COMMENT comment] [VERBATIM])
　　这条命令定义了一个与指定目标的构建过程相关的新命令。新命令在何时执行，由下述的选项决定：

  PRE_BUILD  - 在所有其它的依赖之前执行；
  PRE_LINK   - 在所有其它的依赖之后执行；
  POST_BUILD - 在目标被构建之后执行；
　　注意，只有Visual Studio 7或更高的版本才支持PRE_BUILD。对于其他的生成器，PRE_BUILD会被当做PRE_LINK来对待。

　　如果指定了WORKING_DIRECTORY选项，这条命令会在给定的路径下执行。如果设置了COMMENT选项，后跟的参数会在构建时、以构建信息的形式、在命令执行之前显示出来。如果指定了APPEND选项，COMMAND以及DEPENDS选项的值会附加到第一个输出文件的自定义命令上。在此之前，必须有一次以相同的输出文件作为参数的对该命令的调用。在当前版本下，如果指定了APPEND选项，COMMENT, WORKING_DIRECTORY和MAIN_DEPENDENCY选项会被忽略掉，不过未来有可能会用到。

　　如果指定了VERBATIM选项，所有该命令的参数将会合适地被转义，以便构建工具能够以原汁原味的参数去调用那些构建命令。注意，在add_custom_command能看到这些参数之前，CMake语言处理器会对这些参数做一层转义处理。推荐使用VERBATIM参数，因为它能够保证正确的行为。当VERBATIM未指定时，CMake的行为依赖于平台，因为CMake没有针对某一种工具的特殊字符采取保护措施。

　　如果自定义命令的输出并不是实际的磁盘文件，应该使用SET_SOURCE_FILES_PROPERTIES命令将该输出的属性标记为SYMBOLIC。

　　IMPLICIT_DEPENDS选项请求扫描一个输入文件的隐含依赖关系。给定的语言参数（文中的lang1—译注）指定了应该使用哪种编程语言的依赖扫描器。目前为止，仅支持C和CXX语言扫描器。扫描中发现的依赖文件将会在编译时添加到自定义命令中。注意，IMPLICIT_DEPENDS选项目前仅仅直至Makefile生成器，其它的生成器会忽略之。

　　如果COMMAND选项指定了一个可执行目标（由ADD_EXECUTABLE命令创建的目标），在构建时，它会自动被可执行文件的位置所替换。而且，一个目标级的依赖性将会被添加进去，这样这个可执行目标将会在所有依赖于该自定义命令的结果的目标之前被构建。不过，任何时候重编译这个可执行文件，这种特性并不会引入一个会引起自定义命令重新运行的文件级依赖。

　　DEPENDS选项指定了该命令依赖的文件。如果依赖的对象是同一目录（CMakeLists.txt文件）下另外一个自定义命令的输出，CMake会自动将其它自定义命令带到这个命令中来。如果DEPENDS指定了任何类型的目标（由ADD_*命令创建），一个目标级的依赖性将会被创建，以保证该目标在任何其它目标使用这个自定义命令的输出之前，该目标已经被创建了。而且，如果该目标是可执行文件或库文件，一个文件级依赖将会被创建，用来引发自定义命令在目标被重编译时的重新运行。

