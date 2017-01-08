string
===

string  字符串操作函数。

  string(REGEX MATCH <regular_expression> <output variable> <input> [<input>...])
  string(REGEX MATCHALL <regular_expression> <output variable> <input> [<input>...])
  string(REGEX REPLACE <regular_expression> <replace_expression> <output variable> <input> [<input>...])
  string(REPLACE <match_string> <replace_string> <output variable> <input> [<input>...])
  string(COMPARE EQUAL <string1> <string2> <output variable>)
  string(COMPARE NOTEQUAL <string1> <string2> <output variable>)
  string(COMPARE LESS <string1> <string2> <output variable>)
  string(COMPARE GREATER <string1> <string2> <output variable>)
  string(ASCII <number> [<number> ...] <output variable>)
  string(CONFIGURE <string1> <output variable> [@ONLY] [ESCAPE_QUOTES])
  string(TOUPPER <string1> <output variable>)
  string(TOLOWER <string1> <output variable>)
  string(LENGTH <string> <output variable>)
  string(SUBSTRING <string> <begin> <length> <output variable>)
  string(STRIP <string> <output variable>)
  string(RANDOM [LENGTH <length>] [ALPHABET <alphabet>] [RANDOM_SEED <seed>] <output variable>)
　　REGEX MATCH : 匹配正则表达式一次，然后将匹配的值存储到输出变量中。

　　REGEX MATCHALL : 尽可能多次地匹配正则表达式，然后将匹配的值以list的形势存储到输出变量中。

　　REGEX REPLACE : 尽可能多次地匹配正则表达式，并且将匹配的值用replacement expression 替换掉，然后存储到输出变量中。这个replace expression 可以引用包含匹配字符串的子表达式，这些匹配的字符串用圆括号隔开的\1，\2，...，\9等加以引用。注意：在CMake代码里，如果要使用一个反斜杠，必须要用两个反斜杠(\\1)转义，才能通过参数解析。

　　REPLACE : 将输入字符串内所有出现match_string的地方都用replace_string代替，然后将结果存储到输出变量中。

　　COMPARE EQUAL/NOTEQUAL/LESS/GREATER : 将会比较两个字符串，然后将比较的结果（true/false）存储到输出变量中。

　　ASCII : 将会把所有数字转换为对应的ASCII字符。

　　CONFIGURE : 将一个字符串进行变换，这种变换与将一个FILE变换为CONFIGURE_FILE相似。

　　TOUPPER/TOLOWER : 将字符串转换为大写/小写字符。

　　LENGTH : 返回给定字符串的长度。

　　SUBSTRING : 返回给定字符串的子串。

　　STRIP : 返回一个给定字符串的子串，它会去掉原先字符串开始和结尾的空格。

　　RANDOM : 将会返回一个给定长度的随机字符串，它由给定的字母表中的字母组成。默认的长度是5个字符，默认的字母表是全部的大小写字母以及数字。如果指定了一个整数RANDOM_SEED，它的值将会被用做随机数发生器的种子。

　　在正则表达式中，下述字符有特殊含义：

   ^         在行首匹配。
   $         在行尾匹配。
   .         匹配任意单个字符。
   [ ]       匹配在中括号中的任意字符。
   [^ ]      匹配不在中括号中的任意字符。
    -        匹配任意在短横线两端字符闭区间中间的任意一个字符。
   *         匹配先前模式零次或多次。
   +         匹配先前模式一次或多次。
   ?         匹配先前模式零次或一次。
   |         匹配|两侧的任意一种模式。
   ()        保存一个匹配的子表达式，这个子表达式后续可以在REGEX REPLACE操作中以\n的方式引用。 它也会被所有正则表达式相关的命令所保存；包括，比如，
　　　　　　　 如果用到if( MATCHES )命令的话，这些匹配的值被保存在变量CMAKE_MATCH_(0..9)中。

