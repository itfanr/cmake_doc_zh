separate_arguments
===

separate_arguments 将空格分隔的参数解析为一个分号分隔的list。

  separate_arguments(<var> <UNIX|WINDOWS>_COMMAND "<args>")
　　解析一个unix或者windows风格的命令行字符串"<args>"，并将结果以分号分隔的list的形式存储到<var>中。整个命令行都必须从这个"<args>"参数中给出。

　　UNIX_COMMAND模式以没有被括起来的白字符为参数的分隔符。它可以识别单引号和双引号的引号对。反斜杠可以对下一个字符的字面值转义（\"，就是"）;没有其他特殊的转义字符（例如\n就是n）。

　　WINDOWS_COMMAND模式按照与运行时库相同的语法解析一个windows命令行，在启动(starrtup)时构造argv。它使用没有被双引号括起来的白字符来分隔参数。反斜杠维持其字面含义，除非它们在双引号之前。更多细节，参见MSDN的文章："Parsing C Command-Line Arguments"。

  separate_arguments(VARIABLE)
　　将VARIABLE的值转换为一个分号分隔的list。所有的空格会被替换为';'。该命令可以用来辅助生成命令行。

