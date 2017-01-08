file
===

 file  文件操作命令
  file(WRITE filename "message to write"... )
  file(APPEND filename "message to write"... )
  file(READ filename variable [LIMIT numBytes] [OFFSET offset] [HEX])
  file(STRINGS filename variable [LIMIT_COUNT num]
       [LIMIT_INPUT numBytes] [LIMIT_OUTPUT numBytes]
       [LENGTH_MINIMUM numBytes] [LENGTH_MAXIMUM numBytes]
       [NEWLINE_CONSUME] [REGEX regex]
       [NO_HEX_CONVERSION])
  file(GLOB variable [RELATIVE path] [globbing expressions]...)
  file(GLOB_RECURSE variable [RELATIVE path] 
       [FOLLOW_SYMLINKS] [globbing expressions]...)
  file(RENAME <oldname> <newname>)
  file(REMOVE [file1 ...])
  file(REMOVE_RECURSE [file1 ...])
  file(MAKE_DIRECTORY [directory1 directory2 ...])
  file(RELATIVE_PATH variable directory file)
  file(TO_CMAKE_PATH path result)
  file(TO_NATIVE_PATH path result)
  file(DOWNLOAD url file [TIMEOUT timeout] [STATUS status] [LOG log]
       [EXPECTED_MD5 sum] [SHOW_PROGRESS])
　　WRITE选项将会写一条消息到名为filename的文件中。如果文件已经存在，该命令会覆盖已有的文件；如果文件不存在，它将创建该文件。

　　APPEND选项和WRITE选项一样，将会写一条消息到名为filename的文件中，只是该消息会附加到文件末尾。

　　READ选项将会读一个文件中的内容并将其存储在变量里。读文件的位置从offset开始，最多读numBytes个字节。如果指定了HEX参数，二进制代码将会转换为十六进制表达方式，并存储在变量里。

　　STRINGS将会从一个文件中将一个ASCII字符串的list解析出来，然后存储在variable变量中。文件中的二进制数据会被忽略。回车换行符会被忽略。它也可以用在Intel的Hex和Motorola的S-记录文件；读取它们时，它们会被自动转换为二进制格式。可以使用NO_HEX_CONVERSION选项禁止这项功能。LIMIT_COUNT选项设定了返回的字符串的最大数量。LIMIT_INPUT设置了从输入文件中读取的最大字节数。LIMIT_OUTPUT设置了在输出变量中存储的最大字节数。LENGTH_MINIMUM设置了要返回的字符串的最小长度；小于该长度的字符串会被忽略。LENGTH_MAXIMUM设置了返回字符串的最大长度；更长的字符串会被分割成不长于最大长度的字符串。NEWLINE_CONSUME选项允许新行被包含到字符串中，而不是终止它们。REGEX选项指定了一个待返回的字符串必须满足的正则表达式。典型的使用方式是：

  file(STRINGS myfile.txt myfile)
该命令在变量myfile中存储了一个list，该list中每个项是输入文件中的一行文本。
　　GLOB选项将会为所有匹配查询表达式的文件生成一个文件list，并将该list存储进变量variable里。文件名查询表达式与正则表达式类似，只不过更加简单。如果为一个表达式指定了RELATIVE标志，返回的结果将会是相对于给定路径的相对路径。文件名查询表达式的例子有：

   *.cxx      - 匹配所有扩展名为cxx的文件。
   *.vt?      - 匹配所有扩展名是vta,...,vtz的文件。
   f[3-5].txt - 匹配文件f3.txt, f4.txt, f5.txt。
　　GLOB_RECURSE选项将会生成一个类似于通常的GLOB选项的list，只是它会寻访所有那些匹配目录的子路径并同时匹配查询表达式的文件。作为符号链接的子路径只有在给定FOLLOW_SYMLINKS选项或者cmake策略CMP0009被设置为NEW时，才会被寻访到。参见cmake --help-policy CMP0009 查询跟多有用的信息。

使用递归查询的例子有：

 /dir/*.py  - 匹配所有在/dir及其子目录下的python文件。
　　MAKE_DIRECTORY选项将会创建指定的目录，如果它们的父目录不存在时，同样也会创建。（类似于mkdir命令——译注）

　　RENAME选项对同一个文件系统下的一个文件或目录重命名。（类似于mv命令——译注）

　　REMOVE选项将会删除指定的文件，包括在子路径下的文件。（类似于rm命令——译注）

　　REMOVE_RECURSE选项会删除给定的文件以及目录，包括非空目录。（类似于rm -r 命令——译注）

　　RELATIVE_PATH选项会确定从direcroty参数到指定文件的相对路径。

　　TO_CMAKE_PATH选项会把path转换为一个以unix的 / 开头的cmake风格的路径。输入可以是一个单一的路径，也可以是一个系统路径，比如"$ENV{PATH}"。注意，在调用TO_CMAKE_PATH的ENV周围的双引号只能有一个参数(Note the double quotes around the ENV call TO_CMAKE_PATH only takes one argument. 原文如此。quotes和后面的takes让人后纠结，这句话翻译可能有误。欢迎指正——译注)。

　　TO_NATIVE_PATH选项与TO_CMAKE_PATH选项很相似，但是它会把cmake风格的路径转换为本地路径风格：windows下用\，而unix下用/。

　　DOWNLOAD 将给定的URL下载到指定的文件中。如果指定了LOG var选项，下载日志将会被输出到var中。如果指定了STATUS var选项，下载操作的状态会被输出到var中。该状态返回值是一个长度为2的list。list的第一个元素是操作的数字返回值，第二个返回值是错误的字符串值。错误信息如果是数字0，操作中没有发生错误。如果指定了TIMEOUT time选项，在time秒之后，操作会超时退出；time应该是整数。如果指定了EXPECTED_MD5 sum选项，下载操作会认证下载的文件的实际MD5和是否与期望值匹配。如果不匹配，操作将返回一个错误。如果指定了SHOW_PROGRESS选项，进度信息会以状态信息的形式被打印出来，直到操作完成。

　　file命令还提供了COPY和INSTALL两种格式：

  file(<COPY|INSTALL> files... DESTINATION <dir>
       [FILE_PERMISSIONS permissions...]
       [DIRECTORY_PERMISSIONS permissions...]
       [NO_SOURCE_PERMISSIONS] [USE_SOURCE_PERMISSIONS]
       [FILES_MATCHING]
       [[PATTERN <pattern> | REGEX <regex>]
        [EXCLUDE] [PERMISSIONS permissions...]] [...])
　　COPY版本把文件、目录以及符号连接拷贝到一个目标文件夹。相对输入路径的评估是基于当前的源代码目录进行的，相对目标路径的评估是基于当前的构建目录进行的。复制过程将保留输入文件的时间戳；并且如果目标路径处存在同名同时间戳的文件，复制命令会把它优化掉。赋值过程将保留输入文件的访问权限，除非显式指定权限或指定NO_SOURCE_PERMISSIONS选项（默认是USE_SOURCE_PERMISSIONS）。参见install(DIRECTORY)命令中关于权限（permissions），PATTERN，REGEX和EXCLUDE选项的文档。

　　INSTALL版本与COPY版本只有十分微小的差别：它会打印状态信息，并且默认使用NO_SOURCE_PERMISSIONS选项。install命令生成的安装脚本使用这个版本（它会使用一些没有在文档中涉及的内部使用的选项。）

参考：
http://www.cnblogs.com/coderfenghc/archive/2012/07/08/2581734.html

