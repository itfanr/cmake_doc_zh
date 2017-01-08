load_command
===

load_command 将一条命令加载到一个运行中的CMake。

  　　load_command(COMMAND_NAME <loc1> [loc2 ...])
　　该命令将在给定的路径下查找名字为cmCOMMAND_NAME的一个库。如果找到了，它将会以模块的方式被加载，然后该命令将会被添加到可用的CMake命令集中。通常，TRY_COMPILE选项被用在这个命令之前来编译这个模块。如果该命令被成功加载，一个名为CMAKE_LOADED_COMMAND_<COMMAND_NAME>的变量将会被设置为这个加载模块的完整路径。否则，这个变量就不会被设置。

