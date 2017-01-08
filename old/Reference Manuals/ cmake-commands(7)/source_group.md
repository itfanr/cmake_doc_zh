source_group
===

source_group  为Makefile中的源文件定义一个分组。

  source_group(name [REGULAR_EXPRESSION regex] [FILES src1 src2 ...])
　　为工程中的源文件中定义一个分组。这主要用来在Visual Studio中建立文件组按钮(file tabs)。所有列出来的文件或者匹配正则表达式的文件都会被放到这个文件组中。如果一个文件匹配多个组，那么最后明确地列出这个文件的组将会包含这个文件，如果有这样的组的话。如果没有任何组明确地列出这个文件，那么最后那个其正则表达式与该文件名匹配的组，将会成为最终候选者。

　　组名中可以包含反斜杠，以指定子文件组：source_group(outer\\inner ...)

　　为了保持后向兼容性，这个命令也支持这种格式：source_group(name regex)

