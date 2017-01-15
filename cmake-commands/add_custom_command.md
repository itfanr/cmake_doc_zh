为生成的构建系统添加一条自定义的构建规则。

add_custom_command命令有两种主要的功能。

## 产生文件

第一种是为了生成输出文件，添加一条自定义命令。

```
add_custom_command(OUTPUT output1 [output2 ...]
                   COMMAND command1 [ARGS] [args1...]
                   [COMMAND command2 [ARGS] [args2...] ...]
                   [MAIN_DEPENDENCY depend]
                   [DEPENDS [depends...]]
                   [BYPRODUCTS [files...]]
                   [IMPLICIT_DEPENDS <lang1> depend1
                                    [<lang2> depend2] ...]
                   [WORKING_DIRECTORY dir]
                   [COMMENT comment]
                   [DEPFILE depfile]
                   [VERBATIM] [APPEND] [USES_TERMINAL])
```

这种命令格式定义了一条生成指定的文件（文件组）的命令。在相同路径下创建的目标（CMakeLists.txt文件）——
任何自定义命令的输出都作为它的源文件——被设置了一条规则：在构建的时候，使用指定的命令来生成这些文件。不要在多于一个独立的目录列出output，因为可能被并行构建或者两个规则的实例可能冲突（你可以用add_custom_target()命令来替换，使用它可以使其他的目标独立于另外一个）。在makefile术语中，这个命令以如下的形式创建了一个新的目标：

```
OUTPUT: MAIN_DEPENDENCY DEPENDS
        COMMAND
```

选项如下：

### APPEND

把`COMMAND`和`DEPENDS`选项的值添加到自定义的命令中——该命令赋予指定的第一个输出的。必须在先前调用过可以产生同样输出的这个命令。当APPEND已经给定时，`COMMENT`、`MAIN_DEPENDENCY` 和` WORKING_DIRECTORY `选项当前被忽略，但是在以后可能会被使用。

###BYPRODUCTS

该选项可以指定一些文件——期望命令可以产生但是，修改时间可能或者可能不比依赖新一些。如果一个byproduct名字是一个相对路径，它会被解释为相对于tree目录的路径—该目录随着当前源码目录而改变。每个byproduct文件会被自动地标记上`GENERATED `源文件属性。

显示地指定byproduct是可以被`Ninja`生成器支持的，来告诉ninja构建工具如何重新产生丢失了的byproduct。当其他的构建规则（比如自定义命令）依赖于byproduct的时候，这也是有用的。 Ninja需要一个构建规则给任何产生的文件（另外一个规则依赖于这些文件），虽然有带有排序的依赖来保证在他们的依赖构建之前byproduct是可用的。

`BYPRODUCTS `选项会被忽略在非Ninja的产出器平台，除了用来标记byproducts 的`GENERATED `状态。

###COMMAND

指定一些在构建阶段执行命令行。如果指定了多于一条的命令，它们会按顺序执行，但是没必要把它们组合到一个有状态的shell或者批处理文件中。（如果要运行一个全脚本，使用`configure_file()`或者file(GENERATE)命令来创建，然后指定一个COMMAND来启动它。）ARGS参数是可选的，它的存在是为了保持向后兼容，以后会被忽略掉。

如果`COMMAND `指定了一个可执行目标的名字（被add_executable()命令创建），它会自动被在构建阶段创建的可执行文件的路径替换。如果设置了，那么`CROSSCOMPILING_EMULATOR `可执行目标属性会提前天津到command上，来运行可执行文件可以在主机上执行。（在稍后的命令行上使用`TARGET_FILE` 生成表达式来指向一个可执行文件）。额外的目标层次的依赖会增加，这样可执行目标会在使用这个命令的目标之前构建好。但是，这不会增加一个文件级别的依赖，这个依赖会导致无论可执行文件是否重新编译，自定义命令都会重新运行。

传给COMMAND的参数，可以使用生成表达式产生。在生成表达式中指向目标名字的潜在指的是目标层次的依赖，而不是文件层次的依赖。使用`DEPENDS `列出目标名字，然后增加到文件层次的依赖。

### COMMENT