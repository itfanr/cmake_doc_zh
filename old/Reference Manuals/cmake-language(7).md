cmake-language(7)
===

# cmake-language(7)

标签（空格分隔）： cmake

---

网址：https://cmake.org/cmake/help/v3.4/manual/cmake-language.7.html

##组织 

CMake的输入文件使用CMake 语言来写，是名为CMakeLists.txt的源文件或者以.cmake为扩展名的文件。

CMake语言源文件在一个项目中的组织形式是：

- 目录（CMakeLists.txt）
- 脚本（<script>.cmake),
- 模块(<module>.cmake)

### 目录
当CMake处理一个项目源码树时，入口点是在顶层源码目录的CMakeLists.txt文件。这个文件可能包含整个构件说明或者使用add_subdirectory()命令来增加子目录来构建。通过命令增加的每个子目录 必须也包含CMakeLists.txt文件作为到那个目录的入口点。对于每个CMakeLists.txt文件被处理的源码目录    来说，CMake在构建树中产生了一个对应的目录作为默认的工作和输出目录。

### 脚本
单一的<srcipt>.cmake源文件可能会在脚本模式被处理   ，通过使用带有-p选项的cmake(1)命令行工具 。  脚本模式简单运行在给定的CMake语言源文件中命令，而且不会生成一个构建系统。不允许定义目标或者动作的CMake命令。

### 模块
在目录或者脚本中的CMake语言代码   可能使用include命令来加载在包含的上下文的范围中<moudle>.cmake源文件。可以查看cmake-modules(7)手册页。项目源码树可能也提供他们自己的模块和在CMAKE_MOUDLE_PATH变量中指定它们的位置。

## 语法
###编码
CMake语言源文件可以7位的ASCII码编写，为了的  所有支持的平台上最大程度地便携。新的一行可能编码为ie\n或者\r\n，但是会被转换为\n，当输入文件被读取的时候。

注意实现是8位，所以源文件可能会被编码为UTF-8在平台上      ，使用支持这个编码的系统API。另外，CMake3.2和高版本 在windows平台以UTF-8编码的源文件。而且，CMake3.0和更高版本允许在源文件中先进的UTF-8 Byte-Order Mark编码。

### 源文件
CMake语言源文件 包含0或者 通过新的一行分隔的更多的命令触发和可选的空格个和注释：

```
file         ::=  file_element*
file_element ::=  command_invocation line_ending |
                  (bracket_comment|space)* line_ending
line_ending  ::=  line_comment? newline
space        ::=  <match '[ \t]+'>
newline      ::=  <match '\n'>
```
注意，不在命令参数或括号注释中的任意源文件行   可以在行注释结束。

### 命令触发
命令触发是一个名字，通过空白分隔的paren-enclosed参数命名：

```
command_invocation  ::=  space* identifier space* '(' arguments ')'
identifier          ::=  <match '[A-Za-z_][A-Za-z0-9_]*'>
arguments           ::=  argument? separated_arguments*
separated_arguments ::=  separation+ argument? |
                         separation* '(' arguments ')'
separation          ::=  space | line_ending
```
比如：
```
add_executable(hello world.c)
```

命令名字是大小写敏感的。在参数中的内部的未引用的括号  必须保持配对。每个给命令的`(`或者`）` 调用为 字面上的未引用参数。这可能会被在调用中使用 来让 `if()`命令来封闭条件。

```
if(FALSE AND (FALSE OR TRUE)) # evaluates to FALSE
```

>注意，在3.0之前的CMake版本 要求命令名字识别码为最小2个字符。

>在2.8.12之前的CMake版本在未引用的参数后面默默地接受未引用的参数或者引用的参数，不会被任何的空格分隔。CMake 2.8.12或者更高的版本接受这样的代码但是会输出一个警告。

###命令行参数
在Command Invocations里有三种类型的参数：

```
argument ::=  bracket_argument | quoted_argument | unquoted_argument
```

#### 括号参数
括号参数，被Lua语言的长括号语法激发的灵感，封闭了在打开和关闭相同长度的括号之间的内容：

```
bracket_argument ::=  bracket_open bracket_content bracket_close
bracket_open     ::=  '[' '='{len} '['
bracket_content  ::=  <any text not containing a bracket_close
                       of the same {len} as the bracket_open>
bracket_close    ::=  ']' '='{len} ']'
```

