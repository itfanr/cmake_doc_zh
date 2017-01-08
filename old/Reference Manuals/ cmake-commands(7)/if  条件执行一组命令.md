if  条件执行一组命令
===

if  条件执行一组命令。

  if(expression)
    # then section.
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  elseif(expression2)
    # elseif section.
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  else(expression)
    # else section.
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
  endif(expression)
　　评估给定的表达式。如果结果是true，在THEN段的命令就会被调用。否则，在ELSE区段的命令会被调用。ELSEIF和ELSE区段是可选的 。可以有多个ELSEIF子句。注意，在else和elseif子句中的表达式也是可选的。判断条件可以用长表达式，并且表达式有约定的优先级顺序。括号中的表达式会首先被调用；然后是一元运算符，比如EXISTS，COMMAND以及DEFINED；然后是EQUAL，LESS，GREATER，STRLESS，STRGREATER，STREQUAL，MATCHES；然后是NOT运算符，最后是AND，OR运算符。几种可能的表达式是：

  if(<常量>)
　　如果<常量>是1，ON，YES，TRUE，Y或者非0数值，那么表达式为真；如果<常量>是0，OFF，NO，FALSE，N，IGNORE，""，或者以'-NOTFOUND'为后缀，那么表达式为假。这些布尔常量值是大小写无关的。

  if(<变量>)
　　如果<变量>的值不是一个false常量，表达式为真。

  if(NOT <表达式>)
　　如果<表达式>的值是false的话，真个表达式为真。

  if(<表达式1> AND <表达式2>)
　　如果两个表达式都为真，整个表达式为真。

  if(<表达式1> OR <表达式2>)
　　只要有一个表达式为真，整个表达式为真。

  if(COMMAND command-name)
　　如果给出的名字是一个可以被调用的命令，宏，或者函数的话，整个表达式的值为真。

  if(POLICY policy-id)
　　如果给出的名字是一个已有的策略（格式是CMP<NNNN>），表达式为真。

  if(TARGET 目标名)
　　如果给出的名字是一个已有的构建目标或导入目标的话，表达式为真。

  if(EXISTS 文件名)

  if(EXISTS 路径名)
　　如果给出的文件名或路径名存在，表达式为真。该命令只对完整路径有效。

  if(file1 IS_NEWER_THAN file2)
　　如果file1比file2更新或者其中的一个文件不存在，那么表达式为真。该命令只对完整路径有效。

  if(IS_DIRECTORY directory-name)
　　如果给定的名字是一个路径，表达式返回真。该命令只对完整路径有效。

  if(IS_SYMLINK file-name)
　　如果给定的名字十一个符号链接的话，表达式返回真。该命令只对完整路径有效。

  if(IS_ABSOLUTE path)
　　如果给定的路径是一个绝对路径的话，表达式返回真。

  if(variable MATCHES regex)

  if(string MATCHES regex)
　　如果给定的字串或变量值域给定的正则表达式匹配的话，表达式返回真。

  if(variable LESS number)
  if(string LESS number)
  if(variable GREATER number)
  if(string GREATER number)
  if(variable EQUAL number)
  if(string EQUAL number)
　　如果给定的字串或变量值是一个有效的数字并且不等号或等号满足的话，表达式返回真。

  if(variable STRLESS string)
  if(string STRLESS string)
  if(variable STRGREATER string)
  if(string STRGREATER string)
  if(variable STREQUAL string)
  if(string STREQUAL string)
　　如果给定的字串或变量值依字典序小于（或者大于，或者等于）右边给出的字串或变量值的话，表达式返回真。

  if(version1 VERSION_LESS version2)
  if(version1 VERSION_EQUAL version2)
  if(version1 VERSION_GREATER version2)
　　对版本号的各部分依次比较（版本号格式是major[.minor[.patch[.tweak]]]）version1和version2的大小。

  if(DEFINED variable)
　　如果给定的变量被定义了的话，该表达式为真。如果变量被设置了，它的值是真是假都无所谓。

  if((expression) AND (expression OR (expression)))
　　在小括号内的表达式会首先被计算，然后才按照先前介绍的运算来计算。有内嵌的括号时，最里的括号会作为包含它们的表达式的计算过程的一部分。IF语句在CMake的历史上出现的相当早，它拥有一些需要特殊介绍的便捷特性。IF表达式只有在其中有一个单一的保留值的时候，才会精简操作（即不做变量展开——译注）；这些保留值包括：如果是大小写无关的 ON，1， YES，TRUE，Y，它返回真；如果是OFF，0，NO，FALSE，N，NOTFOUND，*-NOTFOUND，IGNORE，它返回假。这种特性非常合理，它为新作者提供了一种不需要精确匹配true或者false的便利性。这些值会被当做变量处理，即使它们没有使用${}语法的时候，也会被解引用。这意味着，如果你写下了这样的语句：

  if (boobah)
　　CMake将会把它当做你写了 

  if (${boobah})
　　来处理。类似地，如果你写了

  if (fubar AND sol)
　　CMake将会便捷地把它解释为 

  if ("${fubar}" AND "${sol}")
　　上述两例的后者确实是正确的书写方式，但是前者也是可行的。if语句中只有某些操作有这种特殊的变量处理方式。这些特殊的语句包括：

对于MATCHES运算符，待匹配的左边的参数首先被检查，用来确认它是否是一个已经定义的变量；如果是，该变量的值会被使用，否则就会用它的原始值。
如果MATCHES运算符没有左边的参数，它返回false，但不产生错误。 
LESS，GREATER，EQUAL运算符的左边的参数和右边的参数会被独立测试，用来确认它们是否是被定义的变量；如果是，使用它们被定义的值，否则使用它们的原始值。
STRLESS，STRGREATER，STREQUAL运算符的左边的参数和右边的参数会被独立测试，用来确认它们是否是被定义的变量；如果是，使用它们被定义的值，否则使用它们的原始值。
VERSIONLESS，VERSIONGREATER，VERSIONEQUAL运算符的左边的参数和右边的参数会被独立测试，用来确认它们是否是被定义的变量；如果是，使用它们被定义的值，否则使用它们的原始值。
NOT运算符右边的参数会被测试用来确定它是否是布尔常量，如果是，就用这个常量；否则它会被当做一个变量然后被解引用。
AND和OR运算符的左边的参数和右边的参数会被独立测试，用来确认它们是否是布尔常量；如果是，就用这个常量，否则它们会被当做变量然后被解引用。


