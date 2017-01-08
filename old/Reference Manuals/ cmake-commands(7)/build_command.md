build_command
===

build_command  获取构建该工程的命令行。

  build_command(<variable>
                [CONFIGURATION <config>]
                [PROJECT_NAME <projname>]
                [TARGET <target>])
　　把给定的变量<variable>设置成一个字符串，其中包含使用由变量CMAKE_GENERATOR确定的项目构建工具，去构建某一个工程的某一个目标配置的命令行。

　　对于多配置生成器，如果忽略CONFIGURATION选项，CMake将会选择一个合理的默认值；而对于单配置生成器，该选项会被忽略。

　　如果PROJECT_NAME选项被忽略，得到的命令行用来构建当前构建树上的顶层工程。

　　如果TARGET选项被忽略，得到的命令行可以用来构建所有目标，比较高效的用法是构建目标all或者ALL_BUILD。

  build_command(<cachevariable> <makecommand>)
　　不推荐使用以上的这种格式，但对于后相兼容还是有用的。只要可以，就要使用第一种格式。

　　这种格式将变量<cachevariable>设置为一个字符串，其中包含从构建树的根目录，用<makecommand>指定的构建工具构建这个工程的命令。<makecommand>应该是指向msdev，devenv，nmake，make或者是一种最终用户指定的构建工具的完整路径。


