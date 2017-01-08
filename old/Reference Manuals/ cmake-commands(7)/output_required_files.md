output_required_files
===

output_required_files 输出一个list，其中包含了一个给定源文件所需要的其他源文件。

  output_required_files(srcfile outputfile)
　　输出一个指定的源文件所需要的所有源文件的list。这个list会写到outputfile变量中。该命令的功能是将srcfile的依赖性写出到outputfile中，不过该命令将尽可能地跳过.h文件，搜索依赖中的.cxx，.c和.cpp文件。

