message
===

message 为用户显示一条消息。

  　　message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR]
          "message to display" ...)
　　可以用下述可选的关键字指定消息的类型：

　　  　　(无)         　 = 重要消息；
  　　　　STATUS         = 非重要消息；
  　　　　WARNING        = CMake 警告, 会继续执行；
  　　　　AUTHOR_WARNING = CMake 警告 (dev), 会继续执行；
  　　　　SEND_ERROR     = CMake 错误, 继续执行，但是会跳过生成的步骤；
  　　　　FATAL_ERROR    = CMake 错误, 终止所有处理过程；
　　CMake的命令行工具会在stdout上显示STATUS消息，在stderr上显示其他所有消息。CMake的GUI会在它的log区域显示所有消息。交互式的对话框（ccmake和CMakeSetup）将会在状态行上一次显示一条STATUS消息，而其他格式的消息会出现在交互式的弹出式对话框中。

　　CMake警告和错误消息的文本显示使用的是一种简单的标记语言。文本没有缩进，超过长度的行会回卷，段落之间以新行做为分隔符。

