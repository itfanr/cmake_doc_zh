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
任何自定义命令的输出都作为它的源文件——被设置了一条规则：在构建的时候，使用指定的命令来生成这些文件。不要在多于一个独立的目录列出output，因为可能被并行构建或者两个规则的实例可能冲突（你可以用add_custom_target()命令来替换，使用它可以使其他的目标独立于另外一个）。在makefile术语中，这个以如下的形式创建了一个新的目标：

```
OUTPUT: MAIN_DEPENDENCY DEPENDS
        COMMAND
```

选项如下：

### APPEND



 自定义命令。
对于各种类型的makefile而言，这条命令创建了一个格式如下的新目标：