打开的长度>=0的括号  被写上 [ followed by len = followed by [ ，对应的close括号写上`]`followed by len = followed by ]。括号没有内部括号。唯一的长度可能常常被选择 为打开和关闭来包含关闭其他长度的括号。

空格参数内容由 在打开和关闭括号之间的所有的文字组成，除了那些 在打开的括号后面跟着的新的一行，如果存在，就会被忽略。没有封闭的内容的评估，比如 Escape Sequences 或者 Variable References被执行。常常给命令调用一个括号参数，作为它的精确的一个参数。

比如：
```
message([=[
This is the first line in a bracket argument with bracket length 1.
No \-escape sequences or ${variable} references are evaluated.
This is always one argument even though it contains a ; character.
The text does not end on a closing bracket of length 0 like ]].
It does end in a closing bracket of length 1.
]=])
```

>注意，在3.0之前的版本不支持括号参数。它们解析打开括号为一个未引用参数的开始。

#### 引用参数
未引用参数封闭了在打开和关闭括号的双引用字符：

```
quoted_argument     ::=  '"' quoted_element* '"'
quoted_element      ::=  <any character except '\' or '"'> |
                         escape_sequence |
                         quoted_continuation
quoted_continuation ::=  '\' newline
```

引用参数内容由所有的打开和关闭引用之间的文字组成。 Escape Sequences 和 Variable References 被评估。常常给命令调用一个引用参数，作为它的精确的一个参数。

比如：
```
message("This is a quoted argument containing multiple lines.
This is always one argument even though it contains a ; character.
Both \\-escape sequences and ${variable} references are evaluated.
The text does not end on an escaped double-quote like \".
It does end in an unescaped double quote.
")
```

以奇数的反斜杠结尾的每一行最终的`\` 被看做为一个行继续符，并被忽略，随着立即跟着的新的一行的字符。比如：
```
message("\
This is the first line of a quoted argument. \
In fact it is the only line but since it is long \
the source code uses line continuation.\
")
```

>注意，在3.0版本之前的CMake不支持用`\`符号继续。它们报告错误 在引用的参数中的错误，该参数 包含 以奇数的`\`字符结尾的行 。

#### 非引用参数
未引用的参数不会被任何引用语法封闭。它可能不包含任何空白字符、`(`、`)`、`#`、`"`或者`\`，除了被反斜杠避开：

```
unquoted_argument ::=  unquoted_element+ | unquoted_legacy
unquoted_element  ::=  <any character except whitespace or one of '()#"\'> |
                       escape_sequence
unquoted_legacy   ::=  <see note in text>
```

未引用的参数由允许或者避开的字符的继续块中的所有文字组成。 Escape Sequences和 Variable References 都被评估。The resulting value is divided in the same way Lists divide into elements。把每个非空的元素给了命令调用作为一个参数。因此一个未引用的参数可能给命令触发，作为0或者更多的参数。

比如：
```
foreach(arg
    NoSpace
    Escaped\ Space
    This;Divides;Into;Five;Arguments
    Escaped\;Semicolon
    )
  message("${arg}")
endforeach()
```

>注意：
为了支持CMake的遗留代码，未引用参数可能也包含双引用字符串（"..."，可能封闭水平空白符），make类型变量引用（$(MAKEVAR)）。未转义的双引号必须平衡，可能能够不会在未引用参数的开始出现，被当作内容的一部分。比如，未引用的参数`-Da="b c"`, `-Da=$(v)`和 `a" "b"c"d `每个被在字面上解析。

>上面的"unquoted_legacy"产生 代表这样的参数。我们不推荐在新代码中使用遗留的未引用参数。替代方法是，使用一个引用参数或者括号参数来代表内容。

###转义序列

转义序列是在一个字符后面的一个`\` ：

```
escape_sequence  ::=  escape_identity | escape_encoded | escape_semicolon
escape_identity  ::=  '\' <match '[^A-Za-z0-9;]'>
escape_encoded   ::=  '\t' | '\r' | '\n'
escape_semicolon ::=  '\;'
```

在非字母数字的字符后面的`\`会简单编码原义字符 ， 而不解析它作为语法。`\t`、 `\r` 或者`\n` 编码成一个tab、回车或者新一行字符。在任何变量引用的外面的`\;` 编码它自己，但是在未引用参数里使用来编码`;`  而不在参数值上面区分它们。 在变量引用内部的`\;` 编码`;`字符。（也可以看策略CMP0053文档，为历史方面的考虑）

### 变量引用

变量引用有`${variable_name}`，在引用参数内部或者未引用参数内评估。变量引用被变量的值替代，如果变量没有被设置，那么就会被空字符串替换。变量引用可以嵌套  ，在内部可以被评估，比如`${outer_${inner_variable}_variable}`。

字面变量引用可能由字符数字组成，字符`/_.+-`和转移序列。嵌套的引用可能被用来评估任意名字的变量。（可以看策略CMP0053文档，为历史方面的考虑）

变量章节描述了变量名字的范围和它们的值如何被设置。

### 注释
注释以`#`开头，该`#`不在括号参数内部、引用参数或者 被转义（带有`\`作为未引用参数的一部分）。 

#### 括号注释
在括号参数的后面紧跟着的`#`形成了括号注释，由整个括号包围而成：

```
bracket_comment ::=  '#' bracket_argument
```

比如：
```
#[[This is a bracket comment.
It runs until the close bracket.]]
message("First Argument\n" #[[Bracket Comment]] "Second Argument")
```

>注意，在3.0版本之前的CMake不支持括号注释。它们解析打开的`#`为一个行注释的开始。

#### 行注释

不是紧跟在括号参数的后面的`#`形成了行注释，一直到行的结束：

```
line_comment ::=  '#' <any text not starting in a bracket_argument
                       and not containing a newline>
```

比如：
```
# This is a line comment.
message("First Argument\n" # This is a line comment :)
        "Second Argument") # This is a line comment.
```

## 控制结构

### 条件块

`if()`/`elseif()`/`else()`/`endif()`命令划定了有条件执行代码的界限。

### 循环

` foreach()`/`endforeach()` 和`while()`/`endwhile()`命令划定了循环执行代码的界限。
在这样的代码块中break()命令可能被用来提前终止循环，continue()命令被用来马上开始下一次循环。

### 命令定义
` macro()`/`endmacro()`和 `function()`/`endfunction()`命令划定了被记录来为之后被作为命令触发的代码块的界限。
 
## 变量

变量是CMake语言中存储的基本单元。它们的值常常是字符串类型，尽管一些命令可能解析字符串为其他类型。set()和unset()命令显示地set或者unset一个变量，但是其他命令也有语义来修改变量的值。变量名是大小写敏感的，可能包含了任何文字，但是我们推荐只用字符`_`和`-`来连接名字。

变量有多种作用域。每个变量set或者unset创建了一个当前作用域的绑定：

- 函数作用域

被function()命令创建的命令定义创建的命令，这些命令处理 在一个新变量的绑定作用域中被记录的命令。变量set或者unset绑定这个作用域，而且变量被一个文档函数  和任意的嵌套命令使用，但是不在函数返回值后面。

- 目录作用域

在源码树中的每个目录有它自己的变量绑定。在为一个目录处理CMakeLists.txt文件之前，CMake拷贝在父目录中当前定义的所有的变量绑定，如果存在的，来初始化新的目录作用域。CMake脚本，当带有cmake -P处理的，绑定在一个目录作用域的变量。

不在函数调用中的变量set或者unset绑定到当前的目录作用域。

- 永久缓存

CMake存储一个分离的缓存变量的集合，该集合的变量持久存在  于在项目构建树中的多种运行环境。缓存条目有一个隔离的绑定作用域，该作用域只被显示的请求修改，比如被CACHE选项  set()和unset()命令。

当评估变量引用时，CMake首先搜索绑定在函数调用栈中，如果函数调用栈存在的话，然后返回到当前目录作用域的绑定，如果存在。如果一个set绑定被找到，它的值就会被使用。如果unset绑定被找到，或者没有绑定被找到，CMake然后搜索cache条目。如果缓存条目被找到，它的值被使用。否则，变量引用评估空字符串。

cmake-variables手册描述了很多CMake提供的变量   或者对CMake有意义的变量，当被工程代码set的时候。

##列表

尽管所有CMake中的值存储为字符串，但是在某些上下文字符串可能被当作列表 ，比如在未引用的参数期间。在这样的上下文，字符串被切分为列表元素  通过`;`切割了  不在[和]字符不相等的数量后面的字符串和不以`\`为前面的字符串。`\;`不会分开一个值但是被结果元素中的`;`替代。

元素的列表被字符串替代，该字符串  通过连接被`;`分开的元素组成。比如，set()命令存储了多种值到目标变量作为一个列表：

```
set(srcs a.c b.c c.c) # sets "srcs" to "a.c;b.c;c.c"
```

列表为简单使用场合而生， 比如源文件，不应该被用来复杂数据处理工作。多数构成列表的命令 不会转义列表元素中的`;`字符，这样展开为嵌套列表：

```
set(x a "b;c") # sets "x" to "a;b;c", not "a;b\;c"
```

