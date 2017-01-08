add_custom_target
===

add_custom_target 

添加一个目标，它没有输出；这样它就总是会被构建。

```
add_custom_target(Name [ALL] [command1 [args1...]]
                    [COMMAND command2 [args2...] ...]
                    [DEPENDS depend depend depend ... ]
                    [WORKING_DIRECTORY dir]
                    [COMMENT comment] [VERBATIM]
                    [SOURCES src1 [src2...]])
```

用Name选项给定的名字添加一个目标，这个目标会引发给定的那些命令。这个目标没有输出文件，并且总是被认为是过时的，即使那些命令试图去创建一个与该目标同名的文件。使用ADD_CUSTOM_COMMAND命令可以生成一个带有依赖性的文件。默认情况下，没有目标会依赖于自定义目标。使用ADD_DEPENDENCIES命令可以添加依赖于该目标或者被该目标依赖的目标。如果指定了ALL选项，这表明这个目标应该被添加到默认的构建目标中，这样它每次都会被构建（命令的名字不能是ALL）。命令和选项是可选的；如果它们没有被指定，将会产生一个空目标。如果设定了WORKING_DIRECTORY参数，该命令会在它指定的路径下执行。如果指定了COMMENT选项，后跟的参数将会在构件的时候，在命令执行之前，被显示出来。DEPENDS选项后面列出来的依赖目标可以引用add_custom_command命令在相同路径下（CMakeLists.txt）生成的输出和文件。

如果指定了VERBATIM选项，所有传递到该命令的选项将会被合适地转义；这样，该命令调用的构建工具会接收到未经改变的参数。注意，CMake语言处理器会在add_custom_target命令在看到这些参数之前对它们进行一层转义。推荐使用该参数，因为它保证了正确的行为。当未指定该参数时，转义的行为依赖于平台，因为CMake没有针对于特定工具中特殊字符的保护措施。

SOURCES选项指定了会被包含到自定义目标中的附加的源文件。指定的源文件将会被添加到IDE的工程文件中，方便在没有构建规则的情况下能够编辑。

