add_subdirectory
===

add_subdirectory 为构建添加一个子路径。

  add_subdirectory(source_dir [binary_dir] 
                   [EXCLUDE_FROM_ALL])
　　这条命令的作用是为构建添加一个子路径。source_dir选项指定了CMakeLists.txt源文件和代码文件的位置。如果source_dir是一个相对路径，那么source_dir选项会被解释为相对于当前的目录，但是它也可以是一个绝对路径。binary_dir选项指定了输出文件的路径。如果binary_dir是相对路径，它将会被解释为相对于当前输出路径，但是它也可以是一个绝对路径。如果没有指定binary_dir，binary_dir的值将会是没有做任何相对路径展开的source_dir，这也是通常的用法。在source_dir指定路径下的CMakeLists.txt将会在当前输入文件的处理过程执行到该命令之前，立即被CMake处理。

　　如果指定了EXCLUDE_FROM_ALL选项，在子路径下的目标默认不会被包含到父路径的ALL目标里，并且也会被排除在IDE工程文件之外。用户必须显式构建在子路径下的目标，比如一些示范性的例子工程就是这样。典型地，子路径应该包含它自己的project()命令调用，这样会在子路径下产生一份完整的构建系统（比如VS IDE的solution文件）。注意，目标间的依赖性要高于这种排除行为。如果一个被父工程构建的目标依赖于在这个子路径下的目标，被依赖的目标会被包含到父工程的构建系统中，以满足依赖性的要求。

